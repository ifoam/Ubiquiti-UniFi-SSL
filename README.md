# Ubiquiti-UniFi-SSL
How to generate, request, issue, and request an SSL certificate using UniFi, Ubuntu 18, and Microsoft CA

1. Once Unbiquiti has been fully installed and operational, we have to create a CSR.
```
cd /usr/lib/unifi
sudo java -jar lib/ace.jar new_cert controller.ifoam.org "ifoam" Austin Texas US
```
 
This will create a CSR in  /var/lib/unifi/. There are two files in two formats: unifi_certificate.csr.der and unifi_certificate.csr.pem

We can view the CSR with openssl:
```
openssl req -in unifi_certificate.csr.pem -noout -text
```
 
2. Now that we have the certificate request, we need to request the certificate. Go to the webpage of your CA and request a cert using your CSR.
Using Microsoft CA, we visit: http://ca-01.ifoam.org/certsrv
Select "Request a certificate" then submit an "advanced certificiate request"
 
Copy and paste the content of unifi_certificate.csr.pem to the website, select Web Server as the certificate template, and add a SAN. You can do this by adding additional attributes:
 san:dns=controller.ifoam.org
 
 
3. With the certificate successfully issued, you should download the certificate and the certificate chain in DER format.
 
Open the certificate chain and export any subordinate and root certs. Right click on the cert > All Tasks > Export. Use the default DER as the export format.
 
In my case, I had 3 certs: controller.ifoam.org.cer, sub.cer, root.cer. Transfers these files to the server into the /usr/lib/unifi/ directory.
 
4. Import the certificates using the following command:
```
root@controller:/usr/lib/unifi# sudo java -jar lib/ace.jar import_cert controller.ifoam.org.cer sub.cer root.cer
parse sub.cer (DER, 1 certs): ifoam Subordinate Certificate Authority
parse root.cer (DER, 1 certs): ifoam Enterprise Root Certificate Authority
parse controller.ifoam.org.cer (DER, 1 certs): controller.ifoam.org
Importing signed cert[controller.ifoam.org]
... issued by [ifoam Enterprise Subordinate Certificate Authority]
... issued by [ifoam Enterprise Root Certificate Authority]
Certificates successfuly imported. Please restart the UniFi Controller.
root@controller:/usr/lib/unifi#
```
 
5. Reboot Unifi
```
sudo service unifi restart
```
 
