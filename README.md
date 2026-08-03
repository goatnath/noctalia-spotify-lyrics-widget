# Noctalia Synced Lyrics Plugin

A seamless, time-synced scrolling lyrics panel for the Noctalia desktop shell. It integrates directly into your Noctalia bar and displays a beautifully formatted, auto-scrolling lyrics card when you click the `♫` icon.

## Why it's great
* **Zero Configuration:** No Spotify `sp_dc` cookies, API keys, or web scraping required! It pulls lyrics from public databases like LRCLIB and NetEase automatically.
* **Blazing Fast:** Uses a lightweight Python background daemon that fetches lyrics in parallel across multiple APIs and caches them to your disk so subsequent plays load instantly.
* **Native Shell Integration:** Doesn't feel like a clunky third-party app. It uses Noctalia's native declarative UI framework for buttery smooth, theme-aware rendering.

## Installation

You can install everything with a single copy-paste command. Simply run the following block in your terminal. It will clone the repository to your Downloads folder and set up everything automatically:

```bash
# Move to Downloads and clone the repository
cd ~/Downloads
git clone https://github.com/goatnath/noctalia-spotify-lyrics-widget.git
cd noctalia-spotify-lyrics-widget

# 1. Install Dependencies
sudo pacman -S --noconfirm playerctl
pip install syncedlyrics

# 2. Set up the Background Daemon
mkdir -p ~/.local/bin
cp spotify_lyrics_daemon.py ~/.local/bin/
chmod +x ~/.local/bin/spotify_lyrics_daemon.py

mkdir -p ~/.config/systemd/user
cat << 'EOF' > ~/.config/systemd/user/noctalia-lyrics.service
[Unit]
Description=Noctalia Lyrics Daemon
After=graphical-session.target

[Service]
ExecStart=/usr/bin/python3 %h/.local/bin/spotify_lyrics_daemon.py
Restart=always

[Install]
WantedBy=default.target
EOF

systemctl --user daemon-reload
systemctl --user enable --now noctalia-lyrics.service

# 3. Enable the Noctalia Plugin
mkdir -p ~/.local/share/noctalia/plugins/spotify-lyrics
cp *.luau plugin.toml thumbnail.webp ~/.local/share/noctalia/plugins/spotify-lyrics/ 2>/dev/null || true
cp -r translations ~/.local/share/noctalia/plugins/spotify-lyrics/ 2>/dev/null || true
noctalia msg plugins enable noctalia/spotify-lyrics
# 4. Update the Layout
if [ -f ~/.local/state/noctalia/settings.toml ]; then
    sed -i 's/"media"/"media", "lyrics"/g' ~/.local/state/noctalia/settings.toml
    echo "Added lyrics widget to settings.toml."
else
    echo "Please manually add the 'lyrics' widget to your bar's layout in settings.toml."
fi
```

That's it! Play a song on Spotify (or any MPRIS player) and a `♫` icon will appear in your bar. Click it to view the synced lyrics.
