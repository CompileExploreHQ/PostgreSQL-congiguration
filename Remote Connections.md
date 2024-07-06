To enable remote connections for PostgreSQL, follow these steps:

### Step 1: Configure PostgreSQL to Listen on All Addresses
Edit the `postgresql.conf` file to allow PostgreSQL to listen for connections on all IP addresses. 

1. Locate the `postgresql.conf` file. The location varies depending on your installation. Common locations include:
   - `/etc/postgresql/{version}/main/postgresql.conf` (Debian/Ubuntu)
   - `/var/lib/pgsql/{version}/data/postgresql.conf` (CentOS/RHEL)
   - `/usr/local/pgsql/data/postgresql.conf`

2. Open the `postgresql.conf` file in a text editor and find the line:
   ```sql
   #listen_addresses = 'localhost'
   ```

3. Modify it to:
   ```sql
   listen_addresses = '*'
   ```

### Step 2: Allow Remote Connections in pg_hba.conf
Edit the `pg_hba.conf` file to allow connections from specific IP addresses or a range of IP addresses.

1. Locate the `pg_hba.conf` file. Common locations include:
   - `/etc/postgresql/{version}/main/pg_hba.conf` (Debian/Ubuntu)
   - `/var/lib/pgsql/{version}/data/pg_hba.conf` (CentOS/RHEL)
   - `/usr/local/pgsql/data/pg_hba.conf`

2. Open the `pg_hba.conf` file in a text editor and add a line for the IP address or range you want to allow. For example, to allow connections from any IP address:
   ```sql
   host    all             all             0.0.0.0/0               md5
   ```

   Or to allow connections from a specific IP address or range:
   ```sql
   host    all             all             192.168.1.0/24          md5
   ```

### Step 3: Restart PostgreSQL
After making these changes, restart PostgreSQL to apply them.

For Debian/Ubuntu:
```sh
sudo systemctl restart postgresql
```

For CentOS/RHEL:
```sh
sudo systemctl restart postgresql-{version}
```

### Step 4: Adjust Firewall Settings
Ensure that your firewall allows incoming connections on the PostgreSQL port (default is 5432).

For example, using `ufw` on Debian/Ubuntu:
```sh
sudo ufw allow 5432/tcp
```

Or using `firewalld` on CentOS/RHEL:
```sh
sudo firewall-cmd --permanent --add-port=5432/tcp
sudo firewall-cmd --reload
```

### Step 5: Test the Connection
Try connecting to your PostgreSQL server from a remote machine using a PostgreSQL client, such as `psql`:

```sh
psql -h <server_ip> -U <username> -d <database_name>
```

Replace `<server_ip>`, `<username>`, and `<database_name>` with your server's IP address, the PostgreSQL username, and the database name, respectively. If everything is configured correctly, you should be able to connect remotely.