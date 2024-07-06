The `pg_hba.conf` (PostgreSQL Host-Based Authentication) file controls which hosts are allowed to connect to which databases and with which authentication methods. Each entry in the `pg_hba.conf` file describes a connection rule with the following fields:

1. **TYPE**: Specifies the type of connection.
2. **DATABASE**: Specifies the database(s) the rule applies to.
3. **USER**: Specifies the database user(s) the rule applies to.
4. **ADDRESS**: Specifies the client IP address or IP range.
5. **METHOD**: Specifies the authentication method to be used.

Here is an example entry and an explanation of each field:

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host    all             all             192.168.1.100/32        md5
```

### Field Descriptions

1. **TYPE**
   - `local`: Connections from the same machine via Unix domain sockets.
   - `host`: TCP/IP connections.
   - `hostssl`: TCP/IP connections encrypted with SSL.
   - `hostnossl`: TCP/IP connections without SSL.

2. **DATABASE**
   - `all`: Applies to all databases.
   - `sameuser`: The database with the same name as the user.
   - `samerole`: All databases for users with the same role.
   - `replication`: Applies to replication connections.
   - Specific database names.

3. **USER**
   - `all`: Applies to all users.
   - Specific user names.
   - Group names preceded by `+`.

4. **ADDRESS**
   - IP address or CIDR-notation subnet (e.g., `192.168.1.100/32`).
   - `0.0.0.0/0` or `::/0` for all IPv4 or IPv6 addresses.
   - If the type is `local`, this field should be left blank.

5. **METHOD**
   - `trust`: Allows the connection unconditionally.
   - `reject`: Rejects the connection unconditionally.
   - `md5`: Requires an MD5-encrypted password.
   - `password`: Requires a clear-text password.
   - `ident`: Uses the ident (or identd) server to authenticate users.
   - `peer`: Uses the operating system's user name.
   - `gss`, `sspi`, `krb5`: Uses GSSAPI, SSPI, or Kerberos for authentication.
   - `ldap`, `radius`, `cert`, `pam`: Uses LDAP, RADIUS, client certificates, or PAM for authentication.

### Example Entries

1. **Local connections using Unix domain sockets:**
   ```plaintext
   local   all             all                                     trust
   ```

2. **Local connections using peer authentication:**
   ```plaintext
   local   all             all                                     peer
   ```

3. **IPv4 local network connections with MD5 authentication:**
   ```plaintext
   host    all             all             192.168.1.0/24          md5
   ```

4. **Allow connections from a specific IP address:**
   ```plaintext
   host    all             all             192.168.1.100/32        md5
   ```

5. **Reject connections from a specific IP address:**
   ```plaintext
   host    all             all             192.168.1.200/32        reject
   ```

### Putting It All Together

When a client attempts to connect, PostgreSQL checks each rule in `pg_hba.conf` in order from top to bottom and uses the first matching rule to decide whether to allow the connection. Therefore, the order of entries is important. If no rule matches, the connection is rejected by default.

### Example `pg_hba.conf` File

```plaintext
# Allow local connections using Unix domain sockets
local   all             all                                     peer

# Allow local connections using MD5 password authentication
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5

# Allow connections from a specific IP address with MD5 authentication
host    all             all             192.168.1.100/32        md5

# Allow connections from a local network with MD5 authentication
host    all             all             192.168.1.0/24          md5

# Reject connections from a specific IP address
host    all             all             192.168.1.200/32        reject
```

This example allows local connections using Unix domain sockets and MD5 password authentication for connections from localhost and a specific IP address, while rejecting connections from another specific IP address.