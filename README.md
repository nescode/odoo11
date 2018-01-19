
## Nescode Open Source ERP: Odoo 11 CE stable release
------
Odoo is a suite of open-source enterprise management applications. Odoo is used by small business, mid-size business, large companies and many different types of organizations to help them manage, automate, measure and optimize their operations, finances and projects.

## Installation on Ubuntu 16.04 LTS
---------
### Step 1: Initial server setup
```
sudo apt update && sudo apt upgrade

sudo apt install git python-pip python3-pip postgresql postgresql-server-dev-9.5 python-all-dev python-dev python-setuptools libxml2-dev libxslt1-dev libevent-dev libsasl2-dev libldap2-dev pkg-config libtiff5-dev libjpeg8-dev libjpeg-dev zlib1g-dev libfreetype6-dev liblcms2-dev liblcms2-utils libwebp-dev tcl8.6-dev tk8.6-dev python-tk libyaml-dev fontconfig
```

In case of "Unable to locate package language-pack-UTF-8" or "unsupported locale setting"
error:

```
export LC_ALL=C
```
### Step 2: Create a PostgreSQL User
```
sudo su - postgres
```
Set a strong password for the database user and record it in a secure location, you’ll need it in the following sections:
```
createuser odoo -U postgres -dRSP
```
Press CTRL+D to exit the postgres user session.

If you want to run multiple erp instances on the same server remember to check your PostgreSQL client configuration file and modify it according your needs.

### Step 3: Create an erp user

In order to separate erp from other services, create a new erp system user to run its processes:
```
sudo adduser --system --home=/opt/odoo --group odoo
```
### Step 4: Configure Logs

For logging, Ubuntu 16.04 uses systemd and journald by default. With that in mind, you can set up erp logs in several ways. To use journals and a separate erp log file at the same time, create the corresponding directory:
```
sudo mkdir /var/log/odoo
```
### Step 5: Install erp
```
cd /opt/odoo/
sudo git clone https://github.com/nescode/odoo.git .
```
### Step 6: Install dependencies for erp applications

Be sure to follow the steps in this section as your limited, non-root user (not the odoo user).
```
sudo pip3 install -r /opt/odoo/doc/requirements.txt
sudo pip3 install -r /opt/odoo/requirements.txt
```
Note: VM/VPS with ram lesser than 1GB or without swap file will generate below error.
```
error: command 'x86_64-linux-gnu-gcc' failed with exit status 4
```
### Step 7: Install Less CSS via nodejs and npm
```
sudo curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt install -y nodejs
sudo npm install -g less less-plugin-clean-css
```
### Step 8: Install Stable Wkhtmltopdf Version
```
cd /tmp
sudo wget https://downloads.wkhtmltopdf.org/0.12/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb
sudo dpkg -i wkhtmltox-0.12.1_linux-trusty-amd64.deb
```

To ensure wkhtmltopdf functions properly, copy the binaries to a location in your executable path:
```
sudo cp /usr/local/bin/wkhtmltopdf /usr/bin
sudo cp /usr/local/bin/wkhtmltoimage /usr/bin
```

### Step 9: ERP Server Configuration
```
sudo vi /etc/odoo.conf
```
Pase below code, save and exit from terminal

```
[options]
admin_passwd = your_erp_admin_password
db_host = False
db_port = False
db_user = odoo
db_password = your_database_password
logfile = /var/log/odoo/odoo-server.log
addons_path = /opt/odoo/addons,/opt/odoo/odoo/addons
```

### Step 10: Create an erp service
```
sudo vi /etc/systemd/system/odoo.service
```

Pase below code, save and exit

```
[Unit]
Description=Nescode ERP
Documentation=http://www.nescode.com
[Service]
# Ubuntu/Debian convention:
Type=simple
PermissionsStartOnly=true
SyslogIdentifier=odoo
User=odoo
ExecStart=/opt/odoo/odoo-bin -c /etc/odoo.conf --addons-path=/opt/odoo/addons/,/opt/odoo/odoo/addons
WorkingDirectory=/opt/odoo/
StandardOutput=journal+console

[Install]
WantedBy=multi-user.target
```

### Step 11: Change File Ownership and Permissions
```
sudo chmod 755 /etc/systemd/system/odoo.service
sudo chown root: /etc/systemd/system/odoo.service
sudo chown -R odoo: /opt/odoo/
sudo chown odoo:root /var/log/odoo 
sudo chown odoo: /etc/odoo.conf
sudo chmod 640 /etc/odoo.conf
```
### Step 12: Test the Server
```
sudo systemctl start odoo
sudo systemctl status odoo
sudo systemctl stop odoo
sudo systemctl status odoo
```

### Step 13: Enable the erp service

If your system journal doesn’t indicate any problems, enable the odoo unit to start and stop with the server:
```
sudo systemctl enable odoo
```
### Step 14: Reboot your ubuntu 16.04 server
```
sudo reboot
```
### Step 15: Test erp instance

http://<your_domain_or_IP_address_localhost>:8069


