![SAP HANA Academy](https://yt3.ggpht.com/-BHsLGUIJDb0/AAAAAAAAAAI/AAAAAAAAAVo/6_d1oarRr8g/s100-mo-c-c0xffffffff-rj-k-no/photo.jpg)
# SAP Analytics Cloud - Live Connections #
### Tutorial Video Playlist ### 
[SAP Analytics Cloud Live Connection](https://www.youtube.com/playlist?list=PLkzo92owKnVxm3TMoUliy5b5UO1G5GP50)

## Live Connections ##
Text.


(https://www.sapanalytics.cloud/wp-content/uploads/2018/12/SAC_DataConnections-1.2.png)


### Create Live Connection ### 
Long story, short. Creating a Live Connection to a SAP HANA system on SAP Analytics Cloud is a simple task for the business user once all the prerequisites have been met. 
We need to provide the public URL for the system, the HTTPS port, and for basic authentication a user name and password, e.g.:
```
host = myhost.lab.cloud.sap
port = 4390
user = INA_USER
password = *******
```

[![Create Live Connection](https://img.youtube.com/vi/GLs3ByA9WvY/0.jpg)](https://youtu.be/GLs3ByA9WvY "[2019.05] SAP Cloud Analytics Live Connections: Create Connection - SAP Digital Enablement")

### 1. Configure Public URL (XS) ### 

[![1. Public URL (XS)](https://img.youtube.com/vi/qajuw5R6Das/0.jpg)](https://youtu.be/qajuw5R6Das "[2019.05] SAP Cloud Analytics Live Connections: 1. Public URL (XS) - SAP Digital Enablement")

### 2. Certify Public URL (XS) ### 
[![2. Certify Public URL (XS)](https://img.youtube.com/vi/https://youtu.be/SAoG4T1NFbQ/0.jpg)](https://youtu.be/SAoG4T1NFbQ "[2019.05] SAP Cloud Analytics Live Connections: 2. Certify Public URL (XS) - SAP Digital Enablement")

### 3. GRANT INA_ROLE (XS) ### 
[![3. User with INA_ROLE (XS)](https://img.youtube.com/vi/CZUHyZA5pkQ/0.jpg)](https://youtu.be/CZUHyZA5pkQ "[2019.05] SAP Cloud Analytics Live Connections: 3. User with INA_ROLE (XS) - SAP Digital Enablement")

### 4. CORS Configuration INA Service (XS) ###
[![4. CORS Configuration (XS)](https://img.youtube.com/vi/znIgIPZjAxI/0.jpg)](https://youtu.be/znIgIPZjAxI "[2019.05] SAP Cloud Analytics Live Connections: 4. CORS Configuration (XS) - SAP Digital Enablement")

Add the following to Allowed Headers:

    accept
    authorization
    content-type
    x-csrf-token
    x-request-with
    x-sap-cid
    accept-language

Add the following to Exposed Headers: x-csrf-token.
Select the following Allowed Methods: GET, HEAD, POST, OPTIONS.

### 5. HTTP Server Session Timeout (XS) ###
[![5. HTTP Server Session Timeout](https://img.youtube.com/vi/p8mv705d1iM/0.jpg)](https://youtu.be/p8mv705d1iM "[2019.05] SAP Cloud Analytics Live Connections: 5. HTTP Server Timeout - SAP Digital Enablement")


* [Configure HTTPS (SSL) for Client Application Access](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/d33b259c567441aa97e99228dc0f2088.html)

## Code Sample ##
In this example for data masking, you execute the following statements to create three users.
```
CREATE USER xs_admin PASSWORD Password1234 NO FORCE_FIRST_PASSWORD_CHANGE;

CREATE USER ina_user PASSWORD Password1234 NO FORCE_FIRST_PASSWORD_CHANGE;

```
Connect as xs_admin

## Code Sample ##
The user with the INA_USER role ("INA_USER") needs SELECT privileges on the views in the _SYS_BIC schema.
```	
GRANT SELECT ON "_SYS_BIC.MY_VIEW" to "INA_USER"
```
* [2353833 - SAP Analytics Cloud cannot read data from a remote SAP HANA System](https://launchpad.support.sap.com/#/notes/2353833)

### Documentation ### 
* [Live Data Connection to SAP HANA Using a Direct Connection and SSO](https://help.sap.com/doc/00f68c2e08b941f081002fd3691d86a7/release/en-US/780842e11ee44dd6b5e7bd23cf393aff.html)
* [SAP Analytics Cloud Connectivity Guidelines](https://wiki.scn.sap.com/wiki/display/BOC/SAP+Analytics+Cloud+Connectivity+Guidelines) - SAP Community Wiki
* [SAP Analytics Cloud - Hana live connection Best Practices](https://wiki.scn.sap.com/wiki/display/BOC/SAP+Analytics+Cloud+-+Hana+live+connection+Best+Practices) - SAP Community Wiki
