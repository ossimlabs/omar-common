# Web service configuration

In this document we will address the following:

* Common settings and configuration.
* Common User and Group
* Settings and configuration for each web service.
* Service Templates init.d
* Service Template systemd




# Common Settings and Configuration

The ossim RPM's and depdencies can be found for both dev and master builds

* [https://s3.amazonaws.com/o2-rpms/CentOS/7/master/rpms.tgz](https://s3.amazonaws.com/o2-rpms/CentOS/7/master/rpms.tgz)
* [https://s3.amazonaws.com/o2-rpms/CentOS/7/dev/rpms.tgz](https://s3.amazonaws.com/o2-rpms/CentOS/7/dev/rpms.tgz)

The rpms.tgz will extract to a local directory structure of the form:

```
CentOS/<os version>/<distribution>/x86_64/<all rpms>
```

For example, if you downloaded the RPM tgz [https://s3.amazonaws.com/o2-rpms/CentOS/7/dev/rpms.tgz](https://s3.amazonaws.com/o2-rpms/CentOS/7/dev/rpms.tgz) and issued the command

or the release:

For example, if you downloaded the RPM tgz [https://s3.amazonaws.com/o2-rpms/CentOS/7/master/rpms.tgz](https://s3.amazonaws.com/o2-rpms/CentOS/7/master/rpms.tgz) and issued the command

```
tar xvfz rpms.tgz
```

the directory created would be

```
CentOS/7/dev/x86_64/<ALL RPMS>
```

and for the release

```
CentOS/7/master/x86_64/<ALL RPMS>
```

where **ALL RPMS** is a place holder for the RPM list.


If you want to use our repo on the AWS site then you can create a repo file in your /etc/yum.repos.d/ossim.repo directory location.  

```bash
sudo vi /etc/yum.repos.d/ossim.repo
```

##Create Yum Repo

```yum
[ossim-dev]
name=CentOS-$releasever - ossim packages for $basearch
baseurl=http://s3.amazonaws.com/o2-rpms/CentOS/$releasever/dev/$basearch
enabled=1
gpgcheck=0
metadata_expire=5m
protect=1
```
Or if you want the release RPMS that do not change much until the merge of the dev then use:

```yum
[ossim-release]
name=CentOS-$releasever - ossim packages for $basearch
baseurl=http://s3.amazonaws.com/o2-rpms/CentOS/$releasever/master/$basearch
enabled=1
gpgcheck=0
metadata_expire=5m
protect=1
```

Note, in the above **baseurl** variable we have different branches that you can hook into.  In the path you will see a **dev** path http://s3.amazonaws.com/o2-rpms/CentOS/$releasever/**dev**/$basearch.  We also have the **master** branch that is being built and packaged and you can modify this url to use **master**:  http://s3.amazonaws.com/o2-rpms/CentOS/$releasever/**master**/$basearch.  In the future we will have versioned branches that can be setup and installed.  The dev branch could change hourly and so the yum repo for the dev branch might be in the middle of an update when you are accessing the packages.  The master branch will update much less frequently and will only change when a pull request is performed on the dev branch to merge the changes into master.

When going through the installation for the common services there will be some services that will access imagery directly and might need more rpm's installed to handle more types of data.  We allow these additional/optional installations to be up to the site.   Here is a list of other plugins and RPMS that can be added to any of the service installations:

Here is a current listing of all the ossim RPMS that are in the REPO:

* **ossim-devel** Development files for ossim
* **ossim-libs** Development files for ossim
* **ossim-oms** Wrapper library/java bindings for interfacing with ossim. Any O2 RPM that directly accesses imagery will use this JAVA JNI binding.
* **ossim-oms-devel** Development files for ossim oms wrapper library.
* **ossim-planet** 3D ossim library interface via OpenSceneGraph
* **ossim-planet-devel** Development files for ossim planet.
* **ossim-test-apps** Ossim test apps.
* **ossim-video** Ossim vedeo library.
* **ossim-video-devel** Development files for ossim planet.
* **ossim-wms** wms ossim library
* **omar** OSSIM Server
* **ossim** Open Source Software Image Map library and command line applications
* **ossim-gdal-plugin** GDAL ossim plugin
* **ossim-geopdf-plugin** geopdf ossim plugin
* **ossim-hdf5-plugin** HDF5 ossim plugin
* **ossim-kakadu-plugin** kakadu ossim plugin.  This is probably the most popular because a lot of data is now coming in a J2K compressed form.
* **ossim-kml-plugin** kml ossim plugin
* **ossim-opencv-plugin** OSSIM OpenCV plugin, contains registration code.
* **ossim-openjpeg-plugin** OpenJPEG ossim plugin
* **ossim-png-plugin** PNG ossim plugin
* **ossim-sqlite-plugin** OSSIM sqlite plugin, contains GeoPackage reader/writer.
* **ossim-web-plugin** web ossim plugin
* **ossim-aws-plugin** S3 bucket access for ossim
* **ossim-cnes-plugin** Plugin with various sensor models
* **ossim-potrace-plugin** potrace plugin
* **ossim-jpip-server** ossim kakadu jpip server
* **ossim-geocell** Desktop electronic light table


##JAI Setup

We have supplied the JAI files that can be added to your ext directory of the JVM version you are running

* [jai_core-1.1.3.jar](http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_core-1.1.3.jar)
* [jai_codec-1.1.3.jar](http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_codec-1.1.3.jar)
* [jai_imageio-1.1.jar](http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_imageio-1.1.jar)

If you have access to the public site we execute the following for JVM instances when configuring the OMAR/O2 services and place each jar into the proper jvm/java/jre/lib/ext directory:

```
curl -L http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_core-1.1.3.jar -o /usr/lib/jvm/java/jre/lib/ext/jai_core-1.1.3.jar
curl -L http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_codec-1.1.3.jar -o /usr/lib/jvm/java/jre/lib/ext/jai_codec-1.1.3.jar
curl -L http://s3.amazonaws.com/ossimlabs/dependencies/jai/jai_imageio-1.1.jar -o /usr/lib/jvm/java/jre/lib/ext/jai_imageio-1.1.jar
```

**Note:** Please modifiy the curl download script above for your JAVA installation.  At the time of writing this document we are using OpenJDK version 8.  The O2 services should already have the JAI embedded within the "Fat Jar".


##Setup EPEL

The [Epel](https://fedoraproject.org/wiki/EPEL) site has links to the EL6, EL7, and EL5 RPM repo installations via RPMs.   We have tested the [EL6 RPM](https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm) with the CentOS 6 distribution and [EL7 RPM](https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm) with the CentOS 7 distribution.

If you need to install epel manually you can install the RPM by using yum command for EL7:

```
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

##Firewall Settings Using iptables

By default the web applicaiton will come up and listen on port 8080.  If you are running on an OS you created and not via a Docker image you might want to adjust your iptables to open port 8080 unless you have a proxy:

```bash
sudo vi /etc/sysconfig/iptables

add the line:

-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
sudo service iptables restart
```
Your resulting iptables should look something similar to but not necessarily exact to the following:

```bash

# Generated by iptables-save v1.4.7 on Fri May  6 13:32:29 2016
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [9:608]
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
# Completed on Fri May  6 13:32:29 2016
```

## Firewall Settings using firewalld

On CentOS7 they use the firewalld.   To list your zones you can issue the following command:

```
firewall-cmd --get-active-zones

Output:

public
  interfaces: eth0
```

Now to add port 8080 to the public interface you can execute the following command line application:

```
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
```

Restart the service:

```
sudo systemctl restart firewalld
```

### SELINUX Configuration

If SELINUX is running you are installing on an OS and not a docker then you must enable the boolean flag

**httpd\_can\_network_connect**

To list all booleans for httpd and see if your network connect is turned on you can perform the **getsebool** and get output similar to the following:

```
/usr/sbin/getsebool -a | grep httpd

Output:

httpd_anon_write --> off
httpd_builtin_scripting --> on
httpd_can_check_spam --> off
httpd_can_connect_ftp --> off
httpd_can_connect_ldap --> off
httpd_can_connect_mythtv --> off
httpd_can_connect_zabbix --> off
httpd_can_network_connect --> on
httpd_can_network_connect_cobbler --> off
httpd_can_network_connect_db --> off
httpd_can_network_memcache --> off
httpd_can_network_relay --> off
httpd_can_sendmail --> off
httpd_dbus_avahi --> off
httpd_dbus_sssd --> off
httpd_dontaudit_search_dirs --> off
httpd_enable_cgi --> on
httpd_enable_ftp_server --> off
httpd_enable_homedirs --> off
:
:
:
:
:
```
To set a boolean you can give the setsebool command:

```
sudo setsebool -P httpd_can_network_connect on
```

## Common User and Group

We will use our common user name "omar" and create a group with the same name.  If you are running in a docker you might want to create a home account and remove the --no-create-home and also specify a UID of 1001 and a GID of 0 for docker configurations.

```
adduser -r -d /usr/share/omar --no-create-home --user-group omar
```
Examnple for our docker creation for OpenShift deployments might look like

```
useradd -u 1001 -r -g 0 -d \$HOME -s /sbin/nologin \
                      -c 'Default Application User' omar"
```
This account will be used for running a service with a common "omar" user name and group.


## Service Templates For init.d

Installing from RPM you should not have to add this file but we will specify it here for clarity.  For systems using the startup for init.d we add a file called /etc/init.d/\<program_name>.  The template contents should look similar to the following:

```
#!/bin/bash
#
### BEGIN INIT INFO
# chkconfig: 3 80 20
# Provides: General service wrapper for {{program_name}} services
# Required-Start: $network $syslog
# Required-Stop: $network $syslog
# Default-Start: 3
# Default-Stop: 0 1 2 6
# Should-Start: $network $syslog
# Should-Stop: $network $syslog
# Description: start, stop and restart script
# Short-Description: start, stop and restart ingestListener
### END INIT INFO
RETVAL=$?
PROG={{program_name}}
PROG_USER={{program_user}}
PROG_GROUP={{program_group}}
PID_DIR="/var/run/${PROG}"
PROG_PID_DIR="${PID_DIR}"
PROG_PID="$PID_DIR/${PROG}.pid"
PROG_LOG_DIR="/var/log/${PROG}"
PROG_ERROR_LOG="${PROG_LOG_DIR}/error_log"
export WORKING_DIR="/usr/share/omar/${PROG}"
RUN_PROG_TEST="/usr/share/omar/${PROG}/${PROG}.sh"
RUN_PROGRAM="${RUN_PROG_TEST} ${PROG_PID}"

# Must be exported in order for catatlina to generate pid file
export PROG_PID
start() {
   if [ -f "$RUN_PROG_TEST" ]; then
      # Make omar lock file dir and change ownership to omar user
      if [ ! -d "$PID_DIR" ]; then
            mkdir -p $PID_DIR
            chown $PROG_USER:$PROG_GROUP $PID_DIR
      fi
      # Make omar log directory and change ownership to omar user
      if [ ! -d "$PROG_LOG_DIR" ]; then
            mkdir -p $PROG_LOG_DIR
            chown $PROG_USER:$PROG_GROUP $PROG_LOG_DIR
      fi
      # Check for running instance
      if [ -e "$PROG_PID" ]; then
              read PID < $PROG_PID
                   if checkpid $PID 2>&1; then
                       echo $"$PROG process is already running..."
                           return 1
                           echo_failure
                       else
                           echo "Lock file found but no $PROG process running for (pid $PID)"
                           echo "Removing old lock file..."
                     rm -rf $PROG_PID
                   fi
        fi
         echo -ne $"Starting $PROG: "
         echo $RUN_PROGRAM
            /bin/su - $PROG_USER $RUN_PROGRAM >> $PROG_ERROR_LOG 2>&1
            RETVAL="$?"
          if [ "$RETVAL" -eq 0 ]; then
               echo_success
                  echo -en "\n"
            else
                  echo_failure
                  echo -en "\n"
               exit 1
          fi
   else
      echo "$RUN_PROGRAM does not exist..."
      echo_failure
      echo -en "\n"
      exit 1
   fi
}

stop() {
    COUNT=0
    WAIT=10
    if [ -e "$PROG_PID" ]; then
      read PID < $PROG_PID
#echo ${PID}
        if checkpid $PID 2>&1; then
            echo -ne $"Stopping $PROG: "
         kill $PID >> $PROG_ERROR_LOG 2>&1
         RETVAL="$?"
         sleep 2
            while [ "$(ps -p $PID | grep -c $PID)" -eq "1"  -a  "$COUNT" -lt "$WAIT" ]; do
            echo -ne "\nWaiting on $PROG process to exit..."
            COUNT=`expr $COUNT + 1`
            sleep .3
            done

               if [ "$(ps -p $PID | grep -c $PID)" == "1" ] ; then
                  echo -ne "\nCouldn't shutdown, forcing shutdown of $PROG process"
                  kill -9 $PID
               fi
               echo_success
               echo -en "\n"
      fi
   else
      echo -ne $"Stopping $PROG: "
      echo_failure
      echo -en "\n"
   fi
}

status() {
  RETVAL="1"
    if [ -e "$PROG_PID" ]; then
        read PID < $PROG_PID
        if checkpid $PID 2>&1; then
            echo "$PROG (pid $PID) is running..."
            RETVAL="0"
        else
            echo "Lock file found but no process running for (pid $PID)"
        fi
    else
        PID="$(pgrep -u $PROG_USER java)"
        if [ -n "$PID" ]; then
            echo "$PROG running (pid $PID) but no PID file exists"
            RETVAL="0"
        else
            echo "$PROG is stopped"
            RETVAL="1"
        fi
    fi
    return $RETVAL
}

# Check if pid is running
checkpid() {
   local PID
   for PID in $* ; do
      [ -d "/proc/$PID" ] && return 0
   done
   return 1
}

echo_success() {
   if [ -e "/etc/redhat-release" ]; then
      echo -en "\\033[60G"
      echo -n "[  "
      echo -en "\\033[0;32m"
      echo -n $"OK"
      echo -en "\\033[0;39m"
      echo -n "  ]"
      echo -en "\r"
   elif [ -e "/etc/SuSE-release" ]; then
      echo -en "\\033[60G"
      echo -en "\\033[1;32m"
      echo -n $"done"
      echo -en "\\033[0;39m"
      echo -en "\r"
   else
      echo -en "\\033[60G"
      echo "OK"
      echo -en "\r"
   fi
   return 0
}

echo_failure() {
   if [ -e "/etc/redhat-release" ]; then
      echo -en "\\033[60G"
      echo -n "["
      echo -en "\\033[0;31m"
      echo -n $"FAILED"
      echo -en "\\033[0;39m"
      echo -n "]"
      echo -en "\r"
   elif [ -e "/etc/SuSE-release" ]; then
      echo -en "\\033[60G"
      echo -en "\\033[0;31m"
      echo -n $"failed"
      echo -en "\\033[0;39m"
      echo -en "\r"
   else
      echo -en "\\033[60G"
      echo "FAILED"
      echo -en "\r"
   fi
    return 1
}

# See how we were called.
case "$1" in
 start)
   start
   ;;
 stop)
   stop
   ;;
restart)
   stop
    sleep 2
    start
    ;;
status)
   status
   ;;
 *)
   echo $"Usage: $PROG {start|stop|restart|status}"
   exit 1
   ;;
