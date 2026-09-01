---
layout: chapter
title: "Haskell Installation"
---

## Learning Outcomes

- Install Haskell via GHCup and Stack

## Install GHCup

We will use [GHCup](https://www.haskell.org/ghcup/) to install and manage our tooling. The commands we will use to install GHCup differ slightly from the [official installation instructions](https://www.haskell.org/ghcup/install) in order to speed up the installation process.[^1]

- [Windows Instructions](#windows)

- [Non-Windows Instructions](#non-windows)

### Windows

Run this command in a PowerShell session:

```ps1
Set-ExecutionPolicy Bypass -Scope Process -Force;[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; try { & ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -Interactive -DisableCurl -Minimal } catch { Write-Error $_ }
```

In the installation process, select the following options (you might not be asked the first question if you do not have Chocolatey):

```none
Chocolatey was detected on your system...
Continue with GHCup installation?
[C] Continue  [A] Abort  [?] Help (default is "C"): C

Where to install to (this should be a short Path, preferably a Drive like 'C:\')?
If you accept this path, binaries will be installed into 'C:\ghcup\bin' and msys2 into 'C:\ghcup\msys64'.
Press enter to accept the default [C:\]: (Enter)

Specify Cabal directory (this is where haskell packages end up)
Press enter to accept the default [C:\\cabal]: (Enter)

Do you want to install the haskell-language-server (HLS) for development purposes as
well?
[Y] Yes  [N] No  [A] Abort  [?] Help (default is "N"): N

Do you want to install stack as well?
[Y] Yes  [N] No  [A] Abort  [?] Help (default is "N"): N

Do you want to create convenience desktop shortcuts (e.g. for uninstallation and mysys2 shell)?
[Y] Yes  [N] No  [A] Abort  [?] Help (default is "Y"): N

Do you want GHCup to install a default MSys2 toolchain (recommended)?
[Y] Yes  [N] No  [?] Help (default is "Y"): Y
```

Run

```sh
ghcup --version
```

to check that the installation has succeeded. You might need to restart your terminal first.

### Non-Windows

If you are on macOS and have Homebrew, you can install GHCup via Homebrew: `brew install ghcup`

<div class="alert-box alert-warning" markdown="1">
If you install GHCup via Homebrew, you need to add `$HOME/.ghcup/bin` to your `$PATH`. For example, if you use ZSH:
```sh
echo 'export PATH="$HOME/.ghcup/bin:$PATH"' >> ~/.zshrc
```
</div>

If not, run this command in a terminal:

```sh
export BOOTSTRAP_HASKELL_MINIMAL=1 && curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```

In the installation process, select the following options:

```none
Do you want ghcup to automatically add the required PATH variable to "..."?

[P] Yes, prepend  [A] Yes, append  [N] No  [?] Help (default is "P").

A
```

Run

```sh
ghcup --version
```

to check that the installation has succeeded. You might need to restart
your terminal first.

## Install tools using GHCup

We will be installing several tools.
Run each of these commands in a terminal:

```sh
ghcup install ghc 9.10.3 --set
ghcup install hls 2.14.0.0 --set
ghcup install stack 3.11.1 --set
stack config set snapshot lts-24.44
stack config set system-ghc --global true
```

optionally, if they are available for your platform the formatter and linter for Haskell code may be useful:

```sh
ghcup config add-release-channel 3rdparty
ghcup install fourmolu 0.20.0.0 --set
ghcup install hlint 3.10 --set
```

## Check Setup

Run:

```sh
stack setup
```

which should print something similar to this:

```none
Stack will use the GHC on your PATH. For more information on paths, see stack path and stack exec env. To use this GHC and packages outside of a project, consider using: stack ghc, stack ghci, stack runghc, or stack exec.
```

Stack should **not** redownload GHC (like below).
If this appears, **stop** the command (Ctrl+C) and make sure you have [run all the commands](#install-tools-using-ghcup) successfully.

```none
Preparing to install GHC (tinfo6) to an isolated location. This will not interfere with any system-level installation.
ghc-tinfo6-9.10.3: download has begun
```

Also run the following to check that setup worked:

```sh
stack ghc -- --version
```

which should output (again without trying to download anything):

```none
The Glorious Glasgow Haskell Compilation System, version 9.10.3
```

## VS Code Setup

Install the [Haskell extension](https://marketplace.visualstudio.com/items?itemName=haskell.haskell) and [Haskell linting extension](https://marketplace.visualstudio.com/items?itemName=hoovercj.haskell-linter).
These provide Haskell language support including language server, syntax highlighting, and linting.

You can alternatively install these extensions through the command line by running:

```sh
code --install-extension haskell.haskell hoovercj.haskell-linter
```

When you open VS Code, if you are asked

> How do you want the extension to manage/discover HLS and the relevant toolchain?

select the 'Manually via PATH' option (**not** the recommended option).
This corresponds to the 'Haskell: Manage HLS' option in VS Code, which should be set to `PATH` (`"haskell.manageHLS": "PATH"`).

Also set the 'Haskell: Formatting Provider' (`haskell.formattingProvider`) setting in VS Code to 'fourmolu'.

## Troubleshooting

For further issues, see [GHCup’s Troubleshooting page](https://www.haskell.org/ghcup/guide/#troubleshooting).

### Windows Issues

#### `invalid argument (invalid character)`

If you get an error like this:

```none
Error: [S-7282]
       Stack failed to execute the build plan.

       While executing the build plan, Stack encountered the following errors:

       <stderr>: commitAndReleaseBuffer: invalid argument (invalid character)
```

Go to Settings → Time and Language → Language and Region → Administrative language Settings → Change System Locale... → and check ‘Use Unicode UTF-8’. You might need to restart your computer.

#### Spaces or special characters in username

If you get an error that looks like this:

```none
Warning: [S-8432]
          Stack's 'programs' path is C:\Users\Your Name\AppData\Local\Programs\stack\. It contains a space character. This will prevent building with GHC 9.4.1 or later.

          To avoid sucn problems, use the local-programs-path non-project specific configuration option to specify an alternative path without those characteristics.
```

or if it says

```none
It contains at least one non-ISO/IEC 8859-1 (Latin-1) character (Unicode code point > 255). This will cause problems with packages that build using the hsc2hs tool with its default template template-hsc.h.
```

you need to change the `local-programs-path` Stack configuration option. To do this, edit `%AppData%\stack\config.yaml` (by default it will be something like `C:\Users\Your Name\AppData\Roaming\stack\config.yaml`). You can also do this by running the command

```ps1
notepad.exe $(stack path --global-config)
```

Add the following line to the bottom of the file:

```yml
local-programs-path: C:\stack-programs
```

#### Script immediately exits

If the script exits without installing anything, you might need to disable any third-party antivirus software.

### macOS Issues

If you get an error that looks something like `fatal error: 'ffi.h'`, it may be because you do not have the Xcode command line tools installed. (These are a collection of command line utilities and other developer tools that many programs rely on.) You can install them by running the following command in your terminal:

```sh
xcode-select --install
```

If you have an existing (possibly broken) installation, you might need to get rid of it first:

```sh
sudo rm -rf /Library/Developer/CommandLineTools
```

## Appendix: What are all these different tools?

If you are curious, here’s an explanation of the different Haskell build tools you may come across or hear about. (You do not need to know this.)

- [**GHC**](https://www.haskell.org/ghc): Glasgow Haskell Compiler, the most popular Haskell compiler.
- [**Cabal**](https://www.haskell.org/cabal): a Haskell build tool that uses GHC to build Haskell packages, which are specified by a `.cabal` file.
- [**Stack**](https://www.haskellstack.org): another Haskell build tool that wraps around Cabal. Configuration for Stack is in the `stack.yaml` file.
  - Stack provides package sets---a collection of packages that compile together---to make managing dependencies less troublesome. The package set is specified by the `snapshot` or `resolver` key in `stack.yaml`. For example, if your program depends on package A v1 and package B v1, but package A v1 depends on package B v2, your code won’t compile. A Stack package set might specify to use v2 of package A (which depends on package B v2) and v2 of package B v2, which work together. This way, you only need to say ‘I want package A and package B’, and Stack takes care of the rest.
  - Importantly, a package set also relies on a specific version of GHC. In this unit, we have set it up so that Stack manages this for you and installs the correct version of GHC for you.
  - Because `.cabal` files can be tedious to write by hand, Stack supports an alternative format, [**hpack**](https://github.com/sol/hpack), in a `package.yaml` file. When you run a Stack command, Stack will automatically generate a `.cabal` file from this.
- [**HLS**](https://haskell-language-server.readthedocs.io/en/stable): Haskell Language Server. This is the program that editors (e.g. the VS Code Haskell extension) need to give you nice IDE features. Different versions of HLS work with different versions of GHC, which are indicated by `hls-powered` in `ghcup tui`.
  - You might have noticed a `hie.yaml` file in the applied code bundles. This is [**hie-bios**](https://github.com/haskell/hie-bios), a way to tell editors information about the workspace so that the language server works.
- [**GHCup**](https://www.haskell.org/ghcup): a tool that helps you install specific versions of GHC, Cabal, Stack, and HLS.

You may have noticed these instructions do not get you to install GHC directly via GHCup. This is because as mentioned above, we have set it up so that Stack installs the correct version of GHC for you. This means that if you want to run GHCi for example, you need to run `stack ghci` instead of just `ghci`.

(It is possible to install GHC globally so you can run `ghci` without having to put `stack` in front of it; however, by default, this will mean you have two copies of GHC installed on your machine! This is why we modified the GHCup installation process slightly from the official instructions so that it doesn’t automatically install GHC and other tools we don’t need. There is a way around this by setting [`system-ghc: true`](https://docs.haskellstack.org/en/stable/configure/yaml/non-project/#system-ghc) in Stack, but you must ensure that the version of GHC you have installed matches the Stack package set for your project.)

[^1]: The only difference from the official installation script is that we stop GHCup from installing GHC (by adding the `-Minimal` flag to the Windows install script or by setting the environment variable `BOOTSTRAP_HASKELL_MINIMAL=1` on Unix), which can take a long time. Instead, we will install GHC via Stack, which will ensure we always have the correct version of GHC needed.
