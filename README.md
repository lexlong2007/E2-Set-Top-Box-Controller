E2 Set-Top Box Controller
Version 5.12.0 — A single-file Windows GUI for managing Enigma2-based
satellite receivers (set-top boxes) over SSH and the OpenWebIf web API.
Everything the box normally needs a serial console, an FTP client, a Telnet
session, a text editor and a browser for is exposed here in one window:
terminal, file transfer, channel/bouquet editing, EPG management, recording
timers, remote control, signal meters and box logs.
1. Quick Start
1. Copy E2_regui.exe anywhere on your PC (Desktop, USB stick, C:\Tools\…).
No installer, no Python, no extra DLLs required.
2. Double-click it. A splash screen appears, then the main window.
3. Open the Receiver 1 tab, fill in the connection settings, and click
Test Connection.
4. Click Connect. The status pill at the bottom of the window turns green
and the log panel starts printing activity.
5. Work in any tab you like — Receiver 2 is a fully independent second
connection, so you can drive two boxes side by side.
First launch is slow. A single-file EXE self-extracts to a temporary
folder on every start, so expect roughly 5–15 seconds before the splash
disappears. Later launches are usually faster because Windows caches the
payload. If you want a snappier startup, use the onedir build instead
(several files in one folder, starts in about a second).
2. Connection Settings
Open the Receiver 1 (or Receiver 2) tab. The connection card takes
five fields:
Field
Default
Notes
IP Address
(empty)
The box's LAN address, e.g. 192.168.100.7. Leave the port off — it is a separate field.
Port
22
SSH port. OpenWebIf traffic uses 80 on the same host.
User
root
Almost always root on Enigma2 images. Empty falls back to root.
Password
(empty)
The SSH password. Leave blank only if the box accepts empty passwords.
Timeout (s)
15
Per-operation network timeout in seconds. Raise it on slow Wi-Fi bridges.
Buttons to the right of the card:
- Test Connection — checks reachability without opening a session. Reports
success or the exact failure reason.
- Connect — opens the SSH session and starts the status monitor.
- Disconnect — closes it.
Connection states
The status pill and the log panel distinguish three situations on purpose:
- Offline (未连接 / "Not connected") — the box is unreachable. This is
reported as a quiet warning with no pop-up, because a box that is switched
off is not an error.
- Authentication failure — wrong password or insufficient permission.
This always raises a dialog, because retrying with the same credentials
will never help.
- Connected — the session is live and the monitor is polling.
3. What Each Tab Does
Tab
Purpose
Receiver 1 / Receiver 2
Connection settings plus an interactive SSH terminal with quick-command buttons (system info, disk, memory, CPU, network, running processes, services, syslog, package updates, GUI restart, reboot).
Terminal 1 / Terminal 2
Full-screen command terminals, one per receiver, for free-form shell work.
File Transfer
Browse both the local PC and the box side by side, then upload or download files and folders. Supports drag-and-drop from Explorer.
Management Tools
The toolbox: receiver info, EPG assignment wizard, Picon management, EPG providers, and scheduled housekeeping tasks.
Live Control
Current service, now/next programme, zap to a channel, and live signal readouts.
Remote Control
An on-screen remote — navigation keys, numbers, volume, channel, colour buttons, power, and a screenshot capture button.
Recording Timers
Create, edit, enable/disable and delete recording timers on the box.
Signal Meter
Two analogue-style dials: left shows signal strength, right shows signal quality, with target markers.
Box Log
Reads the receiver's own log files (Enigma2 debug/message logs, EPGImport log, plugin log, kernel ring buffer) without leaving the app.
4. Typical Workflows
4.1 Editing channels and bouquets
1. Open Management Tools → Channel Editor (or the live channel tree).
2. The editor loads the box's bouquets (favourites) and the full service list
into memory. Nothing is written until you say so.
3. Rearrange, rename, create or delete bouquets and channels.
4. Click Write back to receiver.
Important: all edits stay in memory until you write them back. If you
switch bouquets with unsaved changes, the app asks first — declining returns
your selection to where it was. Before writing, the app automatically keeps a
timestamped .bak copy of the file it is about to overwrite.
Channel names are not treated as identifiers internally. A channel appearing in
several bouquets gets a unique row ID, and each row remembers the service
reference it maps to — so renaming a channel never causes a deletion to hit
the wrong row.
4.2 EPG mapping
1. Open Management Tools → EPG Assignment.
2. The left list shows the box's channels; the right list shows EPG source
channels. Use the search boxes to filter either side.
3. Select on both sides and press Map Selected.
4. Use Auto Map to let the app propose matches for you.
Auto-mapping is conservative by design. A fuzzy name match is only accepted
when the digit strings agree — CCTV2 will never be mapped onto CCTV1,
however similar the names look. Star ratings in the candidate list only tell
you how confident the match is; they never override the safety rules. If a
language is selected, same-name entries in other languages are filtered out so
the language you asked for wins.
4.3 EPG providers (epgimport)
1. Open Management Tools → EPG Providers.
2. Each provider is a *.sources.xml file on the box. You can create a new
one, or download a provider (e.g. Rytec) from the internet.
3. Add it to the enabled list, then trigger an import.
Downloaded sources are validated before they are written — if the source site
returns an error page instead of XML, the app refuses to overwrite your
existing providers.
This image has no HTTP endpoint for triggering an EPG import, so an import
is triggered by restarting Enigma2. The import result is then read from the
closeImport line in the EPGImport log.
4.4 Picon management
Picons are the small channel logos Enigma2 shows in lists and the infobar.
1. Open Management Tools → Picon.
2. The app derives the correct picon filename from each channel's service
reference and tells you which files are missing.
3. Upload the matched images; they land in /usr/share/enigma2/picon/.
Non-channel references, and references whose SID/TSID/ONID are all zero, are
skipped — those can never have a picon.
4.5 Box information and logs
Management Tools → Receiver Info combines two sources. What OpenWebIf
exposes (firmware, image, driver date, MAC, IP, disk capacity/free space) is
fetched over HTTP. Everything OpenWebIf does not expose — the real SoC model,
the boot counter, the full openATV image details, uptime, Python and GStreamer
versions — is read over SSH in a single batched command.
Log locations on the box, for reference:
What
Where
Enigma2 debug / message / network logs
/home/root/logs/
EPG import log
/tmp/EPGImport_debug.log
Plugin debug log
/tmp/plugin_Debug.log (absent unless the plugin is enabled — this is normal)
Kernel messages
dmesg
Some boxes simply have no EPG data for certain channels (IPTV channels in
particular). An empty programme list in that case is expected behaviour, not a
failure.
5. Appearance and Language
- 8 colour schemes: light, dark, mist, forest, sepia, violet (light base)
and nord, midnight (dark base). Change them in the settings area; the whole
window repaints, including charts.
- Interface language: Chinese and English are both complete. Switching
language updates the window immediately — no restart needed.
- High-DPI aware: the window is sharp on scaled displays instead of being
bitmap-stretched.
- Window sizing adapts to your desktop work area. On a 1080p screen every
tab fits without scrolling. If your notebook tab strip is too narrow to show
every tab, use the ◀ ▶ arrows at the top of the tab area.
6. Files Created at Runtime
These are created next to the EXE, not in a hidden AppData folder, so the
whole install is portable:
File
Purpose
e2_controller_config.ini
Your saved settings (receivers, theme, language, preferences).
e2_controller_key.bin
Encryption key used to protect stored credentials.
logs/e2_regui_YYYY-MM-DD.log
Application log, rotated at 2 MB, 5 files kept.
To reset the app to defaults, close it and delete the two e2_controller_*
files. Passwords are encrypted with a key stored in the key file; if you move
the EXE to another machine, copy the key file too or you will be asked to
re-enter passwords.
7. Troubleshooting
The window never appears.
The first launch unpacks a large payload. Wait 15–20 seconds. If it still does
not appear, check logs/e2_regui_*.log next to the EXE.
"Not connected" appears as soon as I start.
The box is unreachable at that IP. Confirm the box is powered on, that the IP
is correct, and that your PC is on the same network. This is reported quietly
on purpose — a powered-off box is normal, not a crash.
Authentication failed, repeatedly.
The password is wrong or the account lacks permission. Check the credentials
on the box. The app shows this as an error dialog because retrying will not
help.
Test Connection succeeds but Connect hangs.
Something between you and the box is filtering SSH. Try raising the timeout,
and confirm port 22 (or your custom port) is not blocked by a firewall.
Signal meter shows a bare % or dB with no number.
That channel has no tuner — typical for IPTV channels. The box reports units
without a reading. This is expected.
An operation seems to do nothing.
Check the log panel, then logs/. Failures are limited; identical repeated
failures are collapsed for 120 seconds so a background poll cannot flood the
log window.
A single channel has no EPG.
Normal for IPTV and some niche services. See section 4.5.
8. Building from Source
Requirements: Windows with an official python.org Python (one that includes
tkinter).
build_exe.bat            # onedir build (faster start, ship the whole folder)
build_exe.bat onefile    # single-file build (this release)
Output lands in dist/. The build script installs PyInstaller, ttkbootstrap,
tkinterdnd2, paramiko, cryptography and requests automatically.
9. Requirements and Compatibility
- Host: Windows 10 / 11, 64-bit. No runtime installation needed for the
packaged EXE.
- Receiver: any Enigma2 box with SSH and OpenWebIf available. Validated on
an Octagon SF8008 running openATV 8.0 with Python 3.14, and on
lamedb v4 and v5 channel databases.
- Network: the PC must be able to reach both port 22 (SSH) and port 80
(OpenWebIf) on the receiver.
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2001.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2002.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2003.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2004.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2005.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2006.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2007.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2008.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2009.png)
![image](https://github.com/lexlong2007/E2-Set-Top-Box-Controller/blob/main/e2010.png)