esac

exit $RETVAL
```
* **{{program_name}}** is replaced by the web service name:  For example wmts-app.
* **{{program_user}}** is replaced by the user.  In this case the username is *omar*.
* **{{program_group}}** is replaced by the group name.  In this case we use the group name *omar*.

If you were creating a startup script for wmts-app then all occurances of the pattern {{program_name}} would be replaced with wmts-app.


## Service Templates For Systemd

Using the CentOS 7 RPM repo for the OMAR distribution it will automatically install the systemd startup scripts in the location /usr/lib/systemd/system/<app-name>.service.   The contents of each systemd service file will look something like the following:

```bash
[Service]
PermissionsStartOnly=true
Type=forking
PIDFile=/var/run/{{program_name}}/{{program_name}}.pid
ExecStart=/bin/bash -c "/usr/share/omar/{{program_name}}/{{program_name}}.sh /var/run/{{program_name}}/{{program_name}}.pid >> /var/log/{{program_name}}/{{program_name}}.log 2>&1" &
User={{program_user}}
Group={{program_group}}
WorkingDirectory=/usr/share/omar/{{program_name}}
Restart=on-abort
```
* **{{program_name}}** is replaced by the web service name:  For example wmts-app.
* **{{program_user}}** is replaced by the user.  In this case the username is *omar*.
* **{{program_group}}** is replaced by the group name.  In this case we use the group name *omar*.

All web application are installed under the /usr/share/omar/\<program_name> directory.

## Common Config Settings

Most services will have these common config settings.  These config settings can be copied into the services application.yml and added to any services config options.  If you are not using a config server where multiple configuration can be merged together then you must specify all your configuration in one file.  In this example we pulled from our config-server and we will setup common variables that can be used at other configuration.  We will create our own common variables and then can be used by other configurations.

```
serverName: "localhost"
serverProtocol: "http"

