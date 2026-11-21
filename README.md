# MisterDownloadManager — Linux (`x86-64-v3`) Automated Release Workflow

This GitHub Actions workflow (`Generic Linux Release (x86-64-v3)`) builds **MisterDownloadManager** and its native messaging host (`mdm-nmhost`) from upstream source (`anefzaoui/misterdownloadmanager`), tunes the build for modern `x86-64-v3` CPUs, strips debug symbols, and publishes a `.tar.gz` archive to GitHub Releases.

---

## Build Optimizations

* **CPU Target (`x86-64-v3`):** Sets `RUSTFLAGS="-C target-cpu=x86-64-v3"` along with `CFLAGS` and `CXXFLAGS` set to `-march=x86-64-v3 -O3` for C/C++ build dependencies (targeting CPUs with AVX/AVX2, FMA, BMI1/BMI2, and MOVBE support such as Intel Haswell / AMD Excavator or newer).
* **Release Profile Overrides:** Uses `CARGO_PROFILE_RELEASE_LTO="fat"`, `CARGO_PROFILE_RELEASE_CODEGEN_UNITS="1"`, `CARGO_PROFILE_RELEASE_PANIC="abort"`, and `CARGO_PROFILE_RELEASE_OPT_LEVEL="3"` via environment variables rather than mutating `Cargo.toml`, avoiding interference with proc-macros and build scripts.
* **Symbol Stripping & Caching:** Strips compiled binaries with `strip` before archiving into `misterdownloadmanager-linux-x86_64-v3.tar.gz` and caches `~/.cargo/registry`, `~/.cargo/git`, and `target/` keyed by `Cargo.lock`.

---

## Triggers & Release Behavior

* **Push to `main`:** Runs on commits to `main` except changes under `website/**`, `docs/**`, or `**/*.md`.
* **Daily Schedule:** Runs automatically once per day at `00:00 UTC` (`0 0 * * *`) to track upstream `main`.
* **Manual Dispatch:** Can be triggered manually from the **Actions** tab via `workflow_dispatch`.
* **Release Tagging:** Publishes `misterdownloadmanager-linux-x86_64-v3.tar.gz` (and uploads a workflow artifact) under tag `build-<run_number>` with title `Optimized x64-v3 Build #<run_number>` (`contents: write` permission required).

---

## System Requirements & Notes

* **CPU Support:** Requires an `x86_64` processor supporting `x86-64-v3` instructions; older CPUs may fail with `SIGILL` (illegal instruction).
* **Runtime Dependencies:** Although build dependencies (`libgtk-3-dev`, `libwayland-dev`, `cmake`, `libclang-dev`, `nasm`, etc.) are installed on the Ubuntu build runner, running the compiled binary on your Linux distro will require matching runtime libraries (such as GTK 3 / WebKitGTK / AppIndicator packages depending on your desktop environment).
