# Day 4 - Intro to the CLI

- To connect a Cisco device via the RJ45 console port you use a **Rollover Cable**

- Cisco recommends using the following settings for connecting to a Cisco device console port:

    - **Baud rate:** 9600 baud
    - **Data bits:** 8
    - **Parity:** None
    - **Stop bits:** 1

## User EXEC Mode (User Mode)

- When you first enter the CLI you will be in `User EXEC Mode`
- User EXEC Mode is indicated by the "greater than sign": `Router>` - `hostname of the device>`
- Very limited: can look at some things, but cannot make configs

## Privileged EXEC Mode
- If you write `enable` you enter Privileged EXEC Mode.
- Privileged EXEC Mode is indicated by the "greater than sign": `Router#` - `hostname of the device#`
- It provides complete access to view the device's configuration, restart device, et.
- Cannot change the configuration, but can change the time on the device, save the config file, etc.
- `exit` to return to Privileged EXEC Mode

## Global Configuration Mode
- If you type `configure terminal` you will enter **Global Configuration Mode**
- `exit` to return to Privileged EXEC Mode

### enable password
- protect privileged exec mode with a password
- `enable password <password>` (CASE SENSITIVE)
- if you enter password wrong 3 times you are denied access for having **%Bad Secrets**
- the password is in plain-text on the config, which is bad

#### service password-encryption
- in global configuration mode
- the password in the config becomes displayed in an encrypted way 
- there are a kit if crackers online
- better, but not secure
- **7** indicates Cisco's proprietary hash

#### enable secret
- `enable secret <secret_name>`
- if both enable secret and enable password are used, the second one is ignored
- **5** indicates MD5

## Cisco IOS CLI

- You can type `?` to show the commands
    - can be used after prefixes to know what commands exist
- **Autocomplete**: 
    - en + tab => enable
    - en => enable (works if there are no commands that start with that preffix)

#### Cancel command
- type no before a command

## Running-Config / Startup-config

- **Running Config:** current active config file on the device. Changes as commands are entered in the CLI
    - you can view it with `show running-config`

- **Startup-Config:** the configuration file that will be loaded upon restart of the device.
    - you can view it with `show startup-config` (but you need to save the running config first)

### Saving the config

- Running `write` in privileged EXEC mode
- Running `write memory` in privileged EXEC mode
- Running `copy running-config startup-config` in privileged EXEC mode