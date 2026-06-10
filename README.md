# Yocto Combo-Layer Configuration

This repository manages combo-layer configurations for Yocto LTS maintenance
branches.

## Configuration Split

To avoid committing personal local paths and machine-specific configurations
into the shared git repository, configurations are split into two files:

1. **Base configuration (`<name>.conf`)**: Contains repository details, branch
   tracking, file exclusions, and current revisions. This file is tracked in
   git.
2. **Local configuration (`<name>-local.conf`)**: Contains local system paths
   (e.g., `local_repo_dir` and `hook` locations). This file is ignored by git.

### Setup Instructions

1. Copy the provided sample local configuration file:
   ```bash
   cp combo-layer-stable-scarthgap-nut-local.conf.sample \
      combo-layer-stable-scarthgap-nut-local.conf
   ```
2. Edit `combo-layer-stable-scarthgap-nut-local.conf` and adjust the paths
   `local_repo_dir` and `hook` for each repository section to match your local
   system.

---

## Running Combo-Layer

The `./combo-layer` script automatically searches for and loads the
corresponding `-local.conf` file if it exists in the same directory as the
specified base configuration.

To run an update without pulling downstream component repositories (`update -n`):

```bash
./combo-layer -c combo-layer-stable-scarthgap-nut.conf update -n
```

When updates to `last_revision` are written by `combo-layer`, they are written
strictly to the base configuration file (`combo-layer-stable-scarthgap-nut.conf`),
while local paths remain untouched in your ignored local configuration file.
