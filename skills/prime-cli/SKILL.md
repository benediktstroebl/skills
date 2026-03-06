---
name: prime-cli
description: >
  Use when the user mentions: GPU, pods, compute, sandboxes, ML environments, evaluations,
  evals, RL training, reinforcement learning, inference, Docker images, tunneling, disks,
  secrets, Prime Intellect, prime CLI, deploy model, rent GPU, SSH pod, sandbox SDK,
  verifiers, GEPA, OpenEnv, code execution, persistent storage, team management.
---

# Prime CLI Reference

The `prime` CLI manages GPU compute, sandboxes, ML environments, evaluations, RL training, and more on the Prime Intellect platform.

## Authentication

```bash
prime login                  # Browser-based login (recommended)
prime login --headless       # Print URL without opening browser
prime config set-api-key     # Set API key directly (prompts securely)
```

**Environment variables** (override config file):
- `PRIME_API_KEY` - API key
- `PRIME_TEAM_ID` - Team ID
- `PRIME_USER_ID` - User ID
- `PRIME_API_BASE_URL` / `PRIME_BASE_URL` - API base URL
- `PRIME_FRONTEND_URL` - Frontend URL
- `PRIME_INFERENCE_URL` - Inference API URL
- `PRIME_SSH_KEY_PATH` - SSH private key path

**Config file**: `~/.prime/config.json` (created by `prime login`)

## Global Options

- `--version / -v` - Show version
- `--context / -c <name>` - Use a specific config context for this command
- `--output / -o table|json` - Output format (most list/get commands)
- `--yes / -y` - Skip confirmation prompts
- `--watch / -w` - Real-time refresh (pods list, disks list)
- `--num / -n` and `--page / -p` - Pagination (evals, rl, env, deployments)

---

## Account Commands

### `prime login`
Browser-based authentication. Prompts for team selection after login.
- `--headless` - Don't open browser

### `prime whoami`
Show current user info, team, and token permissions.

### `prime config`
| Subcommand | Description |
|---|---|
| `view` | Show current configuration |
| `set-api-key [KEY]` | Set API key (prompts securely if omitted) |
| `set-team-id [ID]` | Set team ID (empty = personal account) |
| `remove-team-id` | Switch to personal account |
| `set-base-url [URL]` | Set API base URL |
| `set-frontend-url [URL]` | Set frontend URL |
| `set-inference-url [URL]` | Set inference URL |
| `set-ssh-key-path <PATH>` | Set SSH private key path |
| `reset` | Reset all config to defaults. `--yes/-y` |
| `use <ENV>` | Switch to named environment (production or custom) |
| `save <NAME>` | Save current config as named environment |
| `envs` | List available environments |

### `prime teams list`
List teams for current user. `-o table|json`

### `prime secret`
| Subcommand | Description |
|---|---|
| `list` | List secrets. `-o table|json` |
| `create` | Create secret. `--name/-n`, `--value/-v`, `--description/-d`, `--file/-f`, `-o` |
| `update [ID]` | Update secret. `--name/-n`, `--value/-v`, `--description/-d`, `-o` |
| `delete [ID]` | Delete secret. `--yes/-y` |
| `get <ID>` | Get secret details. `-o table|json` |

### `prime upgrade`
Auto-detect install method (uv/pipx/pip) and upgrade.
- `--check / -c` - Only check, don't upgrade
- `--force / -f` - Force upgrade even if on latest

---

## Compute Commands

### `prime availability`
| Subcommand | Description |
|---|---|
| `gpu-types` | List available GPU type names |
| `list` | List GPU resources with pricing. `--gpu-type`, `--gpu-count`, `--regions`, `--socket`, `--provider`, `--disks`, `--group-similar/--no-group-similar`, `-o` |
| `disks` | List available disk locations. `--regions`, `--data-center-id`, `-o` |

### `prime pods`
| Subcommand | Description |
|---|---|
| `list` | List running pods. `--limit`, `--offset`, `--watch/-w`, `-o` |
| `status <POD_ID>` | Detailed pod status. `-o` |
| `create` | Create pod (interactive). `--id`, `--cloud-id`, `--gpu-type`, `--gpu-count`, `--name`, `--disk-size`, `--vcpus`, `--memory`, `--image`, `--custom-template-id`, `--team-id`, `--disks` (repeatable), `--env KEY=VALUE` (repeatable), `--yes/-y` |
| `terminate <POD_ID>` | Terminate a pod. `--yes/-y` |
| `history` | List terminated pods. `--limit`, `--offset`, `-o` |
| `ssh <POD_ID>` | SSH into a pod (alias: `connect`) |