omarDb:
  host:
  port: 5432
  name: omardb-prod
  url: jdbc:postgresql://${omarDb.host}:${omarDb.port}/${omarDb.name}
  driver: org.postgresql.Driver
  dialect: 'org.hibernate.spatial.dialect.postgis.PostgisDialect'
  username:
  password:

server:
  contextPath:
  port: 8080

---
grails:
  serverURL:${serverProtocol}://${serverName}${server.contextPath}
  assets:
    url: http://<ip>:8080/assets/

---

# These settings are optional and only used with Spring Boot Admin application
spring:
  boot:
    admin:
      url: http://<ip>:<port>/<context>/
      auto-deregistration: true
      client:
        prefer-ip: true

```

* **serverName** Common variable that will be used to identify the server name that all requests will go through.  If you are going through a proxy then you can specify that server DNS and port if needed.
* **serverProtocol** Common variable that will be used to identify the protocol used.  This will be either http or https
 * **contextPath** You can specify the context path and this is added to the URL to the server.  If the context is say "O2" then to access the url root path you will need to proxy to the location \<ip>:\<port>/O2
* **server**
 * **contextPath** Each service can specify their context path if you want to add context.  The context is prefixed by any URL path endpoint.
 * **port**  Defines the port that this service will listen on.  Default is port 8080
* **serverURL** point to the root location of the wmts-app server. The example goes directly to the service via 8080.  If a proxy is used then you must add the proxy end point.
* **assets**** This is the url to the assets location.  Just add the **/assets/** path to the serverURL.  If you are going through a proxy and the proxy path is different than you service path then you must specify the asset path.  If they are the same then leave this out and do not use.
  * **url** This is only needed if you are going through a proxy and the context-path for the server is different when going through the proxy.  Fro example, if you have a proxy path with http://[my-proxy]/path/to/service and the service path is also /path/to/service then you do not have to specify the assets.

##### Spring Boot Admin

The following settings are _optional_. They are only used in conjunction with the  [Spring Boot Admin](http://codecentric.github.io/spring-boot-admin/1.5.4/#_what_is_spring_boot_admin) server.  The O2 applications have the [Spring Boot Admin Client](http://codecentric.github.io/spring-boot-admin/1.5.4/#register-clients-via-spring-boot-admin) included, and are used to allow the SBA client and SBA server to communicate.

OMAR Spring Boot Admin documentation is located [here](../../../omar-admin-server/docs/install-guide/omar-admin-server/).

Note: You will also need to utilize the common endpoints configuration settings in the following section (Common Endpoints Settings).

* **spring**
  * **admin**
    * **url** URL of the Spring Boot Admin Server
    * **auto-degristration** Switch to enable auto-deregistration at Spring Boot Admin server when context is closed. (Default is false)
    * **client**
      * **prefer-ip** Use the ip-address rather then the hostname in the guessed urls. If server.address / management.address is set, it get used. Otherwise the IP address returned from InetAddress.getLocalHost() gets used.

## Common Endpoints Settings (Enable/Disable)

All the services that start with an application yaml file definition now has to have certain endpoints enabled before you can reach them.  If you need access to the **/heatlh** endpoint then it must be enabled.  Add an entry to the applicaitons YAML file defintion for getting the health of the service.  These settings are used for the Spring Boot Admin, and the Actuator.w

```
endpoints:
  enabled: true
  health:
    enabled: true
  actuator:
    sensitive: false
  beans:
    sensitive: false
  metrics:
    sensitive: false
  configprops:
    sensitive: false
  trace:
    sensitive: false
  mappings:
    sensitive: false
  env:
    sensitive: false
