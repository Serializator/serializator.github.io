---
layout: post
title:  Managing your Homebrew packages on MacOS
tags: macos homebrew
---

Do you know what you services you are running or what tools are installed on your macbook? How many
packages do you think are installed?

[Homebrew Bundle](https://github.com/Homebrew/homebrew-bundle) is a way to manage your Homebrew packages
repeatably and predictably. This helps us to manage our Homebrew packages in an automated fashion.

# Bundle

A bundle you create using the `brew bundle` command. This will let you create a dump of the taps,
packages and casks currently present on your system.

This dump we will later use to bring the system in sync at a regular interval.

```bash
$ brew bundle dump --file ~/Brewfile
```

If you want to add anything later, you can do this two ways.

- Install the package and create a new dump
- Add the entry to `~/Brewfile` and re-install

Re-installing from the `~/Brewfile` manually can be done with this command.
```bash
$ brew bundle install --file ~/Brewfile --clean
```

# + Launchd

[Launchd](https://support.apple.com/guide/terminal/script-management-with-launchd-apdc6c1077b-5d5d-4d35-9c19-60f2397b2369/mac) will help
us to bring the system in sync on a set interval. This way, any ad-hoc installs of packages will be reverted at the end of every day.

Replace `[user]` with the username on your machine and save it to  
`~/Library/LaunchAgents/sh.brew.bundle_sync.plist`

Use this command to load the service.  
`launchctl load -w ~/Library/LaunchAgents/sh.brew.bundle_sync.plist`.

```xml
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>sh.brew.bundle_sync</string>
  
    <key>Program</key>
    <string>/usr/local/bin/brew</string>
  
    <key>ProgramArguments</key>
    <array>
      <string>/usr/local/bin/brew</string>
      <string>bundle</string>
      <string>install</string>
      <string>--file</string>
      <string>/Users/[user]/Brewfile</string>
      <string>--clean</string>
    </array>
  
    <key>StandardOutPath</key>
    <string>/Users/[user]/Brewfile.launchd.stdout</string>
  
    <key>StandardErrorPath</key>
    <string>/Users/[user]/Brewfile.launchd.stderr</string>
  
    <key>StartCalendarInterval</key>
    <dict>
      <key>Hour</key>
      <integer>10</integer>
    </dict>
  </dict>
</plist>
```
