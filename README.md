# generate_certificate_configuration
Configuration for certificate generation  
Example using apache web server  

***apache.cnf***
```
[req]
default_bits  = 2048
distinguished_name = req_distinguished_name
req_extensions = req_ext
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
countryName = SG
stateOrProvinceName = SG
localityName = SG
organizationName = SG
commonName = *
[req_ext]
subjectAltName = @alt_names
[v3_req]
basicConstraints = CA:TRUE
subjectAltName = @alt_names
[alt_names]
IP.1 = 10.10.10.1
IP.2 = 10.10.10.2
IP.3 = 10.10.10.3
IP.4 = 127.0.0.1
IP.5 = 1.2.3.4
DNS.1 = example-apache1
DNS.2 = example-apache2
DNS.3 = example-apache3
DNS.4 = test.internal
DNS.5 = *.test.internal
```

- ***Generate certificate from config file***
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/apache-selfsigned.key \
-out /etc/ssl/certs/apache-selfsigned.crt \
-config apache.cnf
```

### Useful Commands
---------------	

- ***Print certificate details via endpoint***
```
openssl s_client -showcerts -connect 1.2.3.4:5000 < /dev/null
```

- ***Save the certificate***
```
echo -n | \ 
openssl s_client -connect example-apache1:443 \ 
-servername example-apache1 | \ 
openssl x509 > server.crt
```

- ***Print details of certificate file***
```
openssl x509 -noout -text -in server.crt
```

- ***Check if certificate is CA eligible***  
To qualify as CA certificate, certificate must contain *CA:TRUE* in *X509v3 Basic Constraints* section
```
openssl x509 -noout -text \
-in /etc/ssl/certs/server.crt | \
grep --after-context=2 "X509v3 Basic Constraints" | \
grep "CA:TRUE"
```
