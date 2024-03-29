# REGIONAlert
##### (former AlertCity)
## An alert city system

[![MUSANet](https://raw.githubusercontent.com/meslin8752/InterSCity-onibus/master/PoweredByMUSANet.png)](https://musanet.meslin.com.br/)

This project is an alert manager.


### Running REGIONAlert:

**Step 1: InterSCity**

Make sure you have access to the InterSCity platform. You can check if the platform is online using the following command. In this example, we suppose InterSCity is at IP address `172.16.110.131` using TCP port 8000. Please, replace with the correct IP address or FQDN, and TCP port.
```sh
curl -X GET "http://172.16.110.131:8000/catalog/capabilities"
```

Instructions on installing InterSCity can be found [here](https://gitlab.com/interscity/interscity-platform/interscity-platform/-/tree/master/deploy).

> Note: `Capability`'s are created by `br.com.meslin.alert.servlet.listener.CheckInterSCity` (only) when the web server starts (only). That means, if you have some problem with InterSCity and need to make a fresh start, you need to restart the whole application.

**Step 2: ContextNet**

You have two options for running ContextNet:

(1) you can install ContextNet using the instructions available [here](http://wiki.lac.inf.puc-rio.br/doku.php) and run an instance of ContextNet using the following command. Please, replace the path according to your ContextNet installation.
```sh
$ java -jar /opt/ContextNet/contextnet-2.7.jar 127.0.0.1 5500 OpenSplice
```

(2) using Docker, you can run the container described below with ContextNet installed. Pay attention to the container's IP address when configuring REGINALert and HumanMobility.
```sh
$ sudo docker run meslin/contextnet
```

**Step 3: REGIONAlert**

You have two options for running REGIONAlert:

(1) Clone this project.

Please, configure:

* Border database physical directory at:
  * `br.com.meslin.alert.servlet.GetRegion.java`
  * parameters:
    * ~~(deprecated since 2022-01-11) `-f Bairros/RioDeJaneiro.lista`~~
    * (optional since 2022-01-11) `-w /media/meslin/4E7E313D7E311EE1/Users/meslin/Google Drive/workspace-desktop-ubuntu/RegionAlert/`
* InterSCity IP address and TCP port at:
  * WebContent/WEB-INF/web.xml
  * ~~(deprecated since 2022-01-15) `br.com.meslin.alert.connection.Constants.java` (???)~~
  * ~~(deprecated since 2022-01-15) parameter `-i VM005`~~
* Database at (address, user, and password):
  * `br.com.meslin.alert.dao.UserDAO`
    * CLASSNAME
    * DBURL
    
The following environment variables can also be configure and they have priority (they are checked first) over command line parameters and web.xml values:

* REGIONALERT_GATEWAYIP
* REGIONALERT_GATEWAYPORTNUMBER
* REGIONALERT_GROUPDESCRIPTIONFILENAME
* REGIONALERT_INTERSCITYIPADDRESS
* REGIONALERT_WORKDIR

Execute the class `br.com.meslin.alert.main.MyContextNetCore` as Java Application and the project (or `WebContent/index.html`) on server.

(2) Using Docker:

Run meslin/regionalert as a Docker image. The following exemplify how to run:
```sh
$ sudo docker run -e REGIONALERT_GATEWAYIP=10.0.0.101 -e REGIONALERT_GATEWAYPORTNUMBER=5500 -e REGIONALERT_GROUPDESCRIPTIONFILENAME=Bairros/RioDeJaneiro.lista -e REGIONALERT_INTERSCITYIPADDRESS="172.16.110.131:8000" -e REGIONALERT_WORKDIR=/opt/REGIONAlert/ -it regionalert
```
Configure the following environment variables as Docker parameters according to your needs:
* REGIONALERT_GATEWAYIP=10.0.0.101 &rarr; ContextNet-Gateway IP address
* REGIONALERT_GATEWAYPORTNUMBER=5500 &rarr; ContextNet-Gateway TCP port-number
* REGIONALERT_GROUPDESCRIPTIONFILENAME=Bairros/RioDeJaneiro.lista &rarr; Group description filename related to WORKDIR (se bellow) 
* REGIONALERT_INTERSCITYIPADDRESS="172.16.110.131:8000" &rarr;  InterSCity IP address or FQDN, and TCP port-number
* REGIONALERT_WORKDIR=/opt/REGIONAlert/ &rarr; Word directory, including the last slash

> Note: You still need ContextNet Gateway and InterSCity platform as described above. Also, don't forget to run HumanMobility. 

**Step 4: HumanMobility**
To run REGIONAlert, you will need the [HumanMobility](https://github.com/meslin8752/HumanMobility.git) project to simulate users walking around the city.


### Environment
* [Java 8](https://www.java.com/en/download/)
* [Apache Tomcat 8.5](https://tomcat.apache.org/download-80.cgi)
* [MySQL](https://dev.mysql.com/downloads/)
* [Docker](https://www.docker.com/) - optional


### History
* 2022-02-03
  * Added support to configuration via environment variable. This support was necessary to dockerize REGIONAlert
  
* 2022-01-24
  * Now PutAlert accept GET and POST methods to create a new alert

* 2022-01-15
  * Create table statement executed when instantiating database object

* 2022-01-11
  * Fixed: get initial parameters from web
  * `-w` command line parameter no longer needed (now, it's optional)
  * `-f` command line parameter no longer needed
  * all configuration parameters are configured in `web.xml`
  * capability "uv" replaced by fake capability "fake" because the new InterSCity version no longer has uv as a default configured capability

* 2022-01-07
  * Database changed from MySQL to SQLite due to changes in MySQL package
  
  
### TODO
* Include timestamp and duration on each event
    
