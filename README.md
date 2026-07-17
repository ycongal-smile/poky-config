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

---

## Auto-filled cover letters (`with-cover`)

`with-cover` is a wrapper around `create-pull-request` that fills in the
repetitive parts of the LTS cover-letter blurb. Prepend it to any
`create-pull-request` line from an `LTS-*-process.txt` file:

```bash
# from openembedded-core (OE-core):
../poky-config/with-cover scripts/create-pull-request -u contrib \
    -p "OE-core][scarthgap" -s "Patch review" \
    -b stable/scarthgap-nut -r stable/scarthgap-next

# from a sibling component (bitbake, meta-yocto, ...):
../poky-config/with-cover ../openembedded-core/scripts/create-pull-request ...
```

It reads the request type from the `-s` subject on that line:

- `-s "Patch review"` → a patch-review request, including a "comments back by"
  deadline set to two working days from today (Mon–Fri, holidays not accounted
  for).
- `-s "Pull request ..."` → a pull-request / merge blurb that points back to the
  previous patch review.

The release codename is taken from the `-p`/`-b` values, or from `$LTS_RELEASE`
(needed for `yocto-autobuilder2`, whose prefix carries no release):

```bash
LTS_RELEASE=scarthgap ../poky-config/with-cover scripts/create-pull-request ...
```

Anything it cannot know — the autobuilder run URL, the link to the previous
patch review — is left as `XXX` for you to fill in.

To guard against sending an unedited cover letter, `with-cover` re-inserts the
`*** BLURB HERE ***` token into the generated cover letter. `send-pull-request`
refuses to send while that token is present, so you must open the cover letter,
fill the `XXX` placeholders, and remove the token before sending.

`with-cover` stays out of the way (runs `create-pull-request` unchanged) if the
subject is neither a review nor a pull request, or if you already passed your own
`-m` body. The upstream `create-pull-request` script is never modified.
