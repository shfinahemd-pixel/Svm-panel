# SVM Panel

**BEST VPS MANAGE PANEL** - Web-based LXC VPS management panel. **Made by Shafin TW**

## About me

Hi, I'm **Shafin TW**, a YouTuber. I make videos about VPS hosting, servers and panels, and I built
SVM Panel so anyone can run their own VPS hosting from a clean web dashboard.
If this project helps you, subscribe to my channel and star this repo.

## How the panel works

1. **Install** - the installer sets up Python, a virtualenv, a systemd service and (optionally) LXD on your server.
2. **Panel** - a Flask web app runs on port `5000`. The first admin account is created during install.
3. **VPS = LXC containers** - from the panel you pick an OS, CPU, RAM and disk, and the panel creates the
   container through LXD. Without LXD the panel runs but VPS cannot be created.
4. **Users and admins** - admins manage users, nodes and every VPS. Users log in (with optional 2FA) and
   manage only their own VPS.
5. **Daily management** - start, stop, restart, console, file manager, snapshots, backups, port forwarding,
   reinstall, migrate, expiry and suspension, live CPU/RAM/disk stats.
6. **Nodes** - extra machines can be added as nodes using `node.py`, so one panel controls many servers.
7. **Discord bot** - the panel can run a Discord bot so members can deploy and manage VPS from Discord.
8. **API** - a REST API is included for automation (docs inside the panel under Admin -> API).

## Quick install (GitHub)

Run as root on Ubuntu 20.04+ / Debian 11+:

```bash
git clone https://github.com/shfinahemd-pixel/Svm-panel.git
cd Svm-panel
apt install unzip
unzip svm-panel.zip
cd Svm
bash install.sh --with-lxd
```

The installer prints the panel URL (`http://SERVER_IP:5000`) and the admin login at the end.
Drop `--with-lxd` if LXD is already installed (LXD is required to create VPS).

## Options

| Option | Default | Meaning |
|---|---|---|
| `--url URL` | – | Panel archive (`.zip` / `.tar.gz`) to download (curl mode) |
| `--sha256 HASH` | – | Verify the archive before installing |
| `--dir PATH` | `/opt/Svm-panel` | Install location |
| `--port N` | `5000` | Panel port |
| `--name "TEXT"` | `SVM Panel` | Panel name |
| `--admin-user NAME` | `admin` | First admin username |
| `--admin-email MAIL` | `admin@localhost` | First admin email |
| `--with-lxd` | off | Install and initialise LXD (snap) |

Environment variables `SVM_ARCHIVE_URL` and `SVM_ARCHIVE_SHA256` work like `--url` / `--sha256`.

## Manual install (from the zip)

```bash
unzip svm-panel.zip && cd Svm
sudo bash install.sh --with-lxd
```

## After install

- Open `http://SERVER_IP:5000` and log in with the admin user/password printed by the installer
  (they are also in `/opt/Svm-panel/.env`, readable by root only).
- Manage the service:

```bash
systemctl status Svm-panel
journalctl -u Svm-panel -f
systemctl restart Svm-panel
```

## Configuration

Edit `/opt/Svm-panel/.env`, then `systemctl restart Svm-panel`.

| Variable | Meaning |
|---|---|
| `PANEL_NAME` | Name shown in the panel |
| `PORT` / `HOST` | Listen port / address |
| `SECRET_KEY` | Session + encryption key (do not change after install) |
| `YOUR_SERVER_IP` | Public IP shown in the panel |
| `MAIN_ADMIN_USERNAME` / `MAIN_ADMIN_PASSWORD` / `MAIN_ADMIN_EMAIL` | First admin; used only when the database is created |

## Discord bot (deploy VPS from Discord)

Open **Admin -> Discord Bot** in the panel, paste the bot token, your Discord user ID (main admin) and press
**Save & (re)start bot**. Optional: change the bot name, add extra admins, set member limits and the target node.

| Command | Who | What it does |
|---|---|---|
| `/register` `/deploy` `/myvps` `/vpsinfo` `/start` `/stop` `/restart` `/password` `/ostemplates` | members | create an account, deploy and manage their own VPS |
| `/allvps` `/deploy_for` `/delete` `/suspend` `/unsuspend` `/link` `/nodes` `/stats` | bot admins | manage every VPS and user |
| `/addadmin` `/removeadmin` `/botadmins` `/setname` | main admin | manage bot admins and the bot name |

Two engines are included, pick one in **Admin -> Discord Bot -> Bot engine**:

- **Custom bot (`bot.py`)** - prefix commands (`!help`), manages LXC containers directly and keeps its own `vps.db`. Token, bot name, main admin, prefix and server IP come from the panel page. It needs the *Message Content* and *Server Members* intents enabled in the Discord developer portal.
- **Panel bot (`discord_bot.py`)** - slash commands, talks to the panel's REST API so VPS appear in the panel.

The bot runs as a child process of the panel (console log: `bot_console.log`).
It needs `discord.py`, which the installer adds to the venv automatically.

## Video background

A looping animated background (`static/video/bg-loop.mp4`) is enabled by default. Change or disable it in
**Admin -> Settings -> Video background** (any direct `.mp4` URL works).

## Update

Run the same install command again. Panel files are updated; `.env`, the database, backups and uploads are kept.

## Uninstall

```bash
sudo bash /opt/Svm-panel/install.sh uninstall     # removes the service, keeps your data
sudo rm -rf /opt/Svm-panel                        # optional: delete everything
```

## Notes

- The installer must run as root and supports Ubuntu/Debian (apt + systemd).
- Without LXD the panel runs, but VPS cannot be created. Add it later with `sudo bash install.sh --with-lxd`.
- `node.py` is the agent for remote nodes and is installed separately on those machines.
- Logs: `Svm.log` in the install folder and `journalctl -u Svm-panel`.