### `prime sandbox`
| Subcommand | Description |
|---|---|
| `list` (alias `ls`) | List sandboxes. `--team-id`, `--status`, `--label/-l` (repeatable), `--page/-p`, `--num/-n`, `--all`, `-o` |
| `get <ID>` | Sandbox details. `-o` |
| `create [IMAGE]` | Create sandbox. `--name`, `--start-command`, `--cpu-cores`, `--memory-gb`, `--disk-size-gb`, `--gpu-count`, `--gpu-type`, `--network-access/--no-network-access`, `--timeout-minutes`, `--team-id`, `--registry-credentials-id`, `--env KEY=VAL` (repeatable), `--secret KEY=VAL` (repeatable), `--label/-l` (repeatable), `--yes/-y` |
| `delete [IDs...]` | Delete sandboxes. `--all`, `--label/-l`, `--yes/-y`, `--only-mine/--all-users` |
| `run <ID> <CMD...>` | Execute command. `-w/--working-dir`, `-e/--env KEY=VAL`, `--timeout` |
| `logs <ID>` | Get sandbox logs |
| `upload <ID> <LOCAL> <REMOTE>` | Upload file to sandbox |
| `download <ID> <REMOTE> <LOCAL>` | Download file from sandbox |
| `ssh <ID>` | SSH into sandbox. `--shell/-s` |
| `expose <ID> <PORT>` | Expose port. `--name`, `--protocol/-p HTTP|TCP`, `-o` |
| `unexpose <ID> <EXPOSURE_ID>` | Remove port exposure. `--yes/-y` |
| `list-ports [ID]` | List exposed ports (all sandboxes if no ID). `-o` |
| `reset-cache` | Clear auth cache. `--yes/-y` |

### `prime disks`
| Subcommand | Description |
|---|---|
| `list` | List disks. `--limit`, `--offset`, `--watch/-w`, `-o` |
| `get <DISK_ID>` | Disk details. `-o` |
| `create` | Create disk. `--id`, `--size` (required, GB), `--name`, `--country`, `--cloud-id`, `--data-center-id`, `--team-id`, `--provider-type`, `--yes/-y` |
| `update <DISK_ID>` | Rename disk. `--name` (required) |
| `terminate <DISK_ID>` | Delete disk. `--yes/-y` |

### `prime images`
| Subcommand | Description |
|---|---|
| `push <NAME:TAG>` | Build & push Docker image. `--dockerfile/-f`, `--context/-c`, `--platform` (linux/amd64 or linux/arm64) |
| `list` | List images. `-o`, `--all/-a` (include team images) |
| `delete <NAME:TAG>` | Delete image. `--yes/-y` |

### `prime registry`
| Subcommand | Description |
|---|---|
| `list` | List registry credentials. `-o` |
| `check-image <IMAGE>` | Verify image accessible. `--registry-credentials-id` |

### `prime tunnel`
| Subcommand | Description |
|---|---|
| `start` | Start tunnel. `--port/-p` (default 8765), `--name/-n`, `--team-id` |
| `list` | List active tunnels. `--team-id` |
| `status <TUNNEL_ID>` | Get tunnel status |
| `stop [IDs...]` | Stop tunnels. `--all`, `--team-id`, `--yes/-y`, `--only-mine/--all-users` |

### `prime inference`
| Subcommand | Description |
|---|---|
| `models` | List available inference models with pricing. `-o` |

---

## Lab Commands

### `prime env`

**Explore:**
| Subcommand | Description |
|---|---|
| `list` | List environments. `--num/-n`, `--page/-p`, `--owner`, `--visibility`, `--search/-s`, `--tag/-t` (repeatable), `--action-status`, `--sort`, `--order`, `--show-actions`, `--output` |
| `status <ENV_ID>` | Action status for environment. `--output` |
| `info <ENV_ID>` | Environment details. `--version/-v` |

