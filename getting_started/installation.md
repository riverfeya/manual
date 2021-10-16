## Установка

Deno работает на macOS, Linux, и Windows. Deno это единственный двоичный
запускаемый файл. Он не имеет внешних зависимостей.

На macOS, и на M1 (arm64) и на Intel (x64) выполнение доступно. На Linux и
Windows, поддерживается только x64.

### Загрузка и установка

[deno_install](https://github.com/denoland/deno_install) предоставляет удобные
скрипты для загрузки и установки двоичного файла.

Используя shell (macOS и Linux):

```shell
curl -fsSL https://deno.land/x/install/install.sh | sh
```

Используя PowerShell (Windows):

```shell
iwr https://deno.land/x/install/install.ps1 -useb | iex
```

Используя [Scoop](https://scoop.sh/) (Windows):

```shell
scoop install deno
```

Используя [Chocolatey](https://chocolatey.org/packages/deno) (Windows):

```shell
choco install deno
```

Используя [Homebrew](https://formulae.brew.sh/formula/deno) (macOS):

```shell
brew install deno
```

Используя [Nix](https://nixos.org/download.html) (macOS and Linux):

```shell
nix-shell -p deno
```

Сборка и установка из исходников [Cargo](https://crates.io/crates/deno):

```shell
cargo install deno --locked
```

Deno в виде двоичного файла можно установить и вручную, загрузив zip файл из
[github.com/denoland/deno/releases](https://github.com/denoland/deno/releases).
Этот пакет содержит только исполняемый файл. Вам нужно будет установить
исполняемый бит в macOS и Linux.

### Docker

For more information and instructions on the official Docker images:
[https://github.com/denoland/deno_docker](https://github.com/denoland/deno_docker)

### Testing your installation

To test your installation, run `deno --version`. If this prints the Deno version
to the console the installation was successful.

Use `deno help` to see help text documenting Deno's flags and usage. Get a
detailed guide on the CLI [here](./command_line_interface.md).

### Обновление

Чтобы обновить ранее установленную версию Deno, вы можете запустить:

```shell
deno upgrade
```

Это приведет к загрузке последней версии из
[github.com/denoland/deno/releases](https://github.com/denoland/deno/releases),
разархивирует его и заменит им текущий исполняемый файл.

Вы также можете использовать эту утилиту для установки определенной версии Deno:

```shell
deno upgrade --version 1.0.1
```

### Сборка из исходников

Информацию о том, как собрать из исходников, можно найти в
[`Contributing`](../contributing/building_from_source.md) .
