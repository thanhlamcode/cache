# 🚀 Speed Up Your System with RAMDisk (Rust Build + Chrome Cache)

## 🧩 Goal

Use **RAMDisk (drive R:)** to speed up Rust build performance and reduce disk usage while running Chrome.

---

## ⚙️ Part 1 — Use RAMDisk for **Rust Build**

### 🔧 Step 1: Install ImDisk Virtual Disk Driver

Download and install **[ImDisk Virtual Disk Driver](https://sourceforge.net/projects/imdisk-toolkit/)**.

After installation, you can create a RAMDisk easily via the ImDisk control panel.

### 🔧 Step 2: Create necessary folders on RAMDisk

#### **Automated Method (Recommended)**

Run this PowerShell script to create all necessary folders if they don't exist:

```powershell
$folders = @("R:\Temp", "R:\cargo_target", "R:\ChromeCache")

foreach ($f in $folders) {
    if (-not (Test-Path $f)) {
        New-Item -Path $f -ItemType Directory | Out-Null
        Write-Host "✅ Created $f"
    } else {
        Write-Host "ℹ️ Exists: $f"
    }
}
```

#### **Manual Method**

Alternatively, manually create the folders in your RAMDisk drive `R:`:

```
R:\cargo_target\
R:\ChromeCache\
R:\Temp\  (optional)
```

### 🔧 Step 3: Configure Cargo to build into RAMDisk

Open PowerShell or CMD and run one of the following commands:

#### **PowerShell**

```powershell
New-Item -Path "$env:USERPROFILE\.cargo\config.toml" -ItemType File -Force; Add-Content -Path "$env:USERPROFILE\.cargo\config.toml" -Value "[build]`ntarget-dir = 'R:\\cargo_target'"
```

#### **CMD**

```cmd
echo [build]> "%USERPROFILE%\.cargo\config.toml" && echo target-dir = "R:\\cargo_target">> "%USERPROFILE%\.cargo\config.toml"
```

📁 The configuration file will be created at:

```
C:\Users\<username>\.cargo\config.toml
```

### ✅ Result

* All Rust projects (`cargo build`, `cargo test`, …) will build into `R:\cargo_target`.
* Faster build times and reduced SSD/HDD writes.

---

## 🌐 Part 2 — Use RAMDisk for **Chrome Cache**

### 🧱 Preparation

The `R:\ChromeCache\` folder should already be created in **Step 2** of Part 1. If you skipped that step, create it manually or run the automated script from Part 1.

---

### ⚙️ Option 1 — Create a dedicated Chrome Shortcut

Run this command (works in PowerShell or CMD):

```$s = (New-Object -COM WScript.Shell).CreateShortcut("C:\Users\dtlam\OneDrive\Desktop\Chrome_RAMDisk.lnk")
$s.TargetPath = "C:\Program Files\Google\Chrome\Application\chrome.exe"
$s.Arguments = "--disk-cache-dir=`"R:\ChromeCache`""
$s.Save()
```

📌 This creates a **Chrome_RAMDisk** shortcut on your Desktop.
When launched, Chrome stores its cache in the RAMDisk — faster page loading and lower Disk usage.

---

### ⚙️ Option 2 — Use **Symbolic Link (mklink)** for a permanent setup

> ⚠️ Run these commands in **Command Prompt (Run as Administrator)** — not PowerShell.

```cmd
rmdir "%LocalAppData%\Google\Chrome\User Data\Default\Cache" /s /q
mklink /D "%LocalAppData%\Google\Chrome\User Data\Default\Cache" "R:\ChromeCache"
```

📄 After you see:

```
symbolic link created for ... <<===>> R:\ChromeCache
```

→ the setup succeeded.

---

## 🧠 Summary Table

| Component       | Path on RAMDisk   | Notes                              |
| --------------- | ----------------- | ---------------------------------- |
| Rust build      | `R:\cargo_target` | 3–5x faster build speed            |
| Chrome cache    | `R:\ChromeCache`  | Lower Disk usage, faster page load |
| (Optional) TEMP | `R:\Temp`         | Reduce temporary writes on drive C |

---

## 🔁 Optional: Auto-mount RAMDisk on Startup

In **ImDisk**, go to:
**File → Save Image File and Mount Settings → Mount at Windows startup**
→ Windows will recreate drive `R:` automatically every time it starts.

---

## ✅ Final Notes

* Create an 8 GB RAMDisk using ImDisk.
* Configure Cargo and Chrome to use the RAMDisk for builds and cache.
* Enjoy noticeably faster Rust builds, smoother Chrome browsing, and stable
