# Reverse Proxy for Enabling SSL

## What is a Reverse Proxy?

A reverse proxy is a server that typically sits behind the firewall in a private network and directs client request to the appropriate backend server. It provides additional level of abstraction and ensure smooth flow of traffic between client and server. Reverse proxy is effective in protecting the system from web vulnerabilities.

## What is SSL?

SSL stands for Secure Sockets Layer which is the standard technology used to secure the data transferred between two systems by using encryption algorithm. The algorithm scramble the data in transit and prevent the hackers from reading/modifying any information. The information could be anything sensitive or personal like credit card details, any financial information, personal details.&#x20;

## Steps to enable SSL using Reverse Proxy

### 1. Basic configuration to Install Nginx&#x20;

Nginx is used as a reverse proxy to get all the requests on DNS or IP on port 80 and 433 to your application.

**To install Nginx:**

```
sudo apt-get install nginx
sudo service nginx start
```

**Check service is running:**

```
sudo service nginx status
```

To start Nginx when the server boots, **Enable Nginx:**

```
sudo systemctl enable nginx
```

Add the following rules in the IP tables of your servers

```
sudo iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT
```

### 2. SSL Certificate creation

SSL is the combination of a Public certificate and a private key. The SSL key is kept on the server secretly while the SSL certificate is publicly shared with anyone requesting the content.

The SSL key encrypt the content sent to client and the SSL certificate decrypt the content signed by the associate SSL key.

The directory to hold the public certificate should already exist on the server and below is the path

```
/etc/ssl/certs
```

Below are the steps to create the path to hold the private key files with security to prevent any unauthorized access:

```
sudo mkdir /etc/ssl/private
sudo chmod 700 /etc/ssl/private
```

**Command to create self-signed key and certificate pair with OpenSSL**

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
```

<details>

<summary><strong>Above Parameter Description</strong></summary>

* **openssl:** Command line tool for creating and managing OpenSSL certificates, keys, other files, etc.,

<!---->

* **req:** This subcommand specifies that we want to use X.509 certificate signing request (CSR) management. The “X.509” is a public key infrastructure standard that SSL and TLS adheres to for its key and certificate management. To create a new X.509 cert, this command is used as a subcommand&#x20;

<!---->

* **-x509:** This further modifies the previous subcommand by telling the utility that we want to make a self-signed certificate instead of generating a certificate signing request, as would normally happen.&#x20;

<!---->

* **-nodes:** This tells OpenSSL to skip the option to secure our certificate with a passphrase. We need Nginx to be able to read the file, without user intervention, when the server starts up. A passphrase would prevent this from happening because we would have to enter it after every restart.&#x20;

<!---->

* **-days 365:** This option sets the length of time that the certificate will be considered valid. In this case, its set for a year.

<!---->

* **-newkey rsa:2048:** It is specified to generate a new certificate and a new key at the same time. We did not create the key that is required to sign the certificate in a previous step, so we need to create it along with the certificate. The rsa:2048 portion tells it to make an RSA key that is 2048 bits long.&#x20;

<!---->

* **-keyout:** This tells OpenSSL the path where the private key file has to be placed.

<!---->

* **-out:** This tells OpenSSL the path where the certificate has to be placed.

</details>

To Use OpenSSL , a strong Diffie-Hellman group should be created as it will be used while negotiating Perfect Forward Secrecy with clients. Below Command is used for creating dh group:

```
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

### 3. **Configure Nginx to use SSL**

create a new file in the below directory to configure a server block that serves content using the certificate files we generated. We can then optionally configure the default server block to redirect HTTP requests to HTTPS.

Create and open a file called **ssl.conf** in the **/etc/nginx/conf.d** directory:

```
sudo vi /etc/nginx/conf.d/ssl.conf
```

<details>

<summary>Place below content in the file ssl.conf</summary>

```
server { 
listen 443 http2 ssl; 
listen [::]:443 http2 ssl;
server_name server_IP_address;
ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
ssl_dhparam /etc/ssl/certs/dhparam.pem;

########################################################################
# from https://cipherli.st/ #
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html#
########################################################################
. . .
##################################
# END https://cipherli.st/ BLOCK #
##################################

root /usr/share/nginx/html;

location / {
}

error_page 404 /404.html;
location = /404.html {
}

error_page 500 502 503 504 /50x.html;
location = /50x.html {
}
}
```

