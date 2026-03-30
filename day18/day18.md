# Day 18: Install and Configure MariaDB

## Task Description
The Nautilus application development team is planning to deploy a newly developed application on Nautilus infra in Stratos DC. The application uses MariaDB database, so as a pre-requisite we need to set up MariaDB database server as per requirements shared below.

## Requirements
- Install/Configure MariaDB server on the database server
- Create a database named `kodekloud_db5`
- Create a user called `kodekloud_tim` and set its password to `B4zNgHA7Ya`
- Grant full permissions to user `kodekloud_tim` on database `kodekloud_db5`

## Solution Commands

### 1. Install MariaDB server:
```bash
# For RHEL/CentOS based systems
sudo yum update -y
sudo yum install -y mariadb-server mariadb

# For Debian/Ubuntu based systems
sudo apt update
sudo apt install -y mariadb-server mariadb-client
```

### 2. Start and enable MariaDB service:
```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb
```

Expected output should show `Active: active (running)`

### 3. Secure MariaDB installation (Optional but recommended):
```bash
sudo mysql_secure_installation
```

**Interactive prompts:**
- Enter current password for root (just press Enter if none set)
- Set root password? Y/n: `Y` (recommended)
- Remove anonymous users? Y/n: `Y`
- Disallow root login remotely? Y/n: `Y`
- Remove test database? Y/n: `Y`
- Reload privilege tables? Y/n: `Y`

### 4. Log in to MariaDB as root:
```bash
sudo mysql -u root -p
# Or if no root password is set
sudo mysql
```

This opens the MariaDB interactive terminal.

### 5. Create the database:
```sql
CREATE DATABASE kodekloud_db5;
```

Expected output: `Query OK, 1 row affected`

### 6. Create the database user with password:
```sql
CREATE USER 'kodekloud_tim'@'localhost' IDENTIFIED BY 'B4zNgHA7Ya';
```

Expected output: `Query OK, 0 rows affected`

**Note:** For remote access from any host, use `'kodekloud_tim'@'%'` instead of `'kodekloud_tim'@'localhost'`

### 7. Grant full permissions to the user on the database:
```sql
GRANT ALL PRIVILEGES ON kodekloud_db5.* TO 'kodekloud_tim'@'localhost';
```

Expected output: `Query OK, 0 rows affected`

### 8. Flush privileges to apply changes:
```sql
FLUSH PRIVILEGES;
```

Expected output: `Query OK, 0 rows affected`

### 9. Exit MariaDB:
```sql
EXIT;
```

This returns you to the normal shell prompt.

## Verification Steps

### Verify MariaDB is running:
```bash
sudo systemctl status mariadb
sudo ss -tuln | grep 3306
```

### Verify database creation:
```bash
sudo mysql -u root -p -e "SHOW DATABASES;"
```

Look for `kodekloud_db5` in the output.

### Verify user creation:
```bash
sudo mysql -u root -p -e "SELECT User, Host FROM mysql.user WHERE User='kodekloud_tim';"
```

### Verify user permissions:
```bash
sudo mysql -u root -p -e "SHOW GRANTS FOR 'kodekloud_tim'@'localhost';"
```

Expected output should show:
```
GRANT ALL PRIVILEGES ON `kodekloud_db5`.* TO 'kodekloud_tim'@'localhost'
```

### Test user authentication and access:
```bash
mysql -u kodekloud_tim -p kodekloud_db5
```

Enter the password `B4zNgHA7Ya` when prompted. If successful, you'll see the MariaDB prompt.

### Test user can create tables:
```sql
-- After logging in as kodekloud_tim
CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
SHOW TABLES;
DROP TABLE test_table;
EXIT;
```

## Troubleshooting Steps

1. **Check MariaDB status** - Is the service active and running?
   ```bash
   sudo systemctl status mariadb
   ```

2. **Verify MariaDB is listening** - Check if MariaDB is listening on the default port
   ```bash
   sudo netstat -tuln | grep 3306
   sudo ss -tuln | grep 3306
   ```

3. **Check MariaDB logs** - Review logs for any errors
   ```bash
   sudo tail -f /var/log/mariadb/mariadb.log
   sudo journalctl -u mariadb -n 50
   ```

4. **Test socket connection** - Verify Unix socket is working
   ```bash
   sudo mysql -u root
   ```

5. **Check configuration files** - Ensure MariaDB configuration is correct
   ```bash
   sudo cat /etc/my.cnf
   sudo cat /etc/my.cnf.d/server.cnf
   # or for Debian/Ubuntu
   sudo cat /etc/mysql/mariadb.conf.d/50-server.cnf
   ```

6. **Verify firewall settings** - If allowing remote access
   ```bash
   # For firewalld
   sudo firewall-cmd --permanent --add-service=mysql
   sudo firewall-cmd --reload

   # For UFW
   sudo ufw allow 3306/tcp
   ```

## Success Indicators
- MariaDB service status shows `Active: active (running)`
- Port 3306 shows LISTEN status
- Database `kodekloud_db5` appears in the database list
- User `kodekloud_tim` appears in the user list
- `GRANT ALL PRIVILEGES` command executes without errors
- User can successfully connect to the database with provided credentials
- User can create, modify, and delete tables in the database