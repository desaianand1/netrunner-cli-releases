# netrunner

netrunner installs and maintains a large Cyberpunk 2077 mod list through Mod Organizer 2. You give it a list of Nexus Mods links. It looks up every mod, downloads the files, installs them into MO2, sets the load order and checks that everything stayed in sync.

> **Early access for testers.** netrunner is being tested with a small group before a wider release. Expect rough edges, and please report anything that goes wrong (see [Reporting a bug](#reporting-a-bug)).

## Requirements

- Windows 10 version 1809 or later, 64-bit.
- Cyberpunk 2077 from Steam, GOG or Epic, launched once without mods.
- [Mod Organizer 2](https://github.com/ModOrganizer2/modorganizer/releases) 2.5.2, with an instance for Cyberpunk 2077.
- A [Nexus Mods](https://www.nexusmods.com) Premium account and its personal API key. Free accounts cannot download through the API.
- An OpenRouter API key, optional. Without it, netrunner skips the steps that read mod descriptions and installer options with a language model.

The installer sets up the rest: Bun, [7-Zip](https://www.7-zip.org) and netrunner itself. `netrunner init` installs the [MO2 API Bridge](https://github.com/Vhodnoylogin/MO2-ApI-Bridge) plugin that netrunner uses to control MO2.

## Install

Open PowerShell and run:

```powershell
irm https://github.com/desaianand1/netrunner-cli-releases/releases/latest/download/install.ps1 | iex
```

The installer checks your system, installs Bun and 7-Zip if they are missing, verifies the download's checksum and installs netrunner. Running it again when you are up to date changes nothing. It offers to run `netrunner init` at the end.

**Manual install.** Download the `.tgz` and `SHA256SUMS` from the [latest release](https://github.com/desaianand1/netrunner-cli-releases/releases/latest), check the hash with `Get-FileHash .\netrunner-<version>.tgz`, then run `bun add -g .\netrunner-<version>.tgz`. This needs Bun 1.3.14 or later.

If you downloaded `install.ps1` instead of piping it, run it with `powershell -ExecutionPolicy Bypass -File .\install.ps1`.

## Quick start

1. **Set up.** Run `netrunner init`. It finds MO2, the game and 7-Zip, asks for your API keys, installs the bridge plugin and fixes the MO2 settings netrunner needs.
2. **Start MO2.** netrunner talks to MO2 while it is running.
3. **Check.** Run `netrunner preflight`. Every failure comes with the command or step that fixes it.
4. **Add mods.** Run `netrunner open mods` and paste one Nexus Mods link per line.
5. **Install.** Run `netrunner setup`. It looks up your mods, lets you review them, downloads them and installs them into MO2. Cyber Engine Tweaks and RED4ext go straight into the game folder, because they load before MO2 can provide them. It stops after each step if something needs your attention, and `netrunner setup --from <step>` picks up where it left off.

When it finishes, start the game from MO2 with the **Cyberpunk 2077** program.

## Commands

| Command                    | What it does                                                                                              |
| -------------------------- | --------------------------------------------------------------------------------------------------------- |
| `netrunner init`           | Set up netrunner, MO2 and the game. Safe to run again to repair a setup.                                  |
| `netrunner setup`          | Run the whole pipeline: init, preflight, enrich, download, install, status.                               |
| `netrunner preflight`      | Check that everything is ready before a run.                                                              |
| `netrunner enrich`         | Look up every mod in your list and resolve its files and requirements.                                    |
| `netrunner download`       | Download the mod files. Resumes where it stopped.                                                         |
| `netrunner install`        | Install the mods into MO2 and set the load order. `--dry-run` shows the plan only.                        |
| `netrunner status`         | Compare your mod list with what is in MO2.                                                                |
| `netrunner update`         | Check Nexus Mods for newer versions.                                                                      |
| `netrunner diagnose`       | Read the game's logs, find what broke and offer fixes. `--restore` undoes them.                           |
| `netrunner backup`         | Create, list or delete snapshots of your MO2 setup.                                                       |
| `netrunner restore <name>` | Restore a snapshot.                                                                                       |
| `netrunner export`         | Write your load order as a report.                                                                        |
| `netrunner clean`          | Remove old downloads, caches, backups, leftover locks or the frameworks netrunner put in the game folder. |
| `netrunner doctor`         | Summarise the state of netrunner, MO2 and the game.                                                       |
| `netrunner open`           | Open the netrunner folder, its settings, the logs or your mod list.                                       |
| `netrunner bridge probe`   | Check that the MO2 bridge answers the way netrunner expects.                                              |

Every command accepts `--help`. `--verbose` writes more detail to the log, and `--yes` answers yes to every confirmation.

## Settings

`netrunner init` writes the settings for you, and rerunning it keeps anything you changed. `netrunner open config` opens them. A typical file looks like this:

```toml
[game]
plugin = "cyberpunk2077"
install_path = "D:/Games/Cyberpunk 2077"

[mo2]
install_path = "D:/MO2"
bridge_port = 8930

[download]
concurrency = 5
```

## Troubleshooting

- **Start with `netrunner doctor`.** It reports on your setup, your mods in MO2, the frameworks in the game folder, leftover locks and the logs.
- **"Start MO2."** netrunner could not reach the bridge. Start MO2 with the instance netrunner uses, then try again.
- **A run was interrupted.** Run the same command again. Downloads and installs pick up where they stopped. If netrunner says another run is in progress and none is, run `netrunner clean locks`.
- **The game crashes or mods do not load.** Run `netrunner diagnose`. It can narrow a crash down to a single mod.
- **Logs.** `netrunner open logs` opens the folder. Each day has its own file, and the last seven are kept.

## Updating

Run the install command again. netrunner also tells you, at most once a day, when a new version is out. Set `NETRUNNER_NO_UPDATE_CHECK=1` to turn that off.

## Reporting a bug

[Open an issue](https://github.com/desaianand1/netrunner-cli-releases/issues/new/choose) and attach the output of `netrunner doctor --json`. It hides your API keys and your user folder. Include your Windows and MO2 versions.

## License

Free for personal, non-commercial use. See [LICENSE](LICENSE).
