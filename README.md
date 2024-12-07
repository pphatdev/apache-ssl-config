# Apache SSL Configuration Guide
This guide shows you how to set up a local domain with SSL/HTTPS using [Apache](https://httpd.apache.org/) web server.

# Setup Requirements
## Before you begin, you'll need:
- Your web project files in a directory (either `index.php` or a `public` folder for Laravel projects)
- A custom local domain name of your choice
- SSL certificate (`.crt` file - don't worry, we'll help you generate this)
- Administrator/root access to run the configuration

## Generate SSL Certificate

Goto to `bin` folder and create a new file with name `filename.cnf` (example: `pphatdev.local.cnf`) and replace your info in the file.

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
CN = pphatdev.local

[v3_req]
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = pphatdev.local
```
After that, run:

```cmd
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout C:/xampp/apache/conf/ssl.key/pphatdev.local.key -out C:/xampp/apache/conf/ssl.crt/pphatdev.local.crt -config pphatdev.local.cnf
```
to generate the certificate. after that, you will see the certificate in `C:/xampp/apache/conf/conf/ssl.crt` folder and key in `C:/xampp/apache/conf/ssl.key` folder.

## Configure Apache Virtual Host

Goto to `C:/xampp/apache/conf/extra/httpd-vhosts.conf` file and add the following code:

### Without SSL

```conf

# Without SSL
# Example for pphatdev.local
 <VirtualHost pphatdev.local:80>

    # Server Name
    ServerName pphatdev.local

    # Document Root Path (using laravel project)
    DocumentRoot "D:/Project/laravel/blog-post/public"

    ErrorLog "logs/pphatdev.local-error.log"
    CustomLog "logs/pphatdev.local-access.log" common

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
# Example for pphatdev.local
<VirtualHost pphatdev.local:80>
    # Redirect to HTTPS
    Redirect permanent / https://pphatdev.local/
</VirtualHost>


# With SSL
<VirtualHost pphatdev.local:443>

    # Server Name
    ServerName pphatdev.local
    # Server Alias
    ServerAlias pphatdev.local

    # Document Root Path
    DocumentRoot "D:/Project/laravel/blog-post/public"

    # SSL Options
    SSLEngine on
    SSLCertificateFile "C:/xampp/apache/conf/ssl.crt/pphatdev.local.crt"
    SSLCertificateKeyFile "C:/xampp/apache/conf/ssl.key/pphatdev.local.key"

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
127.0.0.1       pphatdev.local
```

## Import the certificate to Windows trust store

Open powershell as administrator and run the following command:

```sh
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("C:\xampp\apache\conf\ssl.crt\pphatdev.local.crt")
$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("Root","LocalMachine")
$store.Open("ReadWrite")
$store.Add($cert)
$store.Close()
```
After completing these steps, you should be able to access your local domain with SSL/HTTPS.

## Test

Start your apache server and open your browser. Type `https://pphatdev.local` to see the result.

And this what you see:

![image](https://github.com/user-attachments/assets/eca4ce75-6c97-4874-81aa-d0e324f92d6d)
