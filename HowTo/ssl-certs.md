## Create a DIR for the CA keys
```shell
mkdir ~/.ssh/selfSignedCA

cd ~/.ssh/selfSignedCA
```

## Create Self-Signed CA cert
```shell
openssl genrsa -des3 -out ./selfSignedCA.pem 4096
```

## Remove the passphrase from the CA key
```shell
openssl rsa -in ./selfSignedCA.pem -out ./selfSignedCA_new.pem

mv ./selfSignedCA_new.pem ./selfSignedCA.pem
```

## Sign the Self-Signed CA Cert to create the CA Authority
```shell
openssl req -new -x509 -sha256 \
    -days 3650 \
    -subj "/C=US/ST=California/L=SantaClara/O=Home/CN=Home CA" \
    -key ./selfSignedCA.pem -out ./selfSignedCA.crt 
```

## Create a DIR for the service keys
```shell
mkdir ~/.ssh/<service_name>

cd ~/.ssh/<service_name>
```

## Create Certificate Config
```shell
cat > ./<service_name>.req.conf <<-EOF
[req]
default_md = sha256
distinguished_name = req_distinguished_name
req_extensions = req_ext
prompt = no

[req_distinguished_name]
C = US
ST = CA
L = Santa Clara
O = Home
OU = Media Services
CN = <service_name>.home

[req_ext]
keyUsage=critical, digitalSignature, keyEncipherment, dataEncipherment
extendedKeyUsage=critical,serverAuth,clientAuth
basicConstraints=CA:FALSE
subjectAltName = @alt_names

[alt_names]
DNS.1=<service_name>.home
DNS.2=<service_name>
IP.1=<service_IP_addr>
EOF
```

## Create service cert key with config
```shell
openssl genrsa -out ./<service_name>.key 4096
```

## Create service key signing request
```shell
openssl req -new -sha256 \
    -config ./<service_name>.req.conf \
    -key ./<service_name>.key  -out ./<service_name>.csr
 ```

 ## Sign service key with Self-Signed CA
 ```shell
openssl x509 -req -days 1825 -sha256 \
    -CA ../selfSignedCA/selfSignedCA.crt -CAkey ../selfSignedCA/selfSignedCA.pem \
    -extfile ./<service_name>.req.conf -in ./<service_name>.csr -out ./<service_name>.crt \
    -extensions req_ext -CAcreateserial
```

## Create a PEM file for HAProxy
```shell
cat ./<service_name>.key ./<service_name>.crt ../selfSignedCA/selfSignedCA.crt > ./<service_name>.pem
```

## Special Steps for Plex
Convert the key and cert into a `PKCS #12 (.p12)` cert:
```shell
openssl pkcs12 -export -out plex.p12 -inkey ./plex.key -in ./plex.crt -name "Plex Server Certificate"
```
Copy the cert to the Plex DIR and set the permissions:
```shell
sudo cp ~/.ssh/plex/plex.p12 /var/lib/plexmediaserver/plex.p12
sudo chown plex:plex plex.p12 /var/lib/plexmediaserver/plex.p12
```
Fully stop and restart the Plex service:
```shell
sudo systemctl stop plexmediaserver.service && sleep 10 \
    sudo systemctl start plexmediaserver.service
```
