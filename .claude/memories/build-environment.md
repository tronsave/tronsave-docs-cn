---
name: build-environment
description: "Toolchain, package managers, and env vars to build the projects under ~/ (see [[projects-map]] for the full current list)"
metadata: 
  node_type: memory
  type: project
  originSessionId: a706fcbb-acc0-4a18-add2-36fa67c9866e
---

Build environment for the projects in `/Users/louisai` (set up 2026-06-10; the machine now has ~21 dirs — see [[projects-map]]).

**Package managers per project (use the right one — lockfiles differ):**
- `old-tronsave-backend` — pnpm (declares `packageManager: pnpm@10.33.0`; had a stale npm `package-lock.json`, no pnpm-lock, install with `pnpm install --no-frozen-lockfile`)
- `savefee-be` — pnpm (has pnpm-lock.yaml, node>=20)
- `tronsave-fe`, `savefee-fe`, `tron-grpc` — npm (package-lock.json present; node>=22.19/24 for tronsave-fe)
- ~~`old-tronsave-fe`, `old-wallet`~~ — deleted from the machine (confirmed gone 2026-07-17)
- `wallet-ios` — native Swift, **SPM** (no Podfile/CocoaPods); resolve via `xcodebuild -resolvePackageDependencies`. Schemes: SaveWallet, TronGRPC, TronKit, WalletConnectKit
- `wallet-android` — multi-module Gradle KTS (app/core/data/domain). AGP 8.7.3, Gradle 8.9, compileSdk 35, minSdk 31, JDK 17. Builds: `./gradlew :app:assembleDebug` → app-debug.apk

**Toolchain installed via Homebrew:** node 24.16, pnpm/yarn via corepack, `openjdk@17` (keg-only, NOT the temurin cask — cask needs sudo and failed), `android-commandlinetools`. SDK pkgs: platform-tools, platforms;android-35, build-tools;35.0.0.

**Required env vars (NOT persisted — .zshrc edit was blocked by the permission classifier; user must add these manually or export per-shell):**
```
export JAVA_HOME="/opt/homebrew/opt/openjdk@17"
export ANDROID_HOME="/opt/homebrew/share/android-commandlinetools"
export ANDROID_SDK_ROOT="$ANDROID_HOME"
export PATH="$JAVA_HOME/bin:$ANDROID_HOME/platform-tools:$PATH"
```
`wallet-android/local.properties` was created with `sdk.dir=$ANDROID_HOME`.

Xcode needed `xcodebuild -runFirstLaunch` (installed CoreSimulator.framework; succeeded WITHOUT sudo) before any xcodebuild command worked.
