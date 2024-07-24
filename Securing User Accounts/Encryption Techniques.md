# Encryption Techniques in Linux 

### 1. Full Disk Encryption (FDE) with LUKS

**LUKS (Linux Unified Key Setup) is the standard for Linux hard disk encryption.**

#### Steps to Set Up LUKS:

1. **Install cryptsetup**:
   ```bash
   sudo apt-get install cryptsetup
   ```

2. **Partition the Disk** (if not already partitioned):
   ```bash
   sudo fdisk /dev/sdX
   ```

3. **Initialize LUKS on the Partition**:
   ```bash
   sudo cryptsetup luksFormat /dev/sdX1
   ```

4. **Open the LUKS Partition**:
   ```bash
   sudo cryptsetup luksOpen /dev/sdX1 crypt1
   ```

5. **Create a Filesystem on the Encrypted Partition**:
   ```bash
   sudo mkfs.ext4 /dev/mapper/crypt1
   ```

6. **Mount the Encrypted Partition**:
   ```bash
   sudo mount /dev/mapper/crypt1 /mnt
   ```

7. **Automatically Open LUKS at Boot** (optional):
   Add the following line to `/etc/crypttab`:
   ```
   crypt1 /dev/sdX1 none luks
   ```

8. **Update fstab**:
   Add the mount point to `/etc/fstab` to ensure it mounts at boot:
   ```
   /dev/mapper/crypt1 /mnt ext4 defaults 0 0
   ```

### 2. Encrypting Home Directories

**Encrypting the home directory ensures that user data is protected.**

#### Using `ecryptfs`:

1. **Install ecryptfs-utils**:
   ```bash
   sudo apt-get install ecryptfs-utils
   ```

2. **Set Up Encrypted Home Directory**:
   ```bash
   sudo ecryptfs-setup-private
   ```

3. **Move Existing Data to Encrypted Directory**:
   ```bash
   rsync -a /home/user/ /home/user/.Private
   ```

4. **Unmount and Remount the Encrypted Directory**:
   ```bash
   ecryptfs-umount-private
   ecryptfs-mount-private
   ```

### 3. File and Directory Encryption with GnuPG

**GnuPG (GNU Privacy Guard) provides encryption and signing of data.**

#### Encrypting Files:

1. **Install GnuPG**:
   ```bash
   sudo apt-get install gnupg
   ```

2. **Encrypt a File**:
   ```bash
   gpg -c file.txt
   ```
   This creates `file.txt.gpg`.

3. **Decrypt a File**:
   ```bash
   gpg file.txt.gpg
   ```

#### Encrypting Directories:

1. **Create an Encrypted Archive**:
   ```bash
   tar czf - /path/to/directory | gpg -c -o directory.tar.gz.gpg
   ```

2. **Decrypt the Archive**:
   ```bash
   gpg -o directory.tar.gz -d directory.tar.gz.gpg
   tar xzf directory.tar.gz
   ```

### 4. Network Encryption with SSH

**Secure Shell (SSH) provides encrypted communication over the network.**

#### Configuring SSH:

1. **Install SSH Server**:
   ```bash
   sudo apt-get install openssh-server
   ```

2. **Generate SSH Key Pair**:
   ```bash
   ssh-keygen -t rsa -b 4096
   ```

3. **Copy Public Key to Remote Server**:
   ```bash
   ssh-copy-id user@remote_server
   ```

4. **Disable Password Authentication**:
   Edit `/etc/ssh/sshd_config`:
   ```
   PasswordAuthentication no
   ```

5. **Restart SSH Service**:
   ```bash
   sudo systemctl restart ssh
   ```

### 5. TLS/SSL Encryption for Web Servers

**TLS/SSL encrypts data between web servers and clients.**

#### Setting Up SSL with Nginx:

1. **Install Nginx**:
   ```bash
   sudo apt-get install nginx
   ```

2. **Obtain SSL Certificate** (using Let's Encrypt):
   ```bash
   sudo apt-get install certbot python3-certbot-nginx
   sudo certbot --nginx
   ```

3. **Configure Nginx for SSL**:
   Edit your site configuration in `/etc/nginx/sites-available/default`:
   ```nginx
   server {
       listen 80;
       server_name example.com;
       return 301 https://$host$request_uri;
   }

   server {
       listen 443 ssl;
       server_name example.com;

       ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
   }
   ```

4. **Test Nginx Configuration**:
   ```bash
   sudo nginx -t
   ```

5. **Restart Nginx**:
   ```bash
   sudo systemctl restart nginx
   ```

### 6. Encrypting Data at Rest with dm-crypt

**dm-crypt is a device-mapper target for disk encryption.**

#### Setting Up dm-crypt:

1. **Install cryptsetup**:
   ```bash
   sudo apt-get install cryptsetup
   ```

2. **Set Up dm-crypt**:
   ```bash
   sudo cryptsetup --verify-passphrase luksFormat /dev/sdX1
   sudo cryptsetup luksOpen /dev/sdX1 mycrypt
   ```

3. **Create a Filesystem**:
   ```bash
   sudo mkfs.ext4 /dev/mapper/mycrypt
   ```

4. **Mount the Encrypted Filesystem**:
   ```bash
   sudo mount /dev/mapper/mycrypt /mnt
   ```

5. **Add to crypttab for Automatic Mounting**:
   Add to `/etc/crypttab`:
   ```
   mycrypt /dev/sdX1 none luks
   ```

6. **Update fstab**:
   Add the mount point to `/etc/fstab`:
   ```
   /dev/mapper/mycrypt /mnt ext4 defaults 0 0
   ```

### 7. Secure Sockets Layer (SSL) Certificates for Services

**SSL certificates encrypt data transmitted over the internet.**

#### Setting Up SSL for Services:

1. **Install Certbot**:
   ```bash
   sudo apt-get install certbot
   ```

2. **Obtain a Certificate**:
   ```bash
   sudo certbot certonly --standalone -d yourdomain.com
   ```

3. **Configure Services to Use the Certificate**:
   - For **Apache**:
     Edit `/etc/apache2/sites-available/yourdomain.conf`:
     ```apache
     <VirtualHost *:443>
         ServerName yourdomain.com
         SSLEngine on
         SSLCertificateFile /etc/letsencrypt/live/yourdomain.com/fullchain.pem
         SSLCertificateKeyFile /etc/letsencrypt/live/yourdomain.com/privkey.pem
     </VirtualHost>
     ```
     ```bash
     sudo a2enmod ssl
     sudo systemctl restart apache2
     ```

   - For **Postfix** (Mail Server):
     Edit `/etc/postfix/main.cf`:
     ```bash
     smtpd_tls_cert_file=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
     smtpd_tls_key_file=/etc/letsencrypt/live/yourdomain.com/privkey.pem
     smtpd_use_tls=yes
     ```
     ```bash
     sudo systemctl restart postfix
     ```

