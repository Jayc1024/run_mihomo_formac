# run_mihomo_formac



下载mihomo-darwin-arm64-.gz文件，解压重命名并移动到系统路径：

为方便使用，将文件重命名为 mihomo 并移动到 /usr/local/bin/：

```bash
mv mihomo-darwin-arm64-<version> mihomo
chmod +x mihomo
sudo mv mihomo /usr/local/bin/
```

chmod +x 确保文件具有可执行权限。

### 配置 Mihomo

1、创建配置文件目录：

Mihomo 需要一个配置文件（通常为 config.yaml）。创建配置目录：

```bash
sudo mkdir -p /etc/mihomo
```

2、验证 Mihomo 运行：

在终端运行以下命令，测试 Mihomo 是否能正常启动：

```bash
/usr/local/bin/mihomo -d /etc/mihomo
```



### 使用 launchd 注册 Mihomo 为系统服务

macOS 使用 launchd 管理后台服务，类似于 Linux 的 systemd。以下是如何创建 launchd 服务以实现 Mihomo 自启动的步骤。

1. 创建 launchd 配置文件：

   - 在 /Library/LaunchDaemons/ 目录下创建一个 plist 文件，用于定义 Mihomo 服务：

   ```bash
   sudo nano /Library/LaunchDaemons/com.metacubex.mihomo.plist
   ```

   将以下内容粘贴到文件中，并根据需要调整路径或参数：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
       <key>Label</key>
       <string>com.metacubex.mihomo</string>
       <key>ProgramArguments</key>
       <array>
           <string>/usr/local/bin/mihomo</string>
           <string>-d</string>
           <string>/etc/mihomo</string>
       </array>
       <key>RunAtLoad</key>
       <true/>
       <key>KeepAlive</key>
       <true/>
       <key>StandardOutPath</key>
       <string>/var/log/mihomo.log</string>
       <key>StandardErrorPath</key>
       <string>/var/log/mihomo.log</string>
       <key>WorkingDirectory</key>
       <string>/etc/mihomo</string>
   </dict>
   </plist>
   ```

   **说明**：

   - Label：服务的唯一标识符。
   - ProgramArguments：运行 Mihomo 的命令和参数，-d /etc/mihomo 指定配置文件目录。
   - RunAtLoad：设置为 true，表示系统启动时自动运行。
   - KeepAlive：设置为 true，确保服务在崩溃或退出时自动重启。
   - StandardOutPath 和 StandardErrorPath：指定日志输出路径。
   - WorkingDirectory：设置工作目录为配置文件所在目录。

   保存文件并退出（在 nano 中，按 Ctrl+O 保存，Ctrl+X 退出）。

   设置文件权限：

   确保 plist 文件具有正确的权限：

   ```bash
   sudo chmod 644 /Library/LaunchDaemons/com.metacubex.mihomo.plist
   sudo chown root:wheel /Library/LaunchDaemons/com.metacubex.mihomo.plist
   ```

   **加载服务**：

   使用 launchctl 加载服务：

   ```bash
   sudo launchctl load /Library/LaunchDaemons/com.metacubex.mihomo.plist
   ```

   检查服务是否正在运行：

   ```bash
   sudo launchctl list | grep com.metacubex.mihomo
   ```

   如果输出中显示服务的 PID（进程 ID），说明服务已成功启动。

   ### 管理 Mihomo 服务

   停止服务：

   ```bash
   sudo launchctl unload /Library/LaunchDaemons/com.metacubex.mihomo.plist
   ```

   重新加载服务（例如修改配置文件后）：

   ```bash
   sudo launchctl unload /Library/LaunchDaemons/com.metacubex.mihomo.plist
   sudo launchctl load /Library/LaunchDaemons/com.metacubex.mihomo.plist
   ```

   查看服务状态：

   ```bash
   sudo launchctl list | grep com.metacubex.mihomo
   ```

   
