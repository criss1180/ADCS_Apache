

## 1ère étape : Génération de la clé avec openssl

***création de la clé***

cd /etc/ssl/private
openssl genrsa -out www.cris.lan.key 2048

![[Pasted image 20231026123712.png]]


***

## 2ème étape : Création d'un fichier de conf avec les subjectAltName

***création du fichier de conf avec les SubjectAltName***

cd /etc/ssl
nano www.cris.lan.conf

[req]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn
[ dn ]
C=FR
ST=CVDL
L=Tours
O=IT
OU=cris.lan
emailAddress=info@cris.lan
CN = www.cris.lan
[ req_ext ]
subjectAltName = @alt_names
[ alt_names ]
DNS.1 = cris.lan
DNS.2 = www.cris.lan

![[Pasted image 20231026123057.png]]


***

## 3ème étape : Création du csr avec notre fichier de conf

***on se place à l'endroit ou est notre clé on indique qu'on veut un certificat csr avec comme config notre fichier de conf crée juste avant***

cd /etc/ssl/private 
openssl req -new -key www.cris.lan.key -out www.cris.lan.csr -config ../www.cris.lan.conf

![[Pasted image 20231026124559.png]]


***

## 4ème étape :  Importer notre csr sur notre AD-CS

***solution winscp et les vmtools***

***

## 5ème étape : Signer notre csr avec notre autorité de certification

***Déplacer vous dans l'emplacement ou vous avez déposé votre .csr ensuite on lance une requête afin qu'il signe notre certificat et nous le redonne en .cer***

cd C:\cert
certreq -submit -attrib "CertificateTemplate:WebServer" www.cris.lan.csr

![[Pasted image 20231026125755.png]]

ensuite vous donner un nom à votre fichier
par exemple www.cris.lan

![[Pasted image 20231026125904.png]]

***

## 6ème step importer votre .cer dans votre apache 

***solution winscp et les vmtools***

Deposer votre .cer dans votre /etc/ssl/certs

***

## 7ème step configuration d'apache

***mettre le bon path pour chaque certificat***

cd /etc/apache2/sites-available
nano default-ssl.conf

        SSLCertificateFile      /etc/ssl/certs/www.cris.lan.cer
        SSLCertificateKeyFile   /etc/ssl/private/www.cris.lan.key

![[Pasted image 20231026130422.png]]


systemctl restart apache2

Afficher la page :

![[Pasted image 20231026130737.png]]

