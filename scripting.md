## Scripting

### Creating a certificate signing request (.csr) and obtaining the PKCS#12 (.pfx)

**Using OpenSSL**

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

.inf file:
>[Version] <br>
>Signature="$Windows NT§" <br>
>[NewRequest] <br>
>Subject = "CN=test,O=test,OU=test,ST=test,L=test,C=test" <br>
>KeyLength = 2048 <br>
>KeySpec = 1 <br>
>Exportable = True <br>
>ProviderName = "Microsoft RSA SChannel Cryptographic Provider" <br>
>HashAlgorithm = SHA256 <br>
>MachineKeySet = True <br>
>SMIME = False <br>
>UseExistingKeySet = False <br>
>RequestType = PKCS10 <br>
>KeyUsage = 0xA0 <br>
>Silent = True <br>
>FriendlyName = "test" <br>
>[EnhancedKeyUsageExtension] <br>
>OID=1.3.6.1.5.5.7.3.1 <br>

Creating the certificate signing request `csr.csr`. 
Its private key is added to the OS store and will be exportable after a matching certificate will be added.
```bash
certreq.exe -new certreq.inf csr.csr
```

The `.csr` can be further used to obtain the `.cer` certificate. <br>
Further on, the thumbprint of the certificate is needed, to have a reference to the certificate that will have
the private key assigned after import. <br>
```powershell
$certPrint = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$certPrint.Import("C:\certificate.cer")
Write-Host $certPrint.Thumbprint
```

Using PowerShell, the certificate can be imported in the local store to enable the export of the private key.
```powershell
Set-Location -Path cert:\LocalMachine\My

Import-Certificate -Filepath "C:\certificate.cer"
```

The final step is exporting the `.pfx`.
```bash
certutil -exportpfx -p "" my $thumbprint C:\certificate.pfx
```
___
