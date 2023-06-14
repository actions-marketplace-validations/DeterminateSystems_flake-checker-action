# The Determinate Flake Checker Action

This flake checker [Action] from [Determinate Systems][detsys] performs health checks on your repos' [`flake.lock`][lock] files.
Specifically, it checks that your root [Nixpkgs] input:

* Has been updated within the last 30 days
* Has the [`NixOS`][nixos-org] GitHub org as its owner
* Is from a supported Git branch

Here's an example configuration that uses `flake-checker-action` as part of a broader Actions workflow involving Nix.

```yaml
on:
  pull_request:
  push:
    branches: [main]

jobs:
  build:
    name: Build Nix targets
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
      - name: Check Nix flake inputs
        uses: DeterminateSystems/flake-checker-action@v1 # This action
      - name: Install Nix
        uses: DeterminateSystems/nix-installer-action@v3
      - name: Build default package
        run: nix build
```

## Configuration

The Flake Checker Action has a number of configuration parameters that you can set in the `with` block:

Parameter | Description | Default
:---------|:------------|:-------
`flake-lock-path` | The path to the `flake.lock` file you want to check. | `flake.lock`
`check-outdated` | Whether to check that the root Nixpkgs input is less than 30 days old. | `true`
`check-owner` | Whether to check that the root Nixpkgs input has the `NixOS` GitHub org as its owner. | `true`
`check-supported` | Whether to check that the root Nixpkgs input has a supported Git ref. Currently supported refs: `nixos-22.11`, `nixos-22.11-small`, `nixos-23.05`, `nixos-23.05-small`, `nixos-unstable`, `nixos-unstable-small`, `nixpkgs-22.11-darwin`, `nixpkgs-23.05-darwin`, `nixpkgs-unstable`. | `true`
`ignore-missing-flake-lock` | Whether to ignore a missing `flake.lock` file, where the path to the file is the value of `flake-lock-path` parameter. If set to `false` (the default is `true`), the Action throws an error and the job fails if the lockfile is missing. | `true`
`fail-mode` | Fail with an exit code of 1 if any issues are encountered. | `false`
`send-statistics` | Anonymously report the number of issues detected by the flake checker. This reporting helps measure the effectiveness of the flake checker. Set to `false` to disable. | `true`

Here's an example non-default configuration:

```yaml
- name: Check Nix flake inputs
  uses: DeterminateSystems/flake-checker-action@v1
  with:
    flake-lock-path: ./nix/flake.lock
    check-owner: false
    ignore-missing-flake-lock: false
    fail-mode: true
```

[action]: https://github.com/features/actions
[detsys]: https://determinate.systems
[lock]: https://zero-to-nix.com/concepts/flakes#lockfile
[nixos-org]: https://github.com/NixOS
[nixpkgs]: https://github.com/NixOS/nixpkgs
