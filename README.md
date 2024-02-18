# Fail2Ban Installation and Configuration on Proxmox 8 by Prime Murcia


## Installation

1. **Install Fail2Ban:**

    ```bash
    sudo apt-get update
    sudo apt-get install fail2ban
    ```

## Configuration

2. **Create `jail.local` configuration file:**

    Open the `jail.local` file for editing:

    ```bash
    sudo nano /etc/fail2ban/jail.local
    ```

    Add the following configuration for Proxmox:

    ```ini
    [proxmox]
    enabled = true
    port = https,http,8006
    filter = proxmox
    banaction = iptables
    backend   = systemd
    maxretry  = 5
    findtime  = 1d
    bantime   = 1d

    [sshd]
    enabled   = true
    filter    = sshd
    banaction = iptables
    backend   = systemd
    maxretry  = 3
    findtime  = 1d
    bantime   = 1d
    ```

3. **Create `proxmox.conf` filter file:**

    Open the `proxmox.conf` file for editing:

    ```bash
    sudo nano /etc/fail2ban/filter.d/proxmox.conf
    ```

    Add the following configuration:

    ```ini
    [Definition]
    failregex = pvedaemon\[.*authentication (verification )?failure; rhost=<HOST> user=.* msg=.*
    ignoreregex =
    ```

4. **Restart Fail2Ban:**

    ```bash
    sudo systemctl restart fail2ban
    ```

## Usage

Fail2Ban is now configured to monitor the specified ports and log files for suspicious activity. If an IP address exceeds the defined threshold, it will be automatically banned for the specified duration.

You can check Fail2Ban's status and view banned IP addresses with the following command:

```bash
sudo fail2ban-client status
sudo fail2ban-client status proxmox
