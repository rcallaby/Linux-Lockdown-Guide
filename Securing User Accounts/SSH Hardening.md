# SSH Hardening Guide for Linux Servers and Computers

Securing SSH (Secure Shell) access on your Linux servers and computers is crucial to prevent unauthorized access and potential breaches. Below is a comprehensive guide to SSH hardening for Linux environments.

---

## 1. **Update and Patch Your System**
Ensure your system is up-to-date with the latest security patches and updates.

```sh
sudo apt update && sudo apt upgrade -y    # For Debian-based systems
sudo yum update -y                        # For RHEL-based systems
```

## 2. **Disable Root Login**
Prevent direct root login over SSH. This forces users to log in as a normal user and then switch to root using `sudo`.

1. Open the SSH configuration file:

    ```sh
    sudo nano /etc/ssh/sshd_config
    ```

2. Find the line:

    ```plaintext
    PermitRootLogin yes
    ```

3. Change it to:

    ```plaintext
    PermitRootLogin no
    ```

4. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

## 3. **Change the Default SSH Port**
Changing the default SSH port (22) to a non-standard port can reduce the risk of automated attacks.

1. Open the SSH configuration file:

    ```sh
    sudo nano /etc/ssh/sshd_config
    ```

2. Find the line:

    ```plaintext
    #Port 22
    ```

3. Uncomment it and change the port number to a non-standard port (e.g., 2222):

    ```plaintext
    Port 2222
    ```

4. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

5. Update your firewall rules to allow traffic on the new port.

    ```sh
    sudo ufw allow 2222/tcp    # For UFW (Uncomplicated Firewall)
    sudo firewall-cmd --permanent --add-port=2222/tcp    # For Firewalld
    sudo firewall-cmd --reload
    ```

## 4. **Use SSH Key Authentication**
Disable password authentication and use SSH key-based authentication.

1. Generate an SSH key pair on your local machine:

    ```sh
    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    ```

2. Copy the public key to the remote server:

    ```sh
    ssh-copy-id -i ~/.ssh/id_rsa.pub username@remote_host
    ```

3. Disable password authentication:

    1. Open the SSH configuration file:

        ```sh
        sudo nano /etc/ssh/sshd_config
        ```

    2. Find the lines:

        ```plaintext
        #PasswordAuthentication yes
        ```

    3. Uncomment it and change it to:

        ```plaintext
        PasswordAuthentication no
        ```

    4. Restart the SSH service:

        ```sh
        sudo systemctl restart sshd
        ```

## 5. **Configure SSH Connection Timeout and Limit Login Attempts**
Setting a timeout for idle SSH sessions and limiting login attempts can enhance security.

1. Open the SSH configuration file:

    ```sh
    sudo nano /etc/ssh/sshd_config
    ```

2. Add the following lines to set a timeout and limit login attempts:

    ```plaintext
    ClientAliveInterval 300
    ClientAliveCountMax 2
    MaxAuthTries 3
    ```

3. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

## 6. **Use Fail2Ban to Prevent Brute Force Attacks**
Fail2Ban scans log files and bans IPs that show malicious signs.

1. Install Fail2Ban:

    ```sh
    sudo apt install fail2ban    # For Debian-based systems
    sudo yum install fail2ban    # For RHEL-based systems
    ```

2. Configure Fail2Ban for SSH:

    ```sh
    sudo nano /etc/fail2ban/jail.local
    ```

    Add the following configuration:

    ```plaintext
    [sshd]
    enabled = true
    port = 2222    # Change to your SSH port
    filter = sshd
    logpath = /var/log/auth.log
    maxretry = 3
    bantime = 3600
    ```

3. Restart Fail2Ban:

    ```sh
    sudo systemctl restart fail2ban
    ```

## 7. **Disable SSH Protocol 1**
Ensure that SSH protocol 1 is disabled as it is outdated and less secure.

1. Open the SSH configuration file:

    ```sh
    sudo nano /etc/ssh/sshd_config
    ```

2. Find the line:

    ```plaintext
    #Protocol 2,1
    ```

3. Uncomment it and change it to:

    ```plaintext
    Protocol 2
    ```

4. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

## 8. **Limit User Access**
Limit which users can access the server via SSH.

1. Open the SSH configuration file:

    ```sh
    sudo nano /etc/ssh/sshd_config
    ```

2. Add the following line to restrict access to specific users or groups:

    ```plaintext
    AllowUsers username1 username2
    ```

    or

    ```plaintext
    AllowGroups sshusers
    ```

3. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

## 9. **Use Two-Factor Authentication (2FA)**
Add an extra layer of security by implementing two-factor authentication.

1. Install Google Authenticator:

    ```sh
    sudo apt install libpam-google-authenticator    # For Debian-based systems
    sudo yum install google-authenticator           # For RHEL-based systems
    ```

2. Configure Google Authenticator for the user:

    ```sh
    google-authenticator
    ```

3. Configure SSH to use PAM and Google Authenticator:

    1. Open the PAM configuration file for SSH:

        ```sh
        sudo nano /etc/pam.d/sshd
        ```

    2. Add the following line:

        ```plaintext
        auth required pam_google_authenticator.so
        ```

    3. Open the SSH configuration file:

        ```sh
        sudo nano /etc/ssh/sshd_config
        ```

    4. Ensure the following lines are set:

        ```plaintext
        ChallengeResponseAuthentication yes
        ```

4. Restart the SSH service:

    ```sh
    sudo systemctl restart sshd
    ```

## 10. **Monitor and Audit SSH Logs**
Regularly monitor and audit your SSH logs for any suspicious activity.

1. Use `tail` to view recent logs:

    ```sh
    sudo tail -f /var/log/auth.log    # For Debian-based systems
    sudo tail -f /var/log/secure      # For RHEL-based systems
    ```

2. Set up a log monitoring solution, such as Logwatch or a centralized logging system, for more comprehensive log analysis.

#### Conclusion
By following these steps, you can significantly enhance the security of SSH access to your Linux servers and computers. Regularly review and update your security practices to adapt to new threats and vulnerabilities.

