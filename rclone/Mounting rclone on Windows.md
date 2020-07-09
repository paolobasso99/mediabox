# Mounting rclone in Windows

1. Download rclone for windows.
2. Download **WinFsp** and select all of the options during installation
3. Download the **nssm tool**
4. Add rclone and nssm to PATH envirorment variables
5. Open CMD, but not as an Admin
    * Type `nssm install` to open the GUI - if this throws an error then change directories within CMD to the rclone folder that is holding the `nssm.exe` file
    * Type the following under Applications:
    * Path: `C:\path\to\rclone\rclone.exe`
    * Startup Directory: `C:\path\to\rclone`
    * Arguments: 
      * VFS: `mount __CLOUDNAME__: X: --config "C:\Users\__USERNAME__\.config\rclone\rclone.conf" --allow-other --buffer-size 1G --dir-cache-time 96h --log-level INFO --user-agent rcloneapp --fast-list --vfs-read-chunk-size 32M --vfs-read-chunk-size-limit off --vfs-cache-mode writes`
    * Service Name: `rcloneMount`
    * Under Exit type: 10000 ms, Select the Restart application setting
6. You should now see a folder that says **CLOUDNAME** (X:) under your Windows Explorer. If not, type `nssm start rcloneMount`
7. Right click on the drive then Proprities -> Customise -> Optimize for: **Documents** (Aply for all sub folders)
