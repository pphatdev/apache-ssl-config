# Apache SSL Configuration Guide
This guide shows you how to set up a local domain with SSL/HTTPS using [Apache](https://httpd.apache.org/) web server.

# Setup Requirements
## Before you begin, you'll need:
- Your web project files in a directory (either `index.php` or a `public` folder for Laravel projects)
- A custom local domain name of your choice
- SSL certificate (`.crt` file - don't worry, we'll help you generate this)
- Administrator/root access to run the configuration

## Generate SSL Certificate

Goto to `bin` folder and create a new file with name `filename.cnf` (example: `pphat.local.cnf`) and replace your info in the file.

```conf
[req]
default_bits = 2048
prompt = no
default_md = sha256
distinguished_name = dn
x509_extensions = v3_req

[dn]
C = KH
ST = Phnom Penh
L = Phnom Penh
O = PPHATDEV
OU = Development
CN = pphat.local

[v3_req]
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = pphat.local
```
After that, run:

```cmd
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout C:/xampp/apache/conf/ssl.key/pphat.local.key -out C:/xampp/apache/conf/ssl.crt/pphat.local.crt -config pphat.local.cnf
```
to generate the certificate. after that, you will see the certificate in `C:/xampp/apache/conf/conf/ssl.crt` folder and key in `C:/xampp/apache/conf/ssl.key` folder.


## Configure Apache Virtual Host

Goto to `C:/xampp/apache/conf/extra/httpd-vhosts.conf` file and add the following code:

### Without SSL

```conf

# Without SSL
# Example for pphat.local
 <VirtualHost pphat.local:80>

    # Server Name
    ServerName pphat.local

    # Document Root Path (using laravel project)
    DocumentRoot "D:/Project/laravel/blog-post/public"

    ErrorLog "logs/pphat.local-error.log"
    CustomLog "logs/pphat.local-access.log" common

    # Directory Permission
    <Directory "D:/Project/laravel/blog-post/public">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

```

### Directly With SSL

```conf
# With SSL
# Example for pphat.local
<VirtualHost pphat.local:80>
    # Redirect to HTTPS
    Redirect permanent / https://pphat.local/
</VirtualHost>


# With SSL
<VirtualHost pphat.local:443>

    # Server Name
    ServerName pphat.local
    # Server Alias
    ServerAlias pphat.local

    # Document Root Path
    DocumentRoot "D:/Project/laravel/blog-post/public"

    # SSL Options
    SSLEngine on
    SSLCertificateFile "C:/xampp/apache/conf/ssl.crt/pphat.local.crt"
    SSLCertificateKeyFile "C:/xampp/apache/conf/ssl.key/pphat.local.key"

    # Directory Permission
    <Directory "D:/Project/laravel/blog-post/public">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

```

## Create Domain name in Hosts

Goto to `C:/Windows/System32/drivers/etc/hosts` file as administrator and add the following code:

```
127.0.0.1       localhost
::1             localhost

# Add your custom local domain
127.0.0.1       pphat.local
```