**Manage:**
| Subcommand | Description |
|---|---|
| `push [ENV_ID]` | Push environment to hub. `--path/-p`, `--name/-n`, `--owner` |
| `pull <ENV_ID>` | Pull environment locally. `--target/-t`, `--version/-v` |
| `init <NAME>` | Initialize new environment |
| `build <ENV_ID>` | Build OpenEnv-backed image |
| `install <ENV_IDs...>` | Install environments. `--with uv|pip`, `--path/-p`, `--no-upgrade` |
| `uninstall <NAME>` | Uninstall environment. `--with uv|pip` |
| `delete <ENV_ID>` | Delete from hub. `--force/-f` |

### `prime eval`

By default, `prime eval <environment>` runs `prime eval run <environment>`.

| Subcommand | Description |
|---|---|
| `run <ENV_OR_CONFIG>` | Run evaluation (passthrough to verifiers) |
| `list` | List evaluations. `-o`, `--num/-n`, `--page/-p`, `--env/-e` |
| `get <EVAL_ID>` | Get evaluation details. `-o json|pretty` |
| `samples <EVAL_ID>` | Get evaluation samples. `--page/-p`, `--num/-n`, `-o json|pretty` |
| `push [PATH]` | Push eval results. `--env/-e`, `--run-id/-r`, `--eval/--eval-id`, `-o json|pretty` |
| `tui` | Launch TUI viewer. `--env-dir/-e`, `--outputs-dir/-o` |

### `prime rl`

**Commands:**
| Subcommand | Description |
|---|---|
| `init [PATH]` | Generate template config (default: rl.toml). `--force/-f` |
| `run <CONFIG.toml>` | Start RL training run. `-e/--env-var KEY=VAL` (repeatable), `--env-file` |
| `list` (alias `ls`) | List runs. `--team/-t`, `--num/-n`, `--page/-p`, `-o` |
| `get <RUN_ID>` | Run details. `-o` |
| `stop <RUN_ID>` | Stop run. `--force/-f` |
| `delete <RUN_ID>` | Delete run. `--force/-f` |
| `restart <RUN_ID>` | Restart from latest checkpoint. `--force/-f` |
| `models` | List available RL models. `-o` |

**Monitoring:**
| Subcommand | Description |
|---|---|
| `logs <RUN_ID>` | Get logs. `--tail/-n`, `--follow/-f`, `--raw/-r` |
| `metrics <RUN_ID>` | Training metrics. `--min-step`, `--max-step`, `--limit/-n` |
| `rollouts <RUN_ID>` | Rollout samples. `--step/-s` (required), `--page/-p`, `--num/-n` |
| `progress <RUN_ID>` | Progress info (JSON) |
| `distributions <RUN_ID>` | Reward/advantage distributions. `--type/-t`, `--step/-s` |
| `checkpoints <RUN_ID>` | List checkpoints. `--status/-s`, `-o` |

### `prime gepa`
| Subcommand | Description |
|---|---|
| `run <ENV_OR_CONFIG>` | Run GEPA prompt optimization |

### `prime lab`
| Subcommand | Description |
|---|---|
| `setup` | Set up a verifiers training workspace |

### `prime deployments`
| Subcommand | Description |
|---|---|
| `list` | List adapter deployments. `--team/-t`, `--num/-n`, `--page/-p`, `-o` |
| `create <MODEL_ID>` | Deploy model for inference. `--yes/-y` |
| `delete <MODEL_ID>` | Unload model from inference |

---

## Common Workflows

### Get a GPU pod running
```bash
prime availability list --gpu-type H100_80GB
prime pods create --id <ID_FROM_ABOVE>     # Interactive setup
prime pods list --watch                     # Wait for ACTIVE
prime pods ssh <POD_ID>
```

### Create and use a sandbox
```bash
prime sandbox create python:3.11-slim --cpu-cores 2 --memory-gb 4
prime sandbox run <ID> -- python -c "print('hello')"
prime sandbox upload <ID> ./script.py /app/script.py
prime sandbox run <ID> -- python /app/script.py
prime sandbox delete <ID>
```

### GPU sandbox
```bash
prime sandbox create --gpu-count 1 --gpu-type H100_80GB --timeout-minutes 120
prime sandbox ssh <ID>
```

### Run and push evaluations
```bash
prime eval run gsm8k                       # Run eval
prime eval push                            # Auto-discover and push results
prime eval push ./outputs/evals/gsm8k/     # Push specific directory
prime eval list                            # View pushed evals
```

