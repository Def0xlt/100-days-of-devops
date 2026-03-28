# Day 17: Install and Configure PostgreSQL

## Task Description
The Nautilus application development team is planning to deploy a newly developed application on Nautilus infra in Stratos DC. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below.

PostgreSQL database server is already installed on the Nautilus database server.

## Requirements
- Create a database user `kodekloud_sam` and set its password to `FGH7rqJWCX`
- Create a database `kodekloud_db6` and grant full permissions to user `kodekloud_sam` on this database

**Note:** Please do not try to restart PostgreSQL server service.

## Solution Commands

### 1. Log in to PostgreSQL as the postgres user:
```bash
sudo -u postgres psql
```

This switches to the postgres system user and opens the PostgreSQL interactive terminal.

### 2. Create the database user with password:
```sql
CREATE USER kodekloud_sam WITH PASSWORD 'FGH7rqJWCX';
```

Expected output: `CREATE ROLE`

### 3. Create the database:
```sql
CREATE DATABASE kodekloud_db6;
```

Expected output: `CREATE DATABASE`

### 4. Grant full permissions to the user on the database:
```sql
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db6 TO kodekloud_sam;
```

Expected output: `GRANT`

### 5. Exit PostgreSQL:
```sql
\q
```

This returns you to the normal shell prompt.

## Verification Steps

### Verify user creation:
```bash
sudo -u postgres psql -c "\du"
```

This lists all database users. Look for `kodekloud_sam` in the output.

### Verify database creation:
```bash
sudo -u postgres psql -c "\l"
```

This lists all databases. Look for `kodekloud_db6` in the output.

### Verify user can connect to the database:
```bash
sudo -u postgres psql -d kodekloud_db6 -c "\c"
```

### Check permissions:
```bash
sudo -u postgres psql -d kodekloud_db6 -c "\dp"
```

### Test user authentication:
```bash
psql -U kodekloud_sam -d kodekloud_db6 -h localhost -W
```

Enter the password `FGH7rqJWCX` when prompted. If successful, you'll see the PostgreSQL prompt.

## Troubleshooting Steps

1. **Check PostgreSQL status** - Is the service active and running?
   ```bash
   sudo systemctl status postgresql
   ```

2. **Verify PostgreSQL is listening** - Check if PostgreSQL is listening on the default port
   ```bash
   sudo netstat -tuln | grep 5432
   sudo ss -tuln | grep 5432
   ```

3. **Check PostgreSQL logs** - Review logs for any errors
   ```bash
   sudo tail -f /var/log/postgresql/postgresql-*.log
   sudo journalctl -u postgresql -n 50
   ```

4. **Verify postgres user exists** - Ensure the postgres system user is available
   ```bash
   id postgres
   ```

5. **Check configuration files** - Ensure pg_hba.conf allows connections
   ```bash
   sudo cat /var/lib/pgsql/data/pg_hba.conf
   # or
   sudo cat /etc/postgresql/*/main/pg_hba.conf
   ```

## Success Indicators
- User `kodekloud_sam` appears in the user list (\du command)
- Database `kodekloud_db6` appears in the database list (\l command)
- `GRANT` command executes without errors
- User can successfully connect to the database with provided credentials
- All commands return expected output messages

## Common Obstacles
- **Authentication failure:** Check if pg_hba.conf allows password authentication for the user
- **Permission denied:** Ensure you're running commands as the postgres user or with sudo
- **Database already exists:** Drop the existing database first or verify requirements
- **User already exists:** Drop the existing user first or use ALTER USER to change password
- **Connection refused:** PostgreSQL service might not be running
- **Peer authentication failed:** pg_hba.conf might be configured for peer auth instead of password auth

## PostgreSQL Commands Reference

### Common psql meta-commands:
- `\l` or `\list` - List all databases
- `\du` or `\du+` - List all roles/users
- `\c database_name` - Connect to a database
- `\dt` - List tables in current database
- `\dp` or `\z` - List table access privileges
- `\q` - Quit psql
- `\h` - Help on SQL commands
- `\?` - Help on psql commands

### User management commands:
```sql
-- Create user with password
CREATE USER username WITH PASSWORD 'password';

-- Alter user password
ALTER USER username WITH PASSWORD 'newpassword';

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE dbname TO username;

-- Grant specific privileges
GRANT SELECT, INSERT, UPDATE ON TABLE tablename TO username;

-- Revoke privileges
REVOKE ALL PRIVILEGES ON DATABASE dbname FROM username;

-- Drop user
DROP USER username;
```

### Database management commands:
```sql
-- Create database
CREATE DATABASE dbname;

-- Drop database
DROP DATABASE dbname;

-- Create database with owner
CREATE DATABASE dbname OWNER username;
```

## Notes
- PostgreSQL uses roles for both users and groups
- The `postgres` user is the default superuser
- Password must be enclosed in single quotes
- Database names and usernames are case-sensitive if quoted
- `GRANT ALL PRIVILEGES` gives full access including SELECT, INSERT, UPDATE, DELETE, etc.
- Do not restart PostgreSQL service as per task requirements
- Changes take effect immediately without service restart
- User passwords are stored encrypted in PostgreSQL

## PostgreSQL Authentication Methods
PostgreSQL supports several authentication methods in pg_hba.conf:
- **trust:** Allow connection without password
- **reject:** Reject connection unconditionally
- **md5:** Require MD5-encrypted password
- **password:** Require plain-text password (not recommended)
- **peer:** Use operating system user name (local connections only)
- **ident:** Use identd service to verify user

## Security Best Practices
- Use strong passwords for database users
- Limit database user privileges to only what's needed
- Regularly update PostgreSQL to patch security vulnerabilities
- Use SSL/TLS for remote connections
- Configure pg_hba.conf to restrict access by IP address
- Never use trust authentication in production
- Regularly audit user privileges and remove unused accounts
- Enable logging for security events

## Additional Configuration (Optional)
If you need to allow remote connections:

1. Edit postgresql.conf:
```bash
sudo vi /var/lib/pgsql/data/postgresql.conf
```

Add or modify:
```
listen_addresses = '*'
```

2. Edit pg_hba.conf:
```bash
sudo vi /var/lib/pgsql/data/pg_hba.conf
```

Add line for remote access:
```
host    kodekloud_db6    kodekloud_sam    0.0.0.0/0    md5
```

3. Reload configuration (only if restart is allowed):
```bash
sudo systemctl reload postgresql
```

## Verification Commands Summary
```bash
# Check PostgreSQL status
sudo systemctl status postgresql

# List all users
sudo -u postgres psql -c "\du"

# List all databases
sudo -u postgres psql -c "\l"

# Check database ownership and access
sudo -u postgres psql -c "\l kodekloud_db6"

# Test connection as the new user
psql -U kodekloud_sam -d kodekloud_db6 -h localhost -W

# Connect and verify permissions
sudo -u postgres psql -d kodekloud_db6
\dp
\q
```
