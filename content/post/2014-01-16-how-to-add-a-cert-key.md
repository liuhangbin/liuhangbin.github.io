---
layout: post
Title: How to add a cert key
Date: 2014-01-16
tags: [Linux, openssl, cert]
---

### Use the following script to generate the keys
```
#!/bin/bash
#echo "[LUCIEN]clear database"
#certutil -D -n "serverKey.crt" -d /etc/ipsec.d/
#certutil -D -n "clientKey.crt" -d /etc/ipsec.d/
find |grep -v test.sh |xargs rm -rf

cd /etc/pki/CA
test -f index.txt ||touch index.txt
echo "01" > serial
echo "01" > crlnumber
cd -

echo "[LUCIEN]create CA key and cert"
openssl req -x509 -newkey rsa:1024 -out cacert.pem -keyout cakey.pem -days 3650
echo "[LUCIEN]create CA crl"
openssl ca -gencrl -out crl.pem -cert cacert.pem -keyfile cakey.pem
echo "===================================="
echo "[LUCIEN]create server KEY"
openssl genrsa -des3 -out serverKey.pem 1024
echo "[LUCIEN]create server req KEY"
openssl req -new -key serverKey.pem -out serverKey.csr
echo "[LUCIEN]create server CERT"
openssl ca -in serverKey.csr -out serverKey.crt -cert cacert.pem -keyfile cakey.pem
echo "===================================="
echo "[LUCIEN]create client KEY"
openssl genrsa -des3 -out clientKey.pem 1024
echo "[LUCIEN]create client req KEY"
openssl req -new -key clientKey.pem -out clientKey.csr
echo "[LUCIEN]create client CERT"
openssl ca -in clientKey.csr -out clientKey.crt -cert cacert.pem -keyfile cakey.pem
echo "===================================="
echo "[LUCIEN]import server cert"
openssl pkcs12 -export -in serverKey.crt -out serverKey.p12 -inkey serverKey.pem -name "serverKey.crt"
echo "[LUCIEN]import client cert"
openssl pkcs12 -export -in clientKey.crt -out clientKey.p12 -inkey clientKey.pem -name "clientKey.crt"


#certutil -A -n "serverKey.crt" -t "p,p,p" -i serverKey.crt -d /etc/ipsec.d/
#certutil -A -n "clientKey.crt" -t "p,p,p" -i clientKey.crt -d /etc/ipsec.d/
pk12util -i clientKey.p12 -d /etc/ipsec.d
pk12util -i serverKey.p12 -d /etc/ipsec.d

cp cacert.pem /etc/ipsec.d/cacerts
cp crl.pem /etc/ipsec.d/crls
certutil -L -d /etc/ipsec.d
```

### Note
1. Province Name must filled with the same value
2. Common Name should never be same
3. others could left blank


### Log
```
# ./ca.sh
rm: cannot remove directory: ‘.’
/root/ca
[LUCIEN]create CA key and cert
Generating a 1024 bit RSA private key
.....................++++++
..........++++++
writing new private key to 'cakey.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:redhat
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:redhat
Email Address []:
[LUCIEN]create CA crl
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for cakey.pem:
====================================
[LUCIEN]create server KEY
Generating RSA private key, 1024 bit long modulus
........................................++++++
.................++++++
e is 65537 (0x10001)
Enter pass phrase for serverKey.pem:
Verifying - Enter pass phrase for serverKey.pem:
[LUCIEN]create server req KEY
Enter pass phrase for serverKey.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:redhat
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:server
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
[LUCIEN]create server CERT
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for cakey.pem:
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: Jan 16 07:21:27 2014 GMT
            Not After : Jan 16 07:21:27 2015 GMT
        Subject:
            countryName               = XX
            stateOrProvinceName       = redhat
            organizationName          = Default Company Ltd
            commonName                = server
        X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            Netscape Comment:
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier:
                51:62:44:48:6A:A9:D3:4D:A4:C5:F7:AD:7E:27:5F:84:75:50:11:EF
            X509v3 Authority Key Identifier:
                keyid:68:51:8C:45:43:31:4B:A0:0D:FD:F1:85:81:72:B6:01:9A:9A:8B:0C

Certificate is to be certified until Jan 16 07:21:27 2015 GMT (365 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
====================================
[LUCIEN]create client KEY
Generating RSA private key, 1024 bit long modulus
.........................++++++
.++++++
e is 65537 (0x10001)
Enter pass phrase for clientKey.pem:
Verifying - Enter pass phrase for clientKey.pem:
[LUCIEN]create client req KEY
Enter pass phrase for clientKey.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:redhat
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:client
Email Address []:


Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
[LUCIEN]create client CERT
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for cakey.pem:
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 2 (0x2)
        Validity
            Not Before: Jan 16 07:21:50 2014 GMT
            Not After : Jan 16 07:21:50 2015 GMT
        Subject:
            countryName               = XX
            stateOrProvinceName       = redhat
            organizationName          = Default Company Ltd
            commonName                = client
        X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            Netscape Comment:
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier:
                A8:5B:0C:A8:5D:E1:A7:38:A1:CC:69:66:01:F6:F9:27:AC:56:67:D9
            X509v3 Authority Key Identifier:
                keyid:68:51:8C:45:43:31:4B:A0:0D:FD:F1:85:81:72:B6:01:9A:9A:8B:0C

Certificate is to be certified until Jan 16 07:21:50 2015 GMT (365 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
====================================
[LUCIEN]import server cert
Enter pass phrase for serverKey.pem:
Enter Export Password:
Verifying - Enter Export Password:
[LUCIEN]import client cert
Enter pass phrase for clientKey.pem:
Enter Export Password:
Verifying - Enter Export Password:
Enter password for PKCS12 file:
pk12util: PKCS12 IMPORT SUCCESSFUL
Enter password for PKCS12 file:
pk12util: using nickname: server - Default Company Ltd
pk12util: PKCS12 IMPORT SUCCESSFUL

Certificate Nickname                                         Trust Attributes
                                                             SSL,S/MIME,JAR/XPI

serverKey.crt                                                u,u,u
clientKey.crt                                                u,u,u
server - Default Company Ltd                                 u,u,u

```