```

This will enable the endpoint .../health to be accessed and should return a JSON formatted string describing the status of the service.  This is a common endpoint that can be enabled so a third party program can watch the service to make sure it's still alive and well.  List of some but not all endpoints:

* **health** Shows the health of the service.
* **env** Exposes properties from Spring’s ConfigurableEnvironment.
* **metrics**  Shows ‘metrics’ information for the current application.
* **mappings** Shows the endpoint mappings for the endpoints
* **info** Shows the version information of the service running
* **restart** Allows one to retart a service
* **shutdown** Allows one to shutdown a service

For a complete list of endpoints please visit the spring boot page found at: [Spring Boot Endpoints](http://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html).

## Common Database

We typically use a common database server to store any service specific table data.  Not all services use this common setting but will be repeated in the services that use it.  Within this installation we have tested against a Postgres database server.  All services, with exception to the jpip-server that does not have an external configuration, will have a common configuration entry in their yaml file that contains an entry of the form:

```
environments:
  production:
    dataSource:
      url: ${omarDb.url}
      host: ${omarDb.host}
      port: ${omarDb}.port
      driverClassName: ${omarDb.driver}
      username: ${omarDb.username}
      password: ${omarDb.password}
      dialect: ${omarDb.dialect}
      pooled: true
      jmxExport: true
