![SAP HANA Academy](https://yt3.ggpht.com/-BHsLGUIJDb0/AAAAAAAAAAI/AAAAAAAAAVo/6_d1oarRr8g/s100-mo-c-c0xffffffff-rj-k-no/photo.jpg)
# SAP Analytics Cloud - Live Connections #
Code snippets, information and documentation links for the tutorial video playlist on YouTube. 

### YouTube Tutorial Video Playlist ### 
[SAP Analytics Cloud Live Connection](https://www.youtube.com/playlist?list=PLkzo92owKnVxm3TMoUliy5b5UO1G5GP50)

## Live Connections ##
We can connect SAP Analytics Cloud to a multitude of data sources. 

![SAC_DataConnections](https://www.sapanalytics.cloud/wp-content/uploads/2018/12/SAC_DataConnections-1.2.png)

In this series, we cover a live connection to SAP HANA using the Information Access (InA) Service. This service is exposed by SAP HANA XS (classic) and requires the following configuration before the connection can be established. 
1. Configure a public URL for the XS server. By default, XS is only accessible internally. 
2. Certify the public URL. By default, the XS server (Web Dispatcher) uses only self-signed certificates. 
3. Grant the INA_ROLE to a user. By default, no user has this role granted. 
4. Enable and confgiure external access to the InA service. By default, CORS (Cross Origin Resource Sharing) is disabled. 
5. Modify the session server timeout for the XS server. By default, the session times out in 90 seconds. 

### Create Live Connection ### 
Creating a Live Connection to a SAP HANA system on SAP Analytics Cloud is a simple task for the business user once all the prerequisites have been met. We need to provide the public URL for the system, the HTTPS port, and for basic authentication a user name and password, e.g.:
```
host = myhost.lab.cloud.sap
port = 4390
user = INA_USER
password = *******
```

#### Video Tutorial ####
[![Create Live Connection](https://img.youtube.com/vi/GLs3ByA9WvY/0.jpg)](https://youtu.be/GLs3ByA9WvY "[2019.05] SAP Cloud Analytics Live Connections: Create Connection - SAP Digital Enablement")

#### Documentation ####
* [Live Data Connection to SAP HANA Using a Direct Connection with Password Authentication](https://help.sap.com/doc/00f68c2e08b941f081002fd3691d86a7/2019.7/en-US/58c890e1c89d41e69b2cec31bac2d95f.html) - SAP Analytics Cloud Help

## SAP HANA Live Connection Prerequisites ##

### 1. Configure Public URL (XS) ### 
To access information views in a HANA tenant database from SAP Analytics Cloud, the HANA XS server needs to listen to a public URL. It is not required to register the public URL with a public DNS. Only the computer accessing SAC needs to be able to resolve the address. This means that even the local hosts file can be used for resolution. More common will be DNS registration inside the corporate network.  
```
## execute as INI ADMIN on SYSTEMDB
ALTER SYSTEM ALTER CONFIGURATION ('xsengine.ini', 'database', 'HXE') SET ('public_urls', 'http_url') = 'http://myhost.lab.cloud.sap:8090' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('xsengine.ini', 'database', 'HXE') SET ('public_urls', 'https_url') = 'https://myhost.lab.cloud.sap:4390' WITH RECONFIGURE;
```
For testing, you can add the FQDN of the host to your local hosts file (Linux, macOS /etc/hosts; Windows %WINDIR%\system32\drivers\etc)
```
127.0.0.1       localhost
153.127.3.52    myhost.lab.cloud.sap
```

#### Video Tutorial ####
[![1. Public URL (XS)](https://img.youtube.com/vi/qajuw5R6Das/0.jpg)](https://youtu.be/qajuw5R6Das "[2019.05] SAP Cloud Analytics Live Connections: 1. Public URL (XS) - SAP Digital Enablement")

#### Documentation ####
* [Configure HTTP(S) Access to Tenant Databases via SAP HANA XS Classic](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/latest/en-US/af24ce38929b4f1fa934a28147fc7641.html) - SAP HANA Administration Guide for SAP HANA Platform
* [Maintain Standard HTTP Port Numbers with SAP HANA XS](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/latest/en-US/116cc3f3f3f645159ee138c3ba50a48b.html) - SAP HANA Administration Guide for SAP HANA Platform

### 2. Certify Public URL (XS) ### 
The public URL needs to be signed by a certificate authority (CA). Digital web server certificates are provided by companies like DigiCert and Symantec (random list). For corporate environments, this service is typically by the IT department (SAP colleagues, visit [Global PKI Certificate Management](https://sapcerts.wdf.global.corp.sap/WebEnrollment.aspx)).

The public/private keys of the PSE (Personal Security Environment) of the HTTP Server (Web Dispatcher) are signed with host name only (CN=sid-hxe in case of HANA express). Recreate the SAPSSLS PSE with proper identification and with the CN corresponding to the public URL, e.g. 
```
DE=Germany
ST=Baden-Wuertemberg
L=Walldorf
O=SAP
OU=IT
CN=myhost.lab.cloud.sap
```
Then generate the CSR (certificate sign request), upload it to the CA and import the web server certificate.  
In the video tutorial, we use the SAP Web Dispatcher to recreate the PSE, generate the CRS and import the certificate. 
You can access the tenant database web dispatcher using the URL configured above with path: 

    https://myhost.lab.cloud.sap:4390/sap/hana/xs/wdisp/admin/

To connect to the Web Dispatcher, we need the WebDispatcherAdmin role. Any user could be used but a least-privileged user approach is generally recommended. In the code example below we create the XS_ADMIN user and grant the user the WebDispatcherAdmin role. 
```	
CREATE USER xs_admin PASSWORD  ****;
CALL GRANT_ACTIVATED_ROLE('sap.hana.xs.wdisp.admin::WebDispatcherAdmin','XS_ADMIN');
``` 

#### Video Tutorial ####
[![2. Certify Public URL (XS)](https://img.youtube.com/vi/SAoG4T1NFbQ/0.jpg)](https://youtu.be/SAoG4T1NFbQ "SAP Cloud Analytics Live Connections: 2. Certify Public URL (XS) - SAP Digital Enablement")

#### Documentation ####
* [Configure HTTPS (SSL) for Client Application Access](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/d33b259c567441aa97e99228dc0f2088.html) - SAP HANA Administration Guide
* [2532559 - Configure HTTP(S) Access to Multitenant Database Containers via SAP HANA XS Classic](https://launchpad.support.sap.com/#/notes/2532559)  
* [2502174 - HANA Basic How-To Series - Securing HANA XS classic via SSL / HTTPS - using Web Dispatcher Administration and pse container](https://launchpad.support.sap.com/#/notes/2502174)  

### 3a. EPM-MDS ### 
For the tutorial videos, we used the SAP HANA, express edition system as provided by SAP Cloud Appliance Library. This development environment already includes the required EPMMDS plug-in. A standard "out-of-the-box" SAP HANA 2.0 SPS 01 platform edition does not include the EPMMDS component, so you would need to install it. The component is included with the media set or can be downloaded from the SAP ONE Support Launchpad, Software Downloads ("SAP HANA EPM-MDS"). 

* [SAP HANA EPM-MDS 1.0 (SUPPORT PACKAGES AND PATCHES)](https://launchpad.support.sap.com/#/softwarecenter/template/products/%20_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=73555000100200006073&V=MAINT&TA=ACTUAL&PAGE=SEARCH/SAP%20HANA%20EPM-MDS%201.0)

Use the platform lifecycle management tool (hdblcm) to install it. The web interface is accessible from HANA cockpit.  
```	
./hdblcm --components=epmmds --action=install
```
    
#### Documentation ####
* [System Requirements and Technical Prerequisites](https://help.sap.com/doc/00f68c2e08b941f081002fd3691d86a7/release/en-US/11b4e5ff76eb4747bc255d7037be1f01.html) - SAP Analytics Cloud
* [2456225 - Installation and update of the SAP HANA EPM-MDS component](https://launchpad.support.sap.com/#/notes/2456225)
* [2441461 - InformationAccess Service GetServerInfo is not available. Install EPM error appears in SAP Analytics Cloud (BOC)](https://launchpad.support.sap.com/#/notes/2441461)

### 3b. GRANT INA_ROLE (XS) ### 
To connect to the InA service we need the INA_USER role. Any user could be used but a least-privileged user approach is generally recommended. In the code example below we create the INA_USER user and grant the user the INA_USER role. 
```	
CREATE USER ina_user PASSWORD ****;
CALL GRANT_ACTIVATED_ROLE('sap.bc.ina.service.v2.userRole::INA_USER','INA_USER');
``` 
To access specific information views, we need to grant this user SELECT privileges on the views:
```	
GRANT SELECT ON "_SYS_BIC"."MY_USER/MY_VIEW" to "INA_USER"
```

Verify connectivity with GetServerInfo:

    https://myhost.lab.cloud.sap:4390/sap/bc/ina/service/v2/GetServerInfo
   
Verify connectivity with GetResponse:
    
    https://myhost.lab.cloud.sap:4390/sap/bc/ina/service/v2/GetResponse?Request={%22Metadata%22:{%22Expand%22:[%22Cubes%22]}} 

#### Error ####
If the user does not have the INA_ROLE the following error is returned by SAP Analytics Cloud when creating the Live Connection:

    You are not authorized to query the remote system. Please ask your administrator to grant you the InA role. 


The GetServerInfo URL returns the following error when the component is not installed. 

    {"Messages":[{"Number":42001,"Type":2,"Text":"InformationAccess Service GetServerInfo is not available. Install EPM."}]}

#### Video Tutorial ####
[![3. User with INA_ROLE (XS)](https://img.youtube.com/vi/CZUHyZA5pkQ/0.jpg)](https://youtu.be/CZUHyZA5pkQ "[2019.05] SAP Cloud Analytics Live Connections: 3. User with INA_ROLE (XS) - SAP Digital Enablement")

#### Documentation ####
* [2353833 - SAP Analytics Cloud cannot read data from a remote SAP HANA System](https://launchpad.support.sap.com/#/notes/2353833)

### 4. CORS Configuration INA Service (XS) ###
Cross-Origin Resource Sharing (CORS) needs to be enabled for the INA service. 
You can access the tenant database XS artifact administration tool using the URL configured above with path: 

    https://myhost.lab.cloud.sap:4390/sap/hana/xs/admin/

To connect to tool we need the RuntimeConfAdministrator role. Any user could be used but a least-privileged user approach is generally recommended. In the code example below we grant the role to the user created in step 2 above.
```	
CALL GRANT_ACTIVATED_ROLE('sap.hana.xs.admin::RuntimeConfAdministrator','XS_ADMIN');
``` 

Edit the CORS settings for the following package:

    sap.bc.ina.service.v2 

Add the following to Allowed Headers:

    accept
    authorization
    content-type
    x-csrf-token
    x-request-with
    x-sap-cid
    accept-language

Add the following to Exposed Headers: 

    x-csrf-token

Select the following Allowed Methods: 

    GET, HEAD, POST, OPTIONS, PUT

#### Video Tutorial ####
[![4. CORS Configuration (XS)](https://img.youtube.com/vi/znIgIPZjAxI/0.jpg)](https://youtu.be/znIgIPZjAxI "[2019.05] SAP Cloud Analytics Live Connections: 4. CORS Configuration (XS) - SAP Digital Enablement")

#### Documentation ####
* [Live Data Connection to SAP HANA Using a Direct Connection with Password Authentication](https://help.sap.com/doc/00f68c2e08b941f081002fd3691d86a7/2019.7/en-US/58c890e1c89d41e69b2cec31bac2d95f.html) - SAP Analytics Cloud Help
* [Application Runtime Configuration Details](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/0ff6baf4a2a24707b3465face9fa8cf4.html) - SAP HANA Administration Guide

#### Error ####
If CORS has not been configured for the InA service, the following error is returned when creating the Live Connection

    Failed to connect to HANA system. Possible causes: CORS Settings; incorrect credentials. More information can be found on the troubleshooting page.   

### 5. HTTP Server Session Timeout (XS) ###
Default value for the HTTP Server (XS Engine) session timeout parameter is 900 seconds. SAC documentation recommends 12 hours. 
```
ALTER SYSTEM ALTER CONFIGURATION ('xsengine.ini', '<NAME OF THE TENANT DATABASE>') SET('httpserver', 'sessiontimeout') ='43200' WITH RECONFIGURE;
```
#### Video Tutorial ####
[![5. HTTP Server Session Timeout](https://img.youtube.com/vi/p8mv705d1iM/0.jpg)](https://youtu.be/p8mv705d1iM "[2019.05] SAP Cloud Analytics Live Connections: 5. HTTP Server Timeout - SAP Digital Enablement")

### Additional Documentation ### 
* [Troubleshooting Errors](https://help.sap.com/doc/00f68c2e08b941f081002fd3691d86a7/2019.7/en-US/e04b07f3473a43c19f9916df39210b23.html)
* [SAP Analytics Cloud Connectivity Guidelines](https://wiki.scn.sap.com/wiki/display/BOC/SAP+Analytics+Cloud+Connectivity+Guidelines) - SAP Community Wiki
* [SAP Analytics Cloud - Hana live connection Best Practices](https://wiki.scn.sap.com/wiki/display/BOC/SAP+Analytics+Cloud+-+Hana+live+connection+Best+Practices) - SAP Community Wiki
* [SAP Web Dispatcher (WDP)](https://wiki.scn.sap.com/wiki/pages/viewpage.action?pageId=414089394) - SAP Community Wiki

### Components ### 
    HAN-AS-INA-SVC
    LOD-ANA
    HAN-AS-XS
