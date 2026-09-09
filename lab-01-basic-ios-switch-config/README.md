# Lab 01 - Basic IOS & Switch Configuration

## Objective

Practice basic Cisco IOS navigation and configure a Cisco switch using the CLI.

## Device

- Cisco 2960 Switch
- Hostname: `S1`

## IOS Modes Practiced

```text
S1>              User EXEC
S1#              Privileged EXEC
S1(config)#      Global Configuration
S1(config-line)# Line Configuration
```

## Commands Used

```text
enable
configure terminal
hostname S1
enable password CCNA
line console 0
password cisco
login
exit
copy running-config startup-config
show running-config
```

## What I Learned

- `enable` moves from User EXEC mode to Privileged EXEC mode.
- `configure terminal` enters Global Configuration mode.
- `hostname S1` changes the switch hostname.
- `enable password` sets a password for privileged access.
- `line console 0` enters console line configuration mode.
- `login` tells the switch to require the configured console password.
- `exit` moves back one configuration level.
- `copy running-config startup-config` saves the current configuration.
- `running-config` is stored in RAM.
- `startup-config` is stored in NVRAM.

## Verification

I verified the configuration using:

```text
show running-config
```

I also saved the configuration successfully and received:

```text
[OK]
```

## Key Takeaway

Cisco IOS configuration depends heavily on understanding which mode you are currently in. The CLI prompt helps identify the current mode and determines which commands are available.
