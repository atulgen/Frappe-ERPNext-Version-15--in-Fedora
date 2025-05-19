# Frappe-ERPNext Version-15 in Nobara/Fedora OS
A complete Guide to Install Frappe/ERPNext version 15 in Nobara/Fedora based OS

![image](https://github.com/user-attachments/assets/a55af47b-1037-4cee-9187-5d18a7595bb2)

[Introduction](https://docs.frappe.io/framework/user/en/introduction)

### Pre-requisites 

- Python 3.11+
- Node.js 18+
- Redis 5 (caching and real time updates)
- MariaDB 10.3.x / Postgres 9.5.x (to run database driven apps)
- yarn 1.12+ (js dependency manager)
- pip 20+ (py dependency manager)
- wkhtmltopdf (version 0.12.5 with patched qt) (for pdf generation)
- cron (bench's scheduled jobs: automated certificate renewal, scheduled backups)
- NGINX (proxying multitenant sites in production)

------
### Steps to Install Python 3.11.xx
------

> ## `Note: Recent Fedora versions (38+) and Nobara come with Python 3.11.xx by default. You can check your version with 'python3 --version'. If you already have 3.11+, you can skip the Python installation steps.`

#### Check current Python version:
```bash
python3 --version
```

#### If you need to install Python 3.11 on older Fedora versions:
```bash
sudo dnf update
sudo dnf install python3.11 python3.11-devel
```

#### Verify installation:
```bash
python3.11 --version
```

-----

### STEP 1: Update system and install Git
```bash
sudo dnf update -y
sudo dnf install git -y
```

### STEP 2: Install Python development tools
```bash
sudo dnf install python3-devel -y
```

### STEP 3: Install setuptools and pip (Python's Package Manager)
```bash
sudo dnf install python3-setuptools python3-pip -y
```

### STEP 4: Install virtualenv
```bash
sudo dnf install python3-virtualenv -y
# Or if using Python 3.11 specifically:
sudo dnf install python3.11-devel -y
```

### STEP 5: Install MariaDB
```bash
sudo dnf install mariadb-server mariadb-devel -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mysql_secure_installation
```

#### MariaDB Secure Installation Process:
```
In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): # PRESS ENTER
OK, successfully used password, moving on...

Switch to unix_socket authentication [Y/n] Y
Enabled successfully!
Reloading privilege tables..
 ... Success!

Change the root password? [Y/n] Y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!

Remove anonymous users? [Y/n] Y
 ... Success!

Disallow root login remotely? [Y/n] Y
 ... Success!

Remove test database and access to it? [Y/n] Y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reload privilege tables now? [Y/n] Y
 ... Success!
```

### STEP 6: Install MySQL/MariaDB client development files
```bash
sudo dnf install mariadb-connector-c-devel -y
```

### STEP 7: Edit the MariaDB configuration (unicode character encoding)
```bash
sudo nano /etc/my.cnf.d/mariadb-server.cnf
```

Add this configuration to the file:
```ini
[mysqld]
innodb-file-format=barracuda
innodb-file-per-table=1
innodb-large-prefix=1
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
query_cache_size = 16M

[mysql]
default-character-set = utf8mb4

[client]
default-character-set = utf8mb4
```

Press (Ctrl+X) to exit, then (Y) to save, then (Enter) to confirm.

Restart MariaDB:
```bash
sudo systemctl restart mariadb
```

### STEP 8: Install Redis
```bash
sudo dnf install redis -y
sudo systemctl start redis
sudo systemctl enable redis
```

### STEP 9: Install Node.js 18.x using NVM
```bash
sudo dnf install curl -y
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 18
nvm use 18
nvm alias default 18
```

Verify Node.js installation:
```bash
node --version
npm --version
```

### STEP 10: Install Yarn
```bash
sudo npm install -g yarn
```

Verify Yarn installation:
```bash
yarn --version
```

### STEP 11: Install wkhtmltopdf
```bash
sudo dnf install wkhtmltopdf xorg-x11-server-Xvfb -y
```

### STEP 12: Install frappe-bench
```bash
sudo pip3 install frappe-bench
```

Verify installation:
```bash
bench --version
```

### STEP 13: Initialize the frappe bench & install frappe latest version
```bash
bench init frappe-bench --frappe-branch version-15 --python python3.11
cd frappe-bench/
bench start
```

### STEP 14: Create a site in frappe bench
Open a new terminal window/tab and navigate to frappe-bench directory:
```bash
cd frappe-bench/
bench new-site dcode.com
bench --site dcode.com add-to-hosts
```

Open URL http://dcode.com:8000 to login

### STEP 15: Install ERPNext latest version in bench & site
```bash
bench get-app erpnext --branch version-15
# OR
# bench get-app https://github.com/frappe/erpnext --branch version-15

bench --site dcode.com install-app erpnext
bench start
```

## Additional Notes for Fedora/Nobara:

1. **Firewall Configuration**: If you're having connection issues, you might need to configure the firewall:
   ```bash
   sudo firewall-cmd --permanent --add-port=8000/tcp
   sudo firewall-cmd --reload
   ```

2. **SELinux**: If you encounter permission issues, you might need to adjust SELinux settings:
   ```bash
   sudo setsebool -P httpd_can_network_connect 1
   ```

3. **Service Management**: Fedora uses systemd for service management. You can check service status with:
   ```bash
   sudo systemctl status mariadb
   sudo systemctl status redis
   ```

4. **Alternative Python Installation**: If you prefer using the system package manager for Python packages:
   ```bash
   sudo dnf install python3-virtualenv python3-wheel
   ```

5. **Development Tools**: For compilation requirements, you might need:
   ```bash
   sudo dnf groupinstall "Development Tools"
   sudo dnf install gcc gcc-c++ make
   ```

## Troubleshooting:

- If you encounter permission errors, ensure your user is in the correct groups
- For database connection issues, verify MariaDB is running and accessible
- If bench commands fail, ensure you're in the frappe-bench directory
- For Node.js version conflicts, use nvm to switch between versions

## Production Setup:

For production deployment on Fedora/Nobara, consider:
- Setting up NGINX as a reverse proxy
- Configuring SSL certificates
- Setting up automatic backups
- Implementing proper security measures
- Configuring systemd services for auto-start
