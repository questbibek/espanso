# Espanso Config Setup

This guide explains how to reset and restore your Espanso config folder from a Git repository.

---

## ⚡ Easiest: one-line installer

This config repo is normally set up automatically by the **espanso-utility** installer, which clones it to `%APPDATA%\espanso`, installs Git + Espanso, configures the AI scripts, and starts everything. From **PowerShell**:

```powershell
irm https://raw.githubusercontent.com/questbibek/espanso-utility/main/setup-espanso.ps1 | iex
```

Re-running it is safe — it `git pull`s the latest config instead of overwriting. See the [espanso-utility repo](https://github.com/questbibek/espanso-utility) for the full toolkit and trigger list.

The steps below are the **manual** alternative — only needed if you want to reset/restore just this config folder by hand.

---

## Prerequisites

- [Espanso](https://espanso.org/) installed
- [Git](https://git-scm.com/) installed

---

## Steps

### 1. Stop Espanso

```powershell
espanso stop
```

---

### 2. Remove the Existing Config Folder

```powershell
Remove-Item -Recurse -Force "$env:APPDATA\espanso"
```

---

### 3. Clone Your Config Repository

Navigate to the Roaming folder and clone your repo as the `espanso` folder:

```powershell
cd "$env:APPDATA"
git clone https://github.com/questbibek/espanso.git espanso
```

---

### 4. Restart Espanso

```powershell
espanso restart
```

---

## Full Script (One-Shot)

```powershell
espanso stop
Remove-Item -Recurse -Force "$env:APPDATA\espanso"
cd "$env:APPDATA"
git clone https://github.com/questbibek/espanso.git espanso
espanso restart
```

### Single-Line Command (Copy & Paste)

```powershell
espanso stop; Remove-Item -Recurse -Force "$env:APPDATA\espanso"; git clone https://github.com/questbibek/espanso.git "$env:APPDATA\espanso"; espanso restart
```

---

## Notes

- Make sure Espanso is fully stopped before removing the folder to avoid file-lock issues.
- If `espanso restart` doesn't work right away, try launching Espanso manually from the Start Menu or running `espanso start`.

## Espanso auto startup issue

# Espanso Startup & UAC Fix (Windows 11)

## Problem
- Espanso was not auto-starting on boot despite being listed in Task Manager startup apps
- When run manually, a UAC (User Account Control) allow/deny popup appeared

## Solution
Replaced the default startup entry with a Windows Task Scheduler task that runs at login with elevated privileges — bypassing the UAC prompt entirely.

---

## Steps & Commands

### 1. Check if Espanso is running
```powershell
espanso status
```

### 2. Start manually
```powershell
espanso start
```

### 3. Check logs
```powershell
espanso log
```

### 4. Re-register the service
```powershell
espanso service unregister
espanso service register
```

### 5. Create a Task Scheduler task (run PowerShell as Administrator)
```powershell
$espansoPath = "$env:LOCALAPPDATA\Programs\Espanso\espansod.exe"
$action = New-ScheduledTaskAction -Execute $espansoPath -Argument "daemon"
$trigger = New-ScheduledTaskTrigger -AtLogOn -User $env:USERNAME
$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries -ExecutionTimeLimit 0
$principal = New-ScheduledTaskPrincipal -UserId $env:USERNAME -LogonType Interactive -RunLevel Highest
Register-ScheduledTask -TaskName "Espanso" -Action $action -Trigger $trigger -Settings $settings -Principal $principal -Force
```

### 6. Unregister the old startup entry
```powershell
espanso service unregister
```

### 7. Restart your PC
Espanso will now start automatically and silently on every login with no UAC popup.

---

## Why This Works

| Method | UAC Popup | Auto-start |
|---|---|---|
| Default service registration | ✅ Yes (annoying) | ❌ Unreliable |
| Task Scheduler (RunLevel Highest) | ❌ No | ✅ Reliable |

Task Scheduler with `RunLevel Highest` tells Windows to trust the process at login, so it never asks for permission again.
