# Fail2Ban Installation and Configuration on Proxmox 8

## Installation

1. **Install Fail2Ban:**

    ```bash
    sudo apt-get update
    sudo apt-get install fail2ban
    ```

2. **Install UFW (Uncomplicated Firewall):**

    ```bash
    sudo apt-get install ufw
    ```

3. **Configure UFW:**

    ```bash
    sudo ufw allow 8006
    sudo ufw allow 443
    sudo ufw allow 80
    sudo ufw allow 69 (Replace with your ssh port) 
    sudo ufw deny from any to any port 22
    sudo ufw enable
    ```

## Configuration

4. **Create `jail.local` configuration file:**

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

5. **Create `proxmox.conf` filter file:**

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

6. **Restart Fail2Ban:**

    ```bash
    sudo systemctl restart fail2ban
    ```

## Usage

Fail2Ban is now configured to monitor the specified ports and log files for suspicious activity. If an IP address exceeds the defined threshold, it will be automatically banned for the specified duration.

UFW is configured to allow traffic on ports 8006, 443, 80, and 11797 while denying SSH access to root. Adjust the configuration parameters as needed for your specific requirements.