</details>

### 4. Configure Nginx to redirect all HTTP to HTTPs

With this configuration, for any requests Nginx responds on port 433 with encrypted content while on port 80 responds with unencrypted content. This means that our site requires better encryption which is important while transferring any confidential data between the client and server.

Thankfully, the default Nginx configuration file allows to add directives to the default port 80 server by adding files in the below directory

```
/etc/nginx/default.d #direcoty
```

Create a new file called ssl-redirect.conf and open it using the below command

```
sudo vi /etc/nginx/default.d/ssl-redirect.conf

#Paste the below line in ssl-redirect.conf file#

return 301 https://$host$request_uri/;
```

### 5. Configuring Reverse proxy for the application

In the location **/etc/nginx/conf.d/ssl.conf file,** the configuration should be added to reverse proxy to your application. Remember that the proxy must go through HTTP and not HTTPs, as it is handled by Nginx.

When the TCP/IP packets go through the Public internet it has to be encrypted in the middle of way which is considered as the "dangerous path", when it reaches Nginx the server will have the private key to decrypt that packet and everything is secured.

Though there is no perfect security architecture, there is an option to enhance the security and make Nginx reverse proxy to HTTPs for application with proper configuration.&#x20;

**Example:** if you have a node express server running, you would need a HTTPS configuration with the proper SSL certificates set up on it. This adds another level of security, and it’s good to man in the middle attacks.

With HTTP only approach, in the below file location add the following data and remember to change the port:

```
#file Location
/etc/nginx/conf.d/ssl.conf

#Add the below content in the conf file
location/{
      proxy_set_header        Host $host;
      proxy_set_header        X-Real-IP $remote_addr;
      proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header        X-Forwarded-Proto $scheme;


      # Fix the “It appears that your reverse proxy set up is broken" error.
      proxy_pass          http://localhost:<the-port-where-your-application-runs>;
   
   proxy_read_timeout  90;
}

```

### 6. Configure Support to WebSockets

The websockets support is a little configuration in the location section of the file **/etc/nginx/conf.d/ssl.conf file.** Add the below data

```
 # WebSocket support
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade"; 

```

<details>

<summary><strong>The final ssl.conf file will be the below data,</strong></summary>

<pre><code><strong>server {
</strong><strong>
</strong><strong>listen 443 http2 ssl;
</strong>listen [::]:443 http2 ssl;

server_name server_IP_address;

ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
ssl_dhparam /etc/ssl/certs/dhparam.pem;


########################################################################
# from https://cipherli.st/                                            #
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html #
########################################################################


##################################
# END https://cipherli.st/ BLOCK #
##################################


root /usr/share/nginx/html;

location / {
  proxy_set_header        Host $host;
  proxy_set_header        X-Real-IP $remote_addr;
  proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header        X-Forwarded-Proto $scheme;



#Fix the “It appears that your reverse proxy set up is broken" error.
  proxy_pass http://localhost:&#x3C;the-port-where-your-application-runs>;
  proxy_read_timeout  90;


  # WebSocket support
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

}

error_page 404 /404.html;
location = /404.html {
}


error_page 500 502 503 504 /50x.html;
location = /50x.html {

}
}</code></pre>

</details>

### 7. Create Trusted Certificates

When multiple people using the system, this configuration for HTTPs work for development purposes while WSS might not work in Test/Prod environment.&#x20;

When self signed certificate is used by WSS the below error might occur,

```
WebSocket connection to 'wss://…' failed: Error in connection establishment: net::ERR_CONNECTION_CLOSED
```

To correct this error some trusted certificate is required and Nginx configuration must have .pem trusted certificate files only.

Use **certbot** with reachable domain to generate the trusted certificate. The below command is used to generate the required trusted certificates. Add the certificate to Nginx configuration replacing the self-signed certificates.

```
certonly --webroot --webroot-path=/var/www/html --email <your-email> --agree-tos --no-eff-email --staging -d <your-domain>  -d www.<your-domain>
```

Restart Nginx to reflect the changes,

```
sudo service nginx restart
```
