# nxnative URL → iOS IPA (GitHub Actions)

A **private** GitHub Actions wrapper for [seedds/nxnative](https://github.com/seedds/nxnative). It accepts a **ZIP download URL** for a game dump you are authorized to use, extracts it on an Apple Silicon runner, and tries to build an unsigned IPA.

## Run

1. Open **Actions → Build nxnative IPA from ROM ZIP → Run workflow**.
2. Enter **rom_zip_url** with a direct HTTPS download link, or leave it empty after adding a repository Actions secret named `ROM_ZIP_URL` under **Settings → Secrets and variables → Actions**.
3. Leave `romfs_mode` as **separate** (recommended for big titles), then click **Run workflow**.
4. On success, download **nxnative-unsigned-ipa** from the workflow's Artifacts section. The artifact expires after **1 day**.

**Privacy:** ROM contents are never committed, cached, or included in an artifact by this workflow. The original zip URL may be visible to repository collaborators in GitHub's workflow-dispatch metadata. For signed/private URLs, use `ROM_ZIP_URL` repository secret instead. Avoid running forks or untrusted PR code. Only use ROMs you have permission to use; do not share copyrighted game data.

## For Mario Kart World v1.8.0

The local ZIP inspected on 2026-11-16 contained the following layout:

```
updates/917504/exefs/main
updates/917504/exefs/main.npdm
updates/917504/romfs/
```

The workflow supports this layout and another variant nested inside one outer directory.

**Important:** `--romfs separate` generates a **small IPA without the 20+ GB game resources**. To actually play, copy the **matching RomFS from your original game data** to the iPhone with the instructions from nxnative's `scripts/push_romfs.sh` / [Quick reinstalls](https://github.com/seedds/nxnative/blob/main/docs/development.md#quick-reinstalls). The workflow intentionally does not publish RomFS artifacts. Switching to `bundle` attempts an enormous IPA and will likely exceed Actions artifact or installation limits.

## Runner and capacity limitations

- The workflow requests `runs-on: xcode-27` and selects **Xcode 27.2**, Apple Silicon. If the runner is unavailable to your plan/repository, the job will remain queued or fail to start.
- nxnative requires **macOS 26.6+**, Xcode 27.2, and a modern Python. The workflow checks the selected toolchain and installs dependencies at runtime.
- The original ZIP (~22 GB) and extracted contents (~23.5 GB) coexist until extraction finishes, and additional shader/build data can need substantial space. The workflow checks disk free space and stops early if insufficient.
- Free GitHub Actions minute quota, hosted-runner eligibility, and artifact quotas depend on your account/plan. A private macOS job may consume billed minutes.
- This repository is a *build workflow*, not a tested successful game port. Switch 2 on iOS is not guaranteed by nxnative. No automatic iPhone signing or installation is provided.

## Troubleshooting

Read the job log to identify the failing step. For disk space problems, use a compatible self-hosted Apple Silicon runner with more storage or a different runner image. For download problems, ensure the link is a direct URL returning a ZIP (not an HTML share page). For compilation problems, compare the source game dump's layout and the upstream game profile.

No ROM, firmware, decryption key, or copyrighted asset is stored in this repository.
