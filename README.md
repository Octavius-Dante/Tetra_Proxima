# Tetra_Proxima

#### BTP Training - Test applications (28/02/2024)
#### Maven Repository : https://mvnrepository.com/
#### Ui5 Libray : https://sapui5.hana.ondemand.com/#/controls  
#### NPM Js Repository : https://npmjs.com  
#### JSON structure validator : https://jsonlint.com  
#### Spring framework reference documentation : https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/htmlsingle/

 </br>
 </br>

</br>
Maven is a dependency manger for JAVA programming
</br>
NPM -node pacakge manager is a dependency manger for Node JS
</br>
</br>

 http://localhost:8080/APP_end_point_name 

 </br>
 </br>
 
https://developers.sap.com/tutorials/btp-cf-buildpacks-java-create.html

https://github.com/SAP/btp-environment-variable-access

https://help.sap.com/docs/btp/sap-business-technology-platform/developing-java-in-cloud-foundry-environment

https://github.com/SAP/cloud-security-services-integration-library/blob/main/java-security/README.md

 </br>
 </br>
 
### Steps for Postgre DB integration : 
 <p>
 </br>
1. Add conenction properties for our postgre DB running locally
 </br>
 </br>
2. We need annotations to our POJO class to specify that our class will act like and entity.
   </br>
   We need to add annotations which will also tell JPA taht my entity have a primary key and colums.
   </br>
   It may so happen that your class property name and your DB columns names may different. EG. @Emtoty ,@Id
 </br>
 </br>
3. We need to add a special interface which inherits from JPARepository interface, which will proovide all the runtime           
   capabilities (CURDQ - Create, Update, Read, Delete, Query).
 </br>
 </br>
4. Replace hardcoded data in our service class with Read DB Operations. 
 </br>
  </br>
  </p>


### Spring Boot Connect to PostgreSQL Database Examples 

https://www.codejava.net/frameworks/spring-boot/connect-to-postgresql-database-examples
</br>
https://studygyaan.com/spring-boot/how-to-connect-postgresql-database-in-spring-boot-project

</br>
</br>

## Spring Boot + PostgreSQL + UUID String
</br>

It’s easy to use UUID String as Primary Key when you are using PostgreSQL database.
</br>
To archive this, just configure your Entity to use UUID2 generator.
</br>

```java

@Id
@GenericGenerator(name = "uuid2", strategy = "uuid2")
@GeneratedValue(strategy = GenerationType.IDENTITY, generator = "uuid2")
@Column(length = 36, nullable = false, updatable = false)
private String id;

```
 </br>
 </br>
 </br>


## Shortcut keys  
 </br>
 </br>
 Ctrl + Shift + F  -- Code Alignment 
 </br>
 Ctrl + Shift + C  -- Code comments 
 </br>
 Ctrl + Shift + T -- Open Type window
 </br>
 Ctrl + Shift + L -- Show key assist
 
 </br> 
 </br>
 </br>
https://www.baeldung.com/spring-data-jpa-batch-inserts
</br>
</br>

</br>
</br>
Passing Native Query EXAMPLES 
</br>
https://stackoverflow.com/questions/66071165/how-to-pass-parameters-in-a-native-query-jpa

</br>
</br>
https://www.bezkoder.com/jpa-native-query/


</br>
</br>

</br>
</br>


## SAP UI5
</br>
</br>

SAP UI5 is a  framework to develop responsive web applcaition for SAP.

</br>
</br>

- [x] Framework - A collection if libraries
- [x] Library - A collection of classes
- [x] Class - A collection of attributes, events, functions and aggregations

</br>
</br>

#### SAP UI5 SDK
</br>
https://sapui5.hana.ondemand.com/
</br>
</br>
XMLNS - XML namespace

</br>
</br>
</br>


#### What is the Apache Olingo Project? 
</br>
</br>
Apache Olingo is a Java library that implements the Open Data Protocol (OData). Apache Olingo serves client and server aspects of OData. It currently supports OData 2.0 and will also support OData 4.0. The latter is the OASIS version of the protocol: OASIS Open Data Protocol (OData) TC.
</br>
</br>
</br>

#### What is App router 
</br>

- [X] It is a Node JS Application
- [X] It is used to aautomate the process of authentication, authorization with XSUAA to do token exchange
- [X] It sits in the middle of idp (identity provider) and XSUAA, it will redirect the request to our micorservice to XSUAA and eventually to IDP.
- [X] IDP sends the login screen to the user, user authenticates, which is taken to XSUAA, XSUAA issues JWT token(java authnticated web token), this JWT token is presented to api end points.
- [X] App router takes th JWT token from XSUAA and give ir ro our microservices and even exchange this token to multiple microservices inside.
- [X] It serves as a single entry point for entire app.
- [X] To tell app router about our multiple microservices so that it can re-direct, we need to create a configuration file named as <b>xs-app.json</b> which contains the details.
</br>

#### App router is an application which does internal communciations between micro services to give single entry point for user it communicates to XSUAA to get access tokens.
</br>


#### What is XSUAA
</br>
Extensibility services for user access authorizations which is responsible for authorization to access the BTP services
</br>
</br>

#### What is node JS.
</br>
Node JS is an open source, cross platform Java Script runtime environemnt and used for executing Java Script code out side the web browser. Java and JavaScript are different by their nature, design and use case, Java Script was dominantly used as a web programming language. All the browsers directly understand Java Script.
</br>
Node JS is also a great web framework for beigneers becuse it works great with data-intensive applciations like straming, real-time apps. it is free and easy to elarn Node JS.
</br>
</br>

#### Applications of Node JS?
</br>
Build Business logic on server side, DB lookups, send emails, write valdiations, create microservices, add automations, show output, build servers, host web apps, make excel/pdf more.

</br>
</br>

#### NPM - Node Pacakge Manager
</br>

- [X] It is dependency manager tool which gets installed along with node js in our system.
- [X] Just like for java we had maven, we have npm for node js.
- [X] we can find millions of resuabel node modules free on internet, which helps us avoid rebuilding everything/ reinvent the wheel.
- [X] world largest resuable code repository NPM Js repsoitory : https://npmjs.com

</br>
</br>

- [X] Globally at computer level, all projects can resue, option used to install CLI
  </br>
  ```bat
  npm install -g module 
  ```
  </br>
- [X] With in the project - @local project level, only single project can use
  </br>
  ```bat
  npm install module
  ```
  </br>
- [X] when we build java project we saw pom.xml which contains dependencies. we have the <b> package.json </b> file in the node project whcih contains details about the entire project and its dependencies to create this we use.
  </br>
  
  ```bat
  npm init
  ```
  </br>