```

* **dataSource**
 * **url** In each of the individual service documentation they will describe further where their configuration yaml file is located. In the connection you will need to specify the **url** to connect to. The **ip** and **port** will need to be replaced with your database server instance. Postgres typically defaults to **port** 5432.
 * **host** host name of the database.  This is the DNS name part without the http and paths
 * **port** port.  For postgres this is typically 5432
 * **username** username for the database.
 * **password** password for the database.

## Logging

Most of the services that start with the external AML file support external Logging overrides.  Let's say we have a common file under /usr/share/omar/logback.groovy then we can add to the YAML file the tags:

```
logging:
  config: "/usr/share/omar/logback.groovy"
```

An example content of the external logback could look like the following:

```
import grails.util.BuildSettings
import grails.util.Environment

// See http://logback.qos.ch/manual/groovy.html for details on configuration
appender('STDOUT', ConsoleAppender) {
    encoder(PatternLayoutEncoder) {
        pattern = "%level %logger - %msg%n"
    }
}

logger 'grails.app.jobs', INFO, ['STDOUT']
logger 'grails.app.services', INFO, ['STDOUT']
logger 'grails.app.controllers', INFO, ['STDOUT']

root(ERROR, ['STDOUT'])

def targetDir = BuildSettings.TARGET_DIR

