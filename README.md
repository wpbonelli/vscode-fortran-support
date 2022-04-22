![alt](assets/modern-fortran-logo.png)

[![GitHub Actions](https://github.com/fortran-lang/vscode-fortran-support/actions/workflows/main.yaml/badge.svg)](https://github.com/fortran-lang/vscode-fortran-support/actions)
[![Downloads](https://vsmarketplacebadge.apphb.com/downloads-short/krvajalm.linter-gfortran.svg)](https://marketplace.visualstudio.com/items?itemName=krvajalm.linter-gfortran)
[![Installs](https://vsmarketplacebadge.apphb.com/installs/krvajalm.linter-gfortran.svg)](https://marketplace.visualstudio.com/items?itemName=krvajalm.linter-gfortran)
[![VS Marketplace](https://vsmarketplacebadge.apphb.com/version-short/krvajalm.linter-gfortran.svg)](https://marketplace.visualstudio.com/items?itemName=krvajalm.linter-gfortran)
[![MIT License](https://img.shields.io/npm/l/stack-overflow-copy-paste.svg?)](http://opensource.org/licenses/MIT)

![alt](assets/intro-demo.gif)

## Key Features

- Syntax highlighting (Free and Fixed forms)
- Hover support, Signature help and Auto-completion
- GoTo/Peek implementation and Find/Peek references
- Project-wide and Document symbol detection and Renaming
- Native Language Server integration with [`fortls`](https://github.com/gnikit/fortls)
- Linting support for `gfortran`, `flang` and `ifort`
- Debugger [C/C++ extension](https://github.com/Microsoft/vscode-cpptools)
- Formatting with [findent](https://github.com/gnikit/findent-pypi) or [fprettify](https://github.com/pseewald/fprettify)
- Code snippets (more can be defined by the user [see](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_create-your-own-snippets))

## Language Server integration

The Fortran Language Server [`fortls`](https://github.com/gnikit/fortls) is responsible
for providing a lot of the higher level, IDE functionality. By default,
`Modern Fortran` will attempt to use it for hover, autocompletion, symbols and Go to & Peeking into definitions.

Allow for `fortls` to be automatically installed with `pip` or `Anaconda` and
if the location where `fortls` is installed is not in your `PATH` point VS Code
to the `fortls` location by setting

```json
{
  "fortran.fortls.path": "/custom/path/to/fortls"
}
```

For more about the Language Server's capabilities please refer to the
[documentation](https://gnikit.github.io/fortls/) of `fortls`.

## Linting

Linting allows for compiler error and warning detection while coding
without the user having to compile.

Using an invalid if expression

![alt](assets/lint-demo.gif)

Using incorrect type and rank as function argument

![alt](assets/lint-demo2.gif)

| :memo: Note                                |
| ------------------------------------------ |
| Save your file to generate linting results |

Linting results can be improved by providing additional options to the compiler.

### Including directories

You can control the include paths to be used by the linter with the `fortran.linter.includePaths` option.

| :exclamation: Important                                                                                            |
| ------------------------------------------------------------------------------------------------------------------ |
| For the best linting results `linter.includePaths` should match the included paths for your project's compilation. |

```json
{
  "fortran.linter.includePaths": ["/usr/include/**", "${workspaceFolder}/include/**"]
}
```

| :exclamation: Important                                                          |
| -------------------------------------------------------------------------------- |
| If a glob pattern is used only directories matching the pattern will be included |

### Additional linting options

More options can be passed to the linter via

```json
{
  "fortran.linter.extraArgs": [
    "-fdefault-real-8",
    "-fdefault-double-8",
    "-Wunused-variable",
    "-Wunused-dummy-argument"
  ]
}
```

Default value is `-Wall` (or `-warn all` for ifort).

### Changing linting compiler

By default, the linter used is `gfortran`, Intel's `ifort` and LLVM's `flang` are also supported.
One can use a different linter compiler via the option

```jsonc
{
  "fortran.linter.compiler": "ifort" | "gfortran" | "flang" | "Disabled"
}
```

The linter executable is assumed to be found in the `PATH`.
In order to use a different executable or if the executable can't be found in the `PATH`
you can point the extension to another linter with the `fortran.linter.compilerPath` option.

```json
{
  "fortran.linter.compilerPath": "/opt/oneapi/compiler/2022.0.2/linux/bin/intel64/ifort"
}
```

## Debugging

![alt](assets/gdb_ani.gif)

The extension uses the debugger from Microsoft's
[C/C++ extension](https://github.com/Microsoft/vscode-cpptools)
for Visual Studio Code. This allows this extension to use the full functionality
of the C/C++ extension for debugging applications:
(un)conditional breaking points, expression evaluation, multithreaded debugging,
call stack, stepping, watch window.

A minimal `launch.json` script, responsible for controlling the debugger, is
provided below. However, Visual Studio Code is also capable of autogenerating
a `launch.json` file and the configurations inside the file.

More details about how to set up the debugger can be found in Microsoft's website:

- General information about debugging in VS Code: <https://code.visualstudio.com/docs/editor/debugging>
- C/C++ extension debugger information: <https://code.visualstudio.com/docs/cpp/cpp-debug>
- Build tasks for easy compiling: <https://code.visualstudio.com/docs/editor/tasks>

```jsonc
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "(gdb) Fortran",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/a.out",
      "args": [], // Possible input args for a.out
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```

## Formatting

Two formatters are supported [`findent`](https://github.com/gnikit/findent-pypi)
and [`fprettify`](https://github.com/pseewald/fprettify). Both of them can be
installed with `pip` automatically through the extension.

|              findent              |              fprettify              |
| :-------------------------------: | :---------------------------------: |
| ![alt](./assets/findent-demo.gif) | ![alt](./assets/fprettify-demo.gif) |

The formatter is controlled by the user option

```jsonc
{
  "fortran.formatting.formatter": "findent" | "fprettify" | "Disabled"
}
```

Additional arguments to the formatter can be input using

```json
{
  "fortran.formatting.findentArgs": ["-Cn", "-Rr"],
  "fortran.formatting.fprettifyArgs": ["--whitespace-comma", "--enable-decl"]
}
```

If the formatter is not present in the `PATH` its location can be input with

```json
{
  "fortran.formatting.path": "/custom-path-to-formatter-binary"
}
```

| :memo: Note                                                            |
| ---------------------------------------------------------------------- |
| `findent` can also be used to generate dependency files for a project. |

## Snippets

Snippets are included by both `fortls` Language Server and the Modern Fortran VS Code extension.
Some available snippets can be seen below. Users can define their own snippets
by following these VS Code [instructions](https://code.visualstudio.com/docs/editor/userdefinedsnippets).

If you think a snippet should be shipped by with the extension feel free to
submit a [feature request](https://github.com/fortran-lang/vscode-fortran-support/issues/new?assignees=&labels=feature-request&template=feature_request.md&title=)

### Program skeleton

![program snippet](https://media.giphy.com/media/OYdq9BKYMOOdy/giphy.gif)

#### Module skeleton

![module snippet](https://media.giphy.com/media/3ohzdUNRuio5FfyF1u/giphy.gif)

## Advanced options

To show the symbols in the file outline enable `provide.symbols`. Symbols can be
served by the fortls, the built-in, both or none. By default
`fortls` is used.

```jsonc
{
  "fortran.provide.symbols": "fortls" | "Built-in" | "Both" | "Disable"
}
```

You can also configure the case for fortran intrinsics auto-complete by using

```jsonc
{
    "fortran.preferredCase": "lowercase" | "uppercase"
}
```

## All options

A summary of all the options

| Setting                            | Possible values                          | Default     | Description                                                                                                                                                                                        |
| ---------------------------------- | ---------------------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `provide.hover`                    | `fortls`, `Built-in`, `Both`, `Disabled` | `fortls`    | Enables hover support, by default it uses fortls                                                                                                                                                   |
| `provide.autocomplete`             | `fortls`, `Built-in`, `Disabled`         | `fortls`    | Enables code autocompletion, by default it uses fortls                                                                                                                                             |
| `provide.symbols`                  | `fortls`, `Both`                         | `fortls`    | Outline of modules, functions, subroutines, etc. in the document, by default it uses fortls, `Both` will use `fortls` and the `Built-in` functionality (not recommended)                           |
| `linter.compiler`                  | `gfortran`, `flang`, `ifort`, `Disabled` | `gfortran`  | Compiler used for linting support                                                                                                                                                                  |
| `linter.includePaths`              | String Array                             | `[]`        | Specifies folder paths to be used as include paths during linting. Can handle globs                                                                                                                |
| `linter.compilerPath`              | String                                   | `''`        | Specifies the path to the linter executable                                                                                                                                                        |
| `linter.extraArgs`                 | String Array                             | `[-Wall]`   | Pass additional options to the linter compiler                                                                                                                                                     |
| `linter.modOutput`                 | String                                   | `''`        | Global output directory for .mod files generated due to linting                                                                                                                                    |
| `formatting.formatter`             | `findent`, `fprettify`, `Disabled`       | `findent`   | Fortran formatter, currently supports findent and fprettify                                                                                                                                        |
| `fortran.formatting.findentArgs`   | String Array                             | `[]`        | Additional arguments for the findent formatter                                                                                                                                                     |
| `fortran.formatting.fprettifyArgs` | String Array                             | `[]`        | Additional arguments for the fprettify formatter                                                                                                                                                   |
| `formatting.path`                  | String                                   | `''`        | If the formatter is not in the $PATH specify the full path to its location                                                                                                                         |
| `fortls.path`                      | String                                   | `fortls`    | Path to the Fortran language server (fortls)                                                                                                                                                       |
| `fortls.preserveKeywordOrder`      | Boolean                                  | `true`      | Display variable keywords information when hovering in original order (default: sort to consistent ordering)                                                                                       |
| `fortls.disableDiagnostics`        | Boolean                                  | `false`     | Disable additional diagnostics from fortls e.g. multiple variable definitions, unknown modules in use statement (requires v1.12.0+)                                                                |
| `fortls.incrementalSync`           | Boolean                                  | `true`      | Use incremental synchronization for file changes                                                                                                                                                   |
| `fortls.symbolTypes`               | Boolean                                  | `trues`     | Include derived types in the symbols outline. If false it adds the `--symbol_skip_mem` argument to fortls.                                                                                         |
| `fortls.notifyInit`                | Boolean                                  | `false`     | Notify when workspace initialization is complete (requires v1.7.0+)                                                                                                                                |
| `fortls.maxLineLength`             | Number                                   | -1          | Maximum line length (fortls requires v1.8.0+). For `gfortran` and `flang` this also sets the linting compiler flag `-ffree-line-length-<n>` and `-ffixed-line-length-<n>`. Default value is `none` |
| `fortls.maxCommentLineLength`      | Number                                   | -1          | Maximum comment line length (fortls requires v1.8.0+).                                                                                                                                             |
| `fortls.extraArgs`                 | String Array                             | `[]`        | Additional arguments for the fortls                                                                                                                                                                |
| `fortls.disabled`                  | Boolean                                  | `false`     | Disable the Language Server. If true, it will limit the extension's functionality substantially (should be avoid!)                                                                                 |
| `preferredCase`                    | `lowercase`, `uppercase`                 | `lowercase` | Specify the word case to use when suggesting autocomplete options                                                                                                                                  |

## Requirements

For debugging you need to have one of the following debuggers installed:

- **Linux**: GDB
- **macOS**: GDB or LLDB
- **Windows**: GDB or Visual Studio Windows Debugger

## Issues

Please report any issues and feature request on the GitHub repo [here](https://github.com/krvajalmiguelangel/vscode-fortran-support/issues/new)

## Notice

The syntax highlight support was imported from [TextMate bundle](https://github.com/textmate/fortran.tmbundle)

The idea of using `gfortran` comes from this awesome [fortran plugin](https://github.com/315234/SublimeFortran) for Sublime Text.

## LICENSE

MIT
