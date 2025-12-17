bing-wallpaper-macos
Automatically update Bing wallpapers on macOS, with support for multiple monitors.

📌 Principle
Bing wallpapers are downloaded to the ~/.local/bing-wallpaper-macos directory.

The last update date is recorded in the same directory.

Old wallpaper files older than 30 days will be removed automatically.

📥 Installation
Homebrew
zsh
brew tap moesnow/tools
brew install bing-wallpaper-macos
🚀 Usage
Automatically update wallpapers
zsh
brew services start bing-wallpaper-macos
Manually update wallpapers
zsh
bing-wallpaper-macos
Optional parameters
zsh
Usage: bing-wallpaper-macos [options]
Options:
  --auto     : Check if program run today
  --version  : Display program version
  --help     : Display this help message
⚙️ Launchd Service (Alternative to Homebrew services)
Copy binary to /usr/local/bin:

zsh
sudo cp bing-wallpaper-macos /usr/local/bin/
sudo chown $USER:staff /usr/local/bin/bing-wallpaper-macos
chmod +x /usr/local/bin/bing-wallpaper-macos
Create plist file at ~/Library/LaunchAgents/com.bing.wallpaper.plist:

xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <!-- 唯一标识 -->
    <key>Label</key>
    <string>com.bing.wallpaper</string>

    <!-- 程序和参数 -->
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/bing-wallpaper-macos</string>
        <string>--auto</string>
    </array>

    <!-- 开机自动运行 -->
    <key>RunAtLoad</key>
    <true/>

    <!-- 每天早上 8 点运行一次 -->
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>8</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>

    <!-- 日志输出 -->
    <key>StandardOutPath</key>
    <string>/tmp/bing-wallpaper.out</string>
    <key>StandardErrorPath</key>
    <string>/tmp/bing-wallpaper.err</string>
</dict>
</plist>

Load service:

zsh
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.bing.wallpaper.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.bing.wallpaper.plist
launchctl enable gui/$(id -u)/com.bing.wallpaper
🛠 Compilation
zsh
brew install nlohmann-json
git clone https://github.com/moesnow/bing-wallpaper-macos
cd bing-wallpaper-macos
make
🐞 Troubleshooting
1. Bootstrap failed: 5: Input/output error
Cause: Program path invalid, file not executable, wrong owner, or log path not writable.

Fix:

zsh
sudo chown $USER:staff /usr/local/bin/bing-wallpaper-macos
chmod +x /usr/local/bin/bing-wallpaper-macos
plutil -lint ~/Library/LaunchAgents/com.bing.wallpaper.plist
2. Load failed: 5: Input/output error
Cause: Using deprecated launchctl load.

Fix: Use launchctl bootstrap instead.

3. No such file or directory: nlohmann/json.hpp
Cause: Include path not set correctly.

Fix: Add -I/opt/homebrew/include to Makefile.

4. Failed to connect to destination URL
Cause: Network issue or wrong country_code.

Fix: Check config file ~/.local/bing-wallpaper-macos/config.json.