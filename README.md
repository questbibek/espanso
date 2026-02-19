# Espanso Config Setup

This guide explains how to reset and restore your Espanso config folder from a Git repository.

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
Remove-Item -Recurse -Force "C:\Users\unrav\AppData\Roaming\espanso"
```

---

### 3. Clone Your Config Repository

Navigate to the Roaming folder and clone your repo as the `espanso` folder:

```powershell
cd "C:\Users\unrav\AppData\Roaming"
git clone <YOUR_REPO_URL> espanso
```

> Replace `<YOUR_REPO_URL>` with the URL of your Git repository.  
> The folder name `espanso` at the end ensures the repo is cloned into the correct directory.

---

### 4. Restart Espanso

```powershell
espanso restart
```

---

## Full Script (One-Shot)

```powershell
espanso stop
Remove-Item -Recurse -Force "C:\Users\unrav\AppData\Roaming\espanso"
cd "C:\Users\unrav\AppData\Roaming"
git clone <YOUR_REPO_URL> espanso
espanso restart
```

---

## Notes

- Make sure Espanso is fully stopped before removing the folder to avoid file-lock issues.
- If `espanso restart` doesn't work right away, try launching Espanso manually from the Start Menu or running `espanso start`.
- To keep your config backed up, remember to `git push` after making changes to your match/config files.
