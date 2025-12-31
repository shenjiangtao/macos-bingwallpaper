bing-wallpaper-macos
在 macOS 上自动更新 Bing 壁纸，支持多显示器。

📌 原理
Bing 壁纸会下载到 ~/.local/bing-wallpaper-macos 目录。

最近一次更新日期会记录在该目录。

超过 365 天的旧壁纸文件会自动删除。

📥 安装
Homebrew
zsh
brew tap moesnow/tools
brew install bing-wallpaper-macos
🚀 使用方法
自动更新壁纸
zsh
brew services start bing-wallpaper-macos
手动更新壁纸
zsh
bing-wallpaper-macos
可选参数
zsh
Usage: bing-wallpaper-macos [options]
Options:
  --auto     : 检查今天是否已运行
  --version  : 显示程序版本
  --help     : 显示帮助信息
⚙️ Launchd 服务（替代 Homebrew services）
将二进制文件复制到 /usr/local/bin：

zsh
sudo cp bing-wallpaper-macos /usr/local/bin/
sudo chown $USER:staff /usr/local/bin/bing-wallpaper-macos
chmod +x /usr/local/bin/bing-wallpaper-macos
创建 plist 文件 ~/Library/LaunchAgents/com.bing.wallpaper.plist：

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

加载服务：

zsh
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.bing.wallpaper.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.bing.wallpaper.plist
launchctl enable gui/$(id -u)/com.bing.wallpaper
🛠 编译
zsh
brew install nlohmann-json
git clone https://github.com/shenjiangtao/macos-bingwallpaper.git
cd bing-wallpaper-macos
make
🐞 常见问题排查
1. Bootstrap failed: 5: Input/output error
原因： 程序路径错误、文件不可执行、权限不正确或日志路径不可写。

解决方法：

zsh
sudo chown $USER:staff /usr/local/bin/bing-wallpaper-macos
chmod +x /usr/local/bin/bing-wallpaper-macos
plutil -lint ~/Library/LaunchAgents/com.bing.wallpaper.plist
2. Load failed: 5: Input/output error
原因： 使用了过时的 launchctl load。

解决方法： 改用 launchctl bootstrap。

3. No such file or directory: nlohmann/json.hpp
原因： 编译器没有找到头文件。

解决方法： 在 Makefile 中添加 -I/opt/homebrew/include。

4. Failed to connect to destination URL
原因： 网络问题或配置文件中的 country_code 错误。

解决方法： 检查 ~/.local/bing-wallpaper-macos/config.json。
