# keycloak-nodejs-example

A simply step by step Keycloak, MySQL and Node.js integration tutorial. 

## Download Keycloak

Download the last version of Keycloak (this example uses 2.5.4.Final)
http://www.keycloak.org/downloads.html

## Configure Keycloak to use MySQL

Perform this steps to get MySQL configured for Keycloak:
https://keycloak.gitbooks.io/server-installation-and-configuration/content/topics/database/checklist.html

There is an error in the documentation — driver should be in the
`modules/system/layers/base/com/mysql/driver/main` catalog. 

The last MySQL driver
https://mvnrepository.com/artifact/mysql/mysql-connector-java

##### `module.xml`
```XML
<module xmlns="urn:jboss:module:1.3" name="com.mysql.driver">
 <resources>
  <resource-root path="mysql-connector-java-6.0.5.jar" />
 </resources>
 <dependencies>
  <module name="javax.api"/>
  <module name="javax.transaction.api"/>
 </dependencies>
</module>
```

##### `part of  standalone.xml`
```XML
<datasources>
...
<datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
<connection-url>jdbc:mysql://localhost:3306/keycloak</connection-url>
    <driver>mysql</driver>
    <pool>
        <max-pool-size>20</max-pool-size>
    </pool>
    <security>
        <user-name>root</user-name>
        <password>root</password>
    </security>
</datasource>
...
</datasources>

<drivers>
...
<driver name="mysql" module="com.mysql.driver">
    <driver-class>com.mysql.jdbc.Driver</driver-class>
</driver>
...
</drivers>
```

To fix time zone error during startup, `connection-url` can be
`jdbc:mysql://localhost:3306/keycloak?serverTimezone=UTC`

Database schema creation takes a long time. 

## Basic configuration

1. Run server using standalone.sh (standalone.bat)

2. You should now have the Keycloak server up and running. 
To check that it's working open [http://localhost:8080](http://localhost:8080). 
You will need to create a Keycloak admin user.
Then click on `Admin Console` https://keycloak.gitbooks.io/documentation/getting_started/topics/first-boot/admin-console.html.

3. Create a `CAMPAIGN_REALM` realm https://keycloak.gitbooks.io/documentation/getting_started/topics/first-realm/realm.html

4. Create users: `admin_user`, `advanced_user`, `basic_user` (don't forget to disable `Temporary` password) 
https://keycloak.gitbooks.io/documentation/getting_started/topics/first-realm/user.html

5. Create realm roles: `ADMIN_ROLE`, `ADVANCED_USER_ROLE`, `BASIC_USER_ROLE`
https://keycloak.gitbooks.io/server-adminstration-guide/content/topics/roles/realm-roles.html<br><br>
*Noitice*: Each client can has their own "client roles", scoped only to the client
https://keycloak.gitbooks.io/server-adminstration-guide/content/topics/roles/client-roles.html

6. Add roles to users: `admin_user` — `ADMIN`, `advanced_user` — `ADVANCED_USER`, `basic_user` — `BASIC_USER_ROLE`
https://keycloak.gitbooks.io/server-adminstration-guide/content/topics/roles/user-role-mappings.html

7. Create a `CAMPAIGN_CLIENT`

* Client ID:  CAMPAIGN_CLIENT
* Client Protocol: openid-connect
* Access Type:  Confidential 

* Standard Flow Enabled: ON
* Implicit Flow Enabled: OFF
* *Direct Access Grants Enabled: OFF* should be ON for the custom login 
* Service Accounts Enabled: ON 
* *Authorization Enabled: ON* to add polices
* Valid Redirect URIs: /login
* Web Origins: *

https://keycloak.gitbooks.io/server-adminstration-guide/content/topics/clients/client-oidc.html

## Configure permissions

1. Using `Authorization -> Policies` add role based polices

* CREATE_CUSTOMER_POLICY -> ADMIN_ROLE
* CREATE_CAMPAIGN_POLICY -> ADMIN_ROLE ADVANCED_USER_ROLE
* SHOW_REPORTS_POLICY -> ADMIN_ROLE, ADVANCED_USER_ROLE, BASIC_USER_ROLE
https://keycloak.gitbooks.io/authorization-services-guide/topics/policy/role-policy.html 

2. Using `Authorization -> Permissions` add resource-based permissions

`createCustomer ADMIN`
`createCampaign ADMIN ADVANCED_USER`
`showReports ADMIN ADVANCED_USER BASIC_USER`

https://keycloak.gitbooks.io/authorization-services-guide/topics/permission/create-resource.html



 




10. Create `keycloak.json` using `Client Authenticator = Client id and secret` (**TODO need investigate**):
https://keycloak.gitbooks.io/server-adminstration-guide/content/topics/clients/oidc/confidential.html

11. Download `keycloak.json` from `Installation`

12. Clone this project https://github.com/v-ladynev/keycloak-nodejs-example.git

13. Copy `keycloak.json` in the root of the project

14. Run `npm install` in the project directory to install Node.js libraries

15. `npm start` to run node.js application

## Custom login
[Change Keycloak login page, get security tokens using REST]
(http://stackoverflow.com/questions/39356300/avoid-keycloak-default-login-page-and-use-project-login-page)
[Obtain access token for user]
(https://keycloak.gitbooks.io/server-developer-guide/content/v/2.2/topics/admin-rest-api.html)


# Links

Keycloak uses _JSON web token (JWT)_ as a barier token format. To decode such tokens: https://jwt.io/