if (Environment.isDevelopmentMode() && targetDir) {
    appender("FULL_STACKTRACE", FileAppender) {
        file = "${targetDir}/stacktrace.log"
        append = true
        encoder(PatternLayoutEncoder) {
            pattern = "%level %logger - %msg%n"
        }
    }
    logger("StackTrace", ERROR, ['FULL_STACKTRACE'], false)
}
```

Which logs all jobs, services, and controllers INFO levels to STDOUT.


# Trouble Shooting

This section is devoted to hints and tricks to problems that might be encountered during the setup and execution of the services provided in the O2 distribution.

## Entropy Problems and Slow Startup Times

Tomcat on startup uses the SecureRandom class for for getting secure random values for it's session ids. If the entropy source for initializing the SecureRandom class is short of entropy we have seen delays of up to 14 or 15 minutes to bring up a web service.  

If you turn on logging you will see a long pause and a print of the line that will look similar to:

`<DATE> org.apache.catalina.util.SessionIdGenerator createSecureRandom
INFO: Creation of SecureRandom instance for session ID generation using [SHA1PRNG] took [5172] milliseconds.`

To resolve this issue you can either use a non-blocking random generator by passing `-Djava.security.egd=file:/dev/./urandom` as a java argument to the JVM.  

If you are running as a docker container you can add the following to the docker run command:

 `docker run -v /dev/urandom:/dev/random`

without having to modify the instance the docker daemon is running on.

You can also install an RPM called ***haveged***.  For RPM based systems you can install using the command:

```
yum install haveged
chkconfig haveged on
```