### RL training lifecycle
```bash
prime rl init                              # Generate rl.toml template
# Edit rl.toml with model, env, hyperparams
prime rl run rl.toml                       # Start training
prime rl list                              # List runs
prime rl logs <RUN_ID> --follow            # Stream logs
prime rl metrics <RUN_ID>                  # Check metrics
prime rl checkpoints <RUN_ID>              # View checkpoints
prime rl stop <RUN_ID>                     # Stop when done
```

### Persistent disks with pods
```bash
prime availability disks                   # See disk locations
prime disks create --id <ID> --size 100    # Create 100GB disk
prime pods create --id <GPU_ID> --disks <DISK_ID>  # Attach to pod
```

### Custom Docker images
```bash
prime images push myapp:v1.0.0             # Build & push from Dockerfile
prime images list                          # Check build status
prime sandbox create myapp:v1.0.0          # Use in sandbox
```

### Team collaboration
```bash
prime login                                # Select team during login
prime config set-team-id <TEAM_ID>         # Switch team
prime config remove-team-id                # Switch to personal
prime teams list                           # List your teams
```

### Tunneling
```bash
prime tunnel start --port 8080             # Expose local port
prime tunnel list                          # List active tunnels
prime tunnel stop <TUNNEL_ID>              # Stop tunnel
```

### Deploy trained model for inference
```bash
prime rl list                              # Find completed run
prime deployments list                     # Check adapter status
prime deployments create <MODEL_ID>        # Deploy for inference
prime inference models                     # Verify model available
```

---

## Python SDK (Sandboxes)

```bash
pip install prime-sandboxes
```

### Sync usage
```python
from prime_sandboxes import APIClient, SandboxClient, CreateSandboxRequest

client = APIClient(api_key="your-api-key")  # Or reads from env/config
sandbox_client = SandboxClient(client)

sandbox = sandbox_client.create(CreateSandboxRequest(
    name="my-sandbox",
    docker_image="python:3.11-slim",
    cpu_cores=2, memory_gb=4,
    environment_vars={"DEBUG": "true"},
    secrets={"API_KEY": "sk-..."},
    labels=["experiment"],
))
sandbox_client.wait_for_creation(sandbox.id)

result = sandbox_client.execute_command(sandbox.id, "python --version")
print(result.stdout)

sandbox_client.upload_file(sandbox.id, "/app/script.py", "./script.py")
sandbox_client.download_file(sandbox.id, "/app/output.txt", "./output.txt")

# Background jobs for long-running tasks
job = sandbox_client.start_background_job(sandbox.id, "python train.py")
status = sandbox_client.get_background_job(sandbox.id, job)

sandbox_client.delete(sandbox.id)
```

### Async usage
```python
import asyncio
from prime_sandboxes import AsyncSandboxClient, CreateSandboxRequest

async def main():
    async with AsyncSandboxClient(api_key="your-api-key") as client:
        sandbox = await client.create(CreateSandboxRequest(
            name="async-sandbox", docker_image="python:3.11-slim",
        ))
        await client.wait_for_creation(sandbox.id)
        result = await client.execute_command(sandbox.id, "echo hello")
        print(result.stdout)
        await client.delete(sandbox.id)

asyncio.run(main())
```

### Bulk operations
```python
# Create multiple sandboxes
ids = [sandbox_client.create(CreateSandboxRequest(...)).id for _ in range(5)]
sandbox_client.bulk_wait_for_creation(ids)
sandbox_client.bulk_delete(sandbox_ids=ids)
# Or delete by labels
sandbox_client.bulk_delete(labels=["experiment"])
```

---

## Troubleshooting

- **"Not authenticated"** - Run `prime login` or set `PRIME_API_KEY`
- **Config issues** - Check `prime config view`, reset with `prime config reset`
- **Pod stuck INSTALLING** - Check `prime pods status <ID>` for installation_error
- **SSH fails** - Verify key path: `prime config set-ssh-key-path ~/.ssh/id_rsa`
- **Sandbox auth errors** - Try `prime sandbox reset-cache`
- **Wrong team context** - Check `prime whoami`, switch with `prime config set-team-id`
- **Multiple environments** - Use `prime config save <name>` / `prime config use <name>` or `prime -c <name> <command>`
