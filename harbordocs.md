# Harbor Installation Docs




## Set Up Harbor
```bash
openssl genrsa -out ca.key 4096


openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=harbor.sidhusai.in" \
 -key ca.key \
 -out ca.crt


openssl genrsa -out harbor.sidhusai.in.key 4096

openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=harbor.sidhusai.in" \
    -key harbor.sidhusai.in.key \
    -out harbor.sidhusai.in.csr


cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=harbor.sidhusai.in
DNS.2=harbor.sidhusai
DNS.3=sidhuharbor
EOF





openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in harbor.sidhusai.in.csr \
    -out harbor.sidhusai.in.crt


cp harbor.sidhusai.in.crt /data/cert/
cp harbor.sidhusai.in.key /data/cert/



openssl x509 -inform PEM -in harbor.sidhusai.in.crt -out harbor.sidhusai.in.cert



cp harbor.sidhusai.in.cert /etc/docker/certs.d/harbor.sidhusai.in/
cp harbor.sidhusai.in.key /etc/docker/certs.d/harbor.sidhusai.in/
cp ca.crt /etc/docker/certs.d/harbor.sidhusai.in/


systemctl restart docker
```