## Первые шаги

Эта страница содержит несколько примеров, чтобы научить вас основам Deno.

В этом документе предполагается, что вы уже знакомы с JavaScript, особенно с
async / await. Если у вас нет предварительных знаний о JavaScript, вы можете
следовать
[руководству по основам JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript)
прежде чем пытаться начать с Deno.

### Hello World

Deno - это среда выполнения для JavaScript / TypeScript, которая пытается быть
веб-совместимой и по возможности использует современные функции.

Совместимость браузера означает, что программа Hello World в Deno такая же, как
та, которую вы можете запустить в браузере:

```ts
console.log("Welcome to Deno!");
```

Попробуйте программу:

```shell
deno run https://deno.land/std@$STD_VERSION/examples/welcome.ts
```

### Создание HTTP запроса

Многие программы используют HTTP-запросы для получения данных с веб-сервера.
Напишем небольшую программу, которая извлекает файл и выводит его содержимое на
терминал.

Как и в браузере, вы можете использовать стандартный веб-интерфейс
[`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API для
выполнения HTTP вызовов:

```ts
const url = Deno.args[0];
const res = await fetch(url);

const body = new Uint8Array(await res.arrayBuffer());
await Deno.stdout.write(body);
```

Давайте рассмотрим, что делает это приложение:

1. Мы получаем первый аргумент, переданный приложению, и сохраняем его в
   константе url.
2. Мы делаем запрос по указанному URL, ожидаем ответа и сохраняем его в
   константе res.
3. Мы парсим тело ответа как
   [`ArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer),
   ожидаем ответа, и конвертируем его в
   [`Uint8Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
   для сохранения в константе `body`.
4. Мы записываем содержимое константы `body` в `stdout`.

Попробуйте:

```shell
deno run https://deno.land/std@$STD_VERSION/examples/curl.ts https://example.com
```

Вы увидите, что эта программа возвращает ошибку, касающуюся доступа к сети, так
что же мы сделали не так? Возможно, вы помните из введения, что Deno - это среда
выполнения, которая по умолчанию безопасна. Это означает, что вам необходимо
явно предоставить программам разрешение на выполнение определенных
«привилегированных» действий, таких как доступ к сети.

Попробуйте еще раз с правильным флагом разрешения:

```shell
deno run --allow-net=example.com https://deno.land/std@$STD_VERSION/examples/curl.ts https://example.com
```

### Чтение файла

Deno также предоставляет APIs которые не пришли из web. Все они содержатся в
`Deno` global. Вы можете найти документацию по этим APIs на
[doc.deno.land](https://doc.deno.land/builtin/stable#Deno).

Например Filesystem APIs нет в стандартной форме web, так что Deno предоставляет
свой сосбтвенный API.

В этой программе предполагается, что каждый аргумент командной строки является
именем файла, файл открывается и выводится на стандартный вывод.

```ts
import { copy } from "https://deno.land/std@$STD_VERSION/io/util.ts";
const filenames = Deno.args;
for (const filename of filenames) {
  const file = await Deno.open(filename);
  await copy(file, Deno.stdout);
  file.close();
}
```

Функция `copy()` здесь фактически делает не больше, чем kernel→userspace→kernel
copies. То есть та же самая память, из которой считываются данные из файла,
записывается в stdout. Это иллюстрирует общую цель проектирования потоков
ввода-вывода в Deno.

Попробуйте программу:

```shell
# macOS / Linux
deno run --allow-read https://deno.land/std@$STD_VERSION/examples/cat.ts /etc/hosts

# Windows
deno run --allow-read https://deno.land/std@$STD_VERSION/examples/cat.ts "C:\Windows\System32\Drivers\etc\hosts"
```

### TCP server

Это пример сервера, который принимает соединения через порт 8080 и возвращает
клиенту все, что отправляет.

```ts
import { copy } from "https://deno.land/std@$STD_VERSION/io/util.ts";
const hostname = "0.0.0.0";
const port = 8080;
const listener = Deno.listen({ hostname, port });
console.log(`Listening on ${hostname}:${port}`);
for await (const conn of listener) {
  copy(conn, conn);
}
```

По соображениям безопасности Deno не позволяет программам получать доступ к сети
без явного разрешения. Чтобы разрешить доступ к сети, используйте флаг командной
строки:

```shell
deno run --allow-net https://deno.land/std@$STD_VERSION/examples/echo_server.ts
```

Чтобы проверить это, попробуйте отправить ему данные с помощью netcat (или
telnet в Windows):

> Note for Windows users: netcat не доступен в Windows. Вместо него вы можете
> использовать встроенный telnet client. Telnet client по умолчанию отключен в
> Windows. Но включить его просто:
> [on Microsoft TechNet](https://social.technet.microsoft.com/wiki/contents/articles/38433.windows-10-enabling-telnet-client.aspx)

```shell
# Note for Windows users: замените `nc` на `telnet`
$ nc localhost 8080
hello world
hello world
```

Как и в примере с cat.ts, функция copy () здесь также не делает ненужных копий
памяти. Она получает пакет от ядра и отправляет его обратно без дополнительных
сложностей.

### Еще примеры

Больше примеров, таких как HTTP file server, есть в главе `Examples`.
