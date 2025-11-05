# 🚀 Speed Up Your System with RAMDisk

## 🚀 Goal

Move temporary directories for:

* **Windows / PowerShell Temp**
* **Chrome Cache**
* **Tauri build** (Rust target + Vite/Node cache)

→ to drive **R:** (RAMDisk)

---

## ⚙️ 1️⃣ Create necessary folders on RAMDisk

Run in **PowerShell (Admin)**:

```powershell
$folders = @(
    "R:\Temp",
    "R:\ChromeCache",
    "R:\cargo_target",
    "R:\tauri_cache",
    "R:\npm_cache"
)
foreach ($f in $folders) {
    if (-not (Test-Path $f)) { 
        New-Item -Path $f -ItemType Directory | Out-Null
        Write-Host "✅ Created $f" 
    } else { 
        Write-Host "ℹ️ Exists: $f" 
    }
}
```

---

## ⚙️ 2️⃣ Set TEMP and TMP to RAMDisk

Run in **PowerShell (Admin)**:

```powershell
setx TEMP "R:\Temp" /M
setx TMP "R:\Temp" /M
```

---

## 🔁 3️⃣ Restart your computer

**Restart your computer** so Windows & PowerShell recognize the new paths.

> ⚠️ **Note**: After restarting, the environment variables `TEMP` and `TMP` will point to `R:\Temp`.

---

## ⚙️ 4️⃣ Configure Tauri + Rust build cache to RAMDisk

Tauri relies on Cargo and Node/Vite, so we need to configure:

* **Cargo build output**
* **npm/yarn/pnpm cache**
* **Vite/Tauri cache**

### 🔧 4.1 Cargo target dir

Run in PowerShell:

```powershell
$cargoConfig = "$env:USERPROFILE\.cargo\config.toml"
New-Item -Path $cargoConfig -ItemType File -Force | Out-Null
Add-Content -Path $cargoConfig -Value "[build]`ntarget-dir = 'R:\\cargo_target'"
Write-Host "✅ Cargo target directory set to R:\cargo_target"
```

### 🔧 4.2 npm/yarn/pnpm cache

#### **npm cache**

```powershell
npm config set cache "R:\npm_cache"
```

#### **yarn cache**

```powershell
yarn config set cacheFolder "R:\npm_cache\yarn"
```

#### **pnpm cache**

```powershell
pnpm config set store-dir "R:\npm_cache\pnpm"
```

### 🔧 4.3 Vite cache (Tauri)

Create a `.env` file in your Tauri project:

```env
VITE_CACHE_DIR=R:\tauri_cache
```

Or set a system-wide environment variable:

```powershell
setx VITE_CACHE_DIR "R:\tauri_cache" /M
```

---

## 🌐 5️⃣ Configure Chrome Cache to RAMDisk

### ⚙️ Option 1 — Create Chrome Shortcut with RAMDisk

Run in PowerShell:

```powershell
$s = (New-Object -ComObject WScript.Shell).CreateShortcut("$env:USERPROFILE\OneDrive\Desktop\Chrome_RAMDisk.lnk")
$s.TargetPath = "C:\Program Files\Google\Chrome\Application\chrome.exe"
$s.Arguments = "--disk-cache-dir=`"R:\ChromeCache`""
$s.Save()
Write-Host "✅ Chrome shortcut created on OneDrive Desktop (Chrome_RAMDisk)"
```

📌 Use the **Chrome_RAMDisk** shortcut to launch Chrome with cache on RAMDisk.

### ⚙️ Option 2 — Symbolic Link (mklink) — Permanent setup

> ⚠️ Run in **Command Prompt (Run as Administrator)** — not PowerShell.

```cmd
rmdir "%LocalAppData%\Google\Chrome\User Data\Default\Cache" /s /q
mklink /D "%LocalAppData%\Google\Chrome\User Data\Default\Cache" "R:\ChromeCache"
```

📄 After you see:

```
symbolic link created for ... <<===>> R:\ChromeCache
```

→ setup succeeded.

---

## 🧠 Summary Table

| Component           | Path on RAMDisk      | Benefits                                    |
| ------------------- | -------------------- | ------------------------------------------- |
| Windows Temp        | `R:\Temp`            | Reduced disk writes, faster temp processing |
| Chrome cache        | `R:\ChromeCache`     | Faster page loading, lower disk usage       |
| Rust build          | `R:\cargo_target`    | 3–5x faster build speed                     |
| npm/yarn/pnpm cache | `R:\npm_cache`       | Faster package installation                 |
| Vite/Tauri cache    | `R:\tauri_cache`     | Faster Tauri builds                         |

---

## 🔁 Optional: Auto-mount RAMDisk on Startup

In **ImDisk**, go to:

**File → Save Image File and Mount Settings → Mount at Windows startup**

→ Windows will automatically recreate drive `R:` every time it starts.

---

## ✅ Final Notes

* Create an 8–16 GB RAMDisk using ImDisk (depending on RAM and needs).
* Configure all temporary directories and cache to RAMDisk.
* Enjoy faster Rust/Tauri builds, smoother Chrome browsing, and reduced SSD/HDD writes.

---

## 📚 References

* [ImDisk Virtual Disk Driver](https://sourceforge.net/projects/imdisk-toolkit/)
* [Cargo Configuration](https://doc.rust-lang.org/cargo/reference/config.html)
* [Tauri Documentation](https://tauri.app/)
