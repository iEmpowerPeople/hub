
# Codelayer for Windows

CodeLayer is currently only macos native. Windows compatability coming soon..

Until then here is the most friendly solution chatgpt and I could come up with.

GOAL: User friendly experience for non-technical person.
DISCLAIMER: I havent done this myself. I use macos.

## CodeLayer UI on Windows via a remote hosted macOS machine

### 1.1 Installation steps (remote Mac approach: Windows as a thin client)

* Rent a hosted Mac (recommended simplest: MacStadium “Mac mini”), then remote into its macOS desktop from Windows using a VNC client. [1]
* On the remote Mac, install CodeLayer using one of the official macOS methods:

  * Homebrew cask: `brew install --cask --no-quarantine humanlayer/humanlayer/codelayer` [2]
  * Or download the DMG from GitHub releases and drag the app into Applications. [3]
* Launch CodeLayer on the remote Mac and do all agent work there (Claude Code integration, prompts, etc.). [2]

### 1.1.1 Explicit “do this” steps (one concrete provider + apps)

* Provider: MacStadium (hosted Mac mini). Remote desktop app on Windows: RealVNC “VNC Viewer” (MacStadium explicitly recommends it for accessing the Mac desktop). [1]
* Connect: install VNC Viewer on Windows, enter the MacStadium connection details, log in, and you will see the macOS desktop in a window. [1]
* Install CodeLayer on that macOS desktop via Homebrew cask (above) or DMG release method (above). [4]

(Alternative provider with official docs: AWS EC2 Mac. GUI access is via Apple Remote Desktop/VNC after initial setup; it is usually more setup-heavy than a managed Mac desktop provider.) [5]

### 1.2 Git (local filesystem) vs GitHub (remote) in the remote Mac setup

* Local git working copy should live on the remote Mac’s disk. In this setup, “local” means “local to the Mac you are remoting into”; Windows is only keyboard/screen. GitHub remains the remote (push/pull unchanged). 
* Do not try to treat your Windows filesystem as the working directory for CodeLayer running on a remote Mac. The simplest rule is: edit/commit/push on the Mac; use GitHub as the sync point back to Windows (separately clone on Windows if needed).
* WSL is not required for this approach. If you also use WSL, it should be for separate Windows-side workflows, not for sharing the same live working tree with the remote Mac.

[1]: https://macstadium.com/blog/accessing-your-mac-mini-from-anywhere?utm_source=chatgpt.com "Accessing Your Mac mini From Anywhere"
[2]: https://humanlayer.dev/docs/introduction?utm_source=chatgpt.com "Introduction"
[3]: https://github.com/humanlayer/humanlayer/releases?utm_source=chatgpt.com "Releases · humanlayer/humanlayer"
[4]: https://github.com/humanlayer/homebrew-humanlayer?utm_source=chatgpt.com "HumanLayer Homebrew Tap"
[5]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-to-mac-instance.html?utm_source=chatgpt.com "Connect to your Mac instance using SSH or a GUI"