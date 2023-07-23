# LOGBOOK 11

## Task 1 
In this task, we want to generate a self-signed certificate for our CA. In order to do that, we need to copy the `openssl.conf` file from `/usr/lib/ssl/openssl.cnf`, with the default settings. Moreover, we need to create a `serial` file with an integer with string format and an `index.txt` file (empty). Having completed these steps, we run the command `openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 \ -keyout ca.key -out ca.crt` to generate the self-signed certificate. 
**Question 1**: We can in the line `CA: TRUE`
![](https://media.discordapp.net/attachments/579367639478960253/934212078267412530/Screenshot_from_2022-01-20_11-44-21.png?width=711&height=364)
**Question 2**: We can infer that it is a self-signed certificate, since the ***Subject Key Identifier*** is the same as the ***Authority Key Identifier***.

![](https://media.discordapp.net/attachments/579367639478960253/934212078267412530/Screenshot_from_2022-01-20_11-44-21.png?width=711&height=364)**Question 3**: The information about the modulus, prime numbers and exponents for the RSA encryption algorithm can be found in `ca.key` file. 
![](https://media.discordapp.net/attachments/579367639478960253/934212078862995497/Screenshot_from_2022-01-20_11-56-50.png?width=711&height=364)

![](https://media.discordapp.net/attachments/579367639478960253/934212079341170708/Screenshot_from_2022-01-20_11-57-49.png?width=711&height=364)
## Task 2
Our goal in this task is to generate a Certificate Signing Request (CSR) for a web server. This request can be generated with the command `openssl req -newkey rsa:2048 -sha256 \ -keyout server.key -out server.csr \ -subj "/CN=www.bank32.com/O=Bank32 Inc./C=US" \ -passout pass:dees`. However, since websites have different URL's that must match with the common name in the certificate, we need to specify alternative hostnames in the certificate (`"subjectAltName"` field). This can be achieved by adding `-addext "subjectAltName = DNS:www.bank32.com, \ DNS:www.bank32A.com, \ DNS:www.bank32B.com"` to the `openssl req` command. This modification adds 2 alternative names to the Certificate Signing Request.

## Task 3
Having generated a Certificate Signing Request (CSR) in the previous task, we want to turn it into a **x509 certificate**. In order to accomplish this task, we need to run the command `openssl ca -config openssl.cnf -policy policy_anything \ -md sha256 -days 3650 \ -in server.csr -out server.crt -batch \ -cert ca.crt -keyfile ca.key` using the files created in tasks 1 and 2. After signing the certificate, we can check the contents of the **server.crt** file created with the command `openssl x509 -in server.crt -text -noout`.

## Task 4
In this task, our goal was to deploy a certificate in an Apache-based HTTPS website. In order to do this, we needed to copy the `certs` folder into the container. Inside this folder, we placed the certificate (generated in task 3) as well as the private key. Having completed this step, we need to run the command `service apache2 start` to start the server. There are additional steps, that are done when the container is started. 
The final step of the task is to instruct Firefox to recognize our certificate as a trusted Authority. We accomplish this by addind `ca.crt` to the **Trusted Authorities**.
![](https://media.discordapp.net/attachments/579367639478960253/934211928266506320/Screenshot_from_2022-01-21_09-42-43.png?width=613&height=650)

## Task 5

In order to simulate the **Man In The Middle** attack, we add an extra entry to the domains mapped for the ip `10.9.0.80` (in a real-world scenario, this would be done by a **DNS cache poisoning** attack). By simulating this attack, (mapping 10.9.0.80 to `www.example.com`), and trying to connect to this domain, we get the following warning,  since `www.example.com` is not a recognized domain specified when the certificate was signed.
![](https://media.discordapp.net/attachments/579367639478960253/934211928727900280/Screenshot_from_2022-01-21_09-48-45.png?width=613&height=650)

## Task 6
In this task, we assume that the CA we created in the previous tasks is compromised, considering the domain `www.example.com` as a trusted domain. The **Man In The Middle** attack is therefore allowed and we are able to connect with the domain `www.example.com`.
 ![](https://media.discordapp.net/attachments/579367639478960253/934211928958578718/Screenshot_from_2022-01-21_11-17-43.png?width=711&height=364)

