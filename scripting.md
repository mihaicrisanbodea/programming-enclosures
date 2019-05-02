## Scripting

### Creating a certificate signing request (.cer) and obtaining the PKCS#12 (.pfx)

**Using OpenSSL**
*Prerequisite: openssl needs to be installed* <br>
Creating the private key `pvkey.key` and certificate signing request `csr.csr`
```bash
openssl req -nodes -newkey rsa:2048 -keyout pvkey.key -out csr.csr -subj "/C=test/ST=test/L=test/O=test/OU=test/CN=test"
```

The `.csr` can be further used to obtain the `.cer` certificate. <br>
With the `.cer` file, the `.pem` file can be created.
```bash
openssl x509 -inform der -in certificate.cer -out certificate.pem
```
Using the `.pem` file and the original private key, the `.pfx` can be build.

```bash
openssl pkcs12 -export -out cert.pfx -inkey pvkey.key -in certificate.pem
```
___

**Using certreq and PowerShell**
```bash
certreq.exe -new certreq.inf csr.req
```

```powershell
Set-Location -Path cert:\LocalMachine\My

Import-Certificate -Filepath "C:\certificate.cer"
```
___
