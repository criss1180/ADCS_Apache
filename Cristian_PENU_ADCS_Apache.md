

## 1ère étape : Génération de la clé avec openssl

***création de la clé***

cd /etc/ssl/private
openssl genrsa -out www.cris.lan.key 2048
![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/6a535629-1e60-4ca0-aef6-c3179e25bb53)



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

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/51fdba87-1f48-4585-8a25-d12fae1345dd)



***

## 3ème étape : Création du csr avec notre fichier de conf

***on se place à l'endroit ou est notre clé on indique qu'on veut un certificat csr avec comme config notre fichier de conf crée juste avant***

cd /etc/ssl/private 
openssl req -new -key www.cris.lan.key -out www.cris.lan.csr -config ../www.cris.lan.conf

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/a4395d43-8f41-4e13-a30f-12b30b3dcb23)



***

## 4ème étape :  Importer notre csr sur notre AD-CS

***solution winscp et les vmtools***

***

## 5ème étape : Signer notre csr avec notre autorité de certification

***Déplacer vous dans l'emplacement ou vous avez déposé votre .csr ensuite on lance une requête afin qu'il signe notre certificat et nous le redonne en .cer***

cd C:\cert
certreq -submit -attrib "CertificateTemplate:WebServer" www.cris.lan.csr

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/1a4060d8-0147-4239-99a2-2d83f5da2fd7)


ensuite vous donner un nom à votre fichier
par exemple www.cris.lan

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/6d60c798-f6ee-424a-a25d-eed3841d64bd)


***

## 6ème étape importer votre .cer dans votre apache 

***solution winscp et les vmtools***

Deposer votre .cer dans votre /etc/ssl/certs

***

## 7ème étape configuration d'apache

***mettre le bon path pour chaque certificat***

cd /etc/apache2/sites-available
nano default-ssl.conf

        SSLCertificateFile      /etc/ssl/certs/www.cris.lan.cer
        SSLCertificateKeyFile   /etc/ssl/private/www.cris.lan.key

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/3e173672-6a5e-452e-af8f-34b16e9c623d)



systemctl restart apache2

Afficher la page :

![image](https://github.com/criss1180/ADCS_Apache/assets/115303549/05dfca86-836f-4d46-8f66-e8cdec08646c)


