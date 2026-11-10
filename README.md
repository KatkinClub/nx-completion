# Nx completion plugin for Zsh

> This plugin bring Nx autocompletion to Zsh.

![demo](https://user-images.githubusercontent.com/8522558/111908149-67e8d780-8a58-11eb-9343-691f6d664163.gif)

## Features

- ✅ **Dynamic command & option parsing**
  Automatically discovers and updates completions from `nx --help` output

- 🚀 **Intelligent caching = blazing fast completions**
  Leverages Nx’s project graph cache and memoized command parsing

- 🧠 **Workspace-aware, always up-to-date**
  Auto-syncs completions with your Nx version, project executors, and custom generators

- 🧩 **Deep integration with Nx executors**
  Completes `--` options from the executor's own `schema.json` (for example `--fix` for `@nx/eslint:lint`), using the executor of the project you typed, including for custom targets such as `nx apply-cdk`

- 🎯 **Only relevant projects**
  `nx build <TAB>` (and `lint`, `test`, `serve`, `e2e` or any custom target) lists only the projects that define that target

- ✨ **Smart autocompletion**
  Supports arguments, flags, projects, targets, and generators—all in context

- 📦 **Version flexibility**
  Works seamlessly across different Nx workspace versions

## Install

### Prerequisit

Install [`jq`](https://stedolan.github.io/jq/) dependency:

```shell
apt install jq
```

On MacOS you can install with:

```shell
brew install jq
```

> **Note**: [`jq`](https://stedolan.github.io/jq/) is a lightweight command-line JSON processor used to manipulate the workspace graph.

### As an [Oh My ZSH!](https://github.com/robbyrussell/oh-my-zsh) custom plugin

Clone the repository into the custom plugins directory:

```shell
git clone git@github.com:KatkinClub/nx-completion.git ~/.oh-my-zsh/custom/plugins/nx-completion
```

Then load it as a plugin in your `.zshrc`:

```shell
plugins+=(nx-completion)
```

### Manually

Clone this repository somewhere (`~/.nx-completion` for example):

```shell
git clone git@github.com:KatkinClub/nx-completion.git ~/.nx-completion
```

Then source it in your `.zshrc`:

```shell
source ~/.nx-completion/nx-completion.plugin.zsh
```

## How completion works

### Options (`nx <command> <project> --<TAB>`)

Options are resolved from the executor behind the target:

1. If a project is already on the command line, the executor configured for that project's target is used. The same target name can use different executors in different projects (for example `build` may be `@nx/webpack:webpack`, `@nx/next:build` or `@nx/js:tsc`).
2. Otherwise the most common executor for that target name is used.
3. For `build`, `lint`, `test`, `serve` and `e2e` only, if no executor can be resolved the plugin falls back to guessing executors by name.

The options come from the executor's `schema.json`, found through the package's `executors.json` in `node_modules`. Every option is offered with its description, booleans take no value, and enums complete their values. Option keys that projects set in their target `options` but that the schema does not declare are added too.

Limitations:

- Local executors (`./tools/executors/foo:bar`) have no resolved schema, so only the option keys set in project config are offered.
- Targets that use `nx:run-commands` get the `run-commands` options (`--command`, `--cwd`, `--env`, ...), not the flags of the tool they run.

### Projects (`nx <target> <TAB>`)

Only projects that define the target are offered. If no project defines it, all projects are offered.

### Limits

| Variable | Default | Applies to |
|---|---|---|
| `NX_MAX_RESULTS` | `30` | Generators, `nx run` targets, and the unfiltered project list (`--exclude`, `--focus`) |
| `NX_MAX_OPTIONS` | `100` | Options listed per command |

Command names (`nx <TAB>`) and the project lists above are not capped.

## Cache Management

When reinstalling or updating the nx-completion plugin, you may need to flush the zsh completion cache to ensure you're using the latest version.

### Quick Cache Clear

The simplest way to clear the zsh completion cache:

```shell
# Clear zsh completion cache and rebuild
rm -rf ~/.zcompdump* && autoload -U compinit && compinit -D
```

### Using the Clear Cache Script

Run the included script for automated cache clearing:

```shell
# Make executable and run
chmod +x clear-cache.zsh
./clear-cache.zsh
```

## Testing

This repository includes a comprehensive test environment in the `test/` directory with simplified project structures for easy testing and development.

### Test Environment Structure

```
test/
├── .nx/workspace-data/project-graph.json  # Main test graph (.nodes structure)
├── nx.json                                # Nx workspace config
├── project-graph-nested.json              # Test graph (.graph.nodes structure)
├── test-completion.zsh                    # Automated test script
├── test-cache.zsh                         # Cache performance test script
├── PERFORMANCE-TESTING.md                 # Real-world performance testing guide
└── README.md                              # Test environment docs
```

### Quick Testing

```bash
# Run automated tests
cd test && ./test-completion.zsh

# Test caching performance
cd test && ./test-cache.zsh

# Interactive completion testing
cd test
source ../nx-completion.plugin.zsh
nx <TAB>  # Test completions

# Performance testing guide
cd test && cat PERFORMANCE-TESTING.md
```

The test environment includes 5 projects (frontend-app, backend-api, shared-utils, ui-components, data-access) with realistic Nx configurations and supports testing both JSON structure formats.

## License

This project is MIT licensed.
