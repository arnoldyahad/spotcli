# SpotCLI

![GitHub Workflow Status](https://img.shields.io/github/workflow/status/ironsource-mobile/spotcli/Release)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/ironsource-mobile/spotcli)
![GitHub Release Date](https://img.shields.io/github/release-date/ironsource-mobile/spotcli)
![GitHub issues](https://img.shields.io/github/issues/ironsource-mobile/spotcli)
![GitHub top language](https://img.shields.io/github/languages/top/ironsource-mobile/spotcli)
![GitHub](https://img.shields.io/github/license/ironsource-mobile/spotcli)

SpotCLI is a command-line interface for managing Spot Elastigroups.

## Installation

Install latest SpotCLI:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ironsource-mobile/spotcli/main/install.sh)"
```

You can also install SpotCLI manually:

- Download wheel from [releases](https://github.com/ironsource-mobile/spotcli/releases) page
- Install SpotCLI using pip:

```bash
pip install spotcli-1.0.0-py3-none-any.whl
```

## Configuration

SpotCLI looks in `~/.spot/config.yaml` by default, extending the config if extra sources are specified. Basic config may look like this:

```yaml
version: 1
sources:
  # Extra config will be loaded from Consul speficied in consul-example provider
  - provider: consul-example
    # source's path will be appended to provider's base KV path
    path: config
providers:
  # Consul provider for configs
  consul-example:
    kind: consul
    server: consul.example.com:8500
    # Base KV path
    path: /spotcli
  # Spot provider
  spot:
    kind: spot
    account: act-1234abcd
    token: 0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
scenarios:
  roll-web:
    description: Roll web servers
    tasks:
      - kind: roll
        batch: 25%
        grace: 10m
        targets:
          # Exact match
          - production.web.us-east-1
  upscale-db:
    description: Upscale DB servers by 10%
    tasks:
      - kind: upscale
        amount: 10%
        targets:
          # Alias interpolation
          - db
aliases:
  mysql:
    # Regular expression
    - "mysql-[0-9](?!.*eu-central-1)"
  redis:
    # Exact match
    - production.redis1.us-east-1
    # Substring match
    - redis2
  db:
    # Alias interpolation
    - mysql
    - redis
```

## Usage

### Scenarios

When scenarios are defined in the config, you may call them:

```bash
spotcli run roll-web
```

To automatically accept all prompts, add `-y`.

### Ad-Hoc Actions

You can run multiple standalone actions with this tool as well.

Supported actions:

- status (*prints group name, ID, instance count and process suspension status when `--show-processes` is used*)
- roll
- suspend/unsuspend process or scaling policy (*you can do multiple at once*)
- scale up/down (*by instance amount or percentage from currect capacity*)

To see more details about each action, run `spotcli {action} --help` (replace *`{action}`* with action in question).
