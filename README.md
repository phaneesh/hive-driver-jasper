# Hive JDBC Driver for JasperServer
This is a hive jdbc driver shaded to be compatible with Jasper Server 7.5 

### Compatibility
* Java 1.8+
* HDP Version 3.1
* Hive 3.1.0

### Download Release
Download a version of ready to use driver compatible with your stack version. 
Release naming is inline with HDP stack and versions 
 
### Build instructions
  - Clone the source:

        git clone github.com/phaneesh/hive-driver

  - Build

        mvn package

### Usage
* Place the built shaded jar ```target/hive-driver-jasperserver-3.1.0.3.1.0.0-78.jar``` in ```jasperserver/WEB-INF/lib``` 
* Change ```jasperserver/WEB-INF/applicationContext.xml``` to disable ```defaultReadOnly``` and ```autoCommit``` for hive JDBC connections
```xml
<bean id="jdbcDataSourceServiceFactory" class="com.jaspersoft.jasperserver.api.engine.jasperreports.service.impl.JdbcReportDataSourceServiceFactory">
    <property name="pooledJdbcDataSourceFactory">
        <ref bean="dbcpJdbcDataSourceFactory"/>
    </property>
    <property name="autoCommitUnsupportedDrivers">
        <set>
            <value>com.simba.cassandra.jdbc4.Driver</value>
            <value>com.simba.impala.jdbc.Driver</value>
            <value>com.simba.spark.jdbc.Driver</value>
            <value>com.simba.googlebigquery.jdbc41.Driver</value>
            <value>org.apache.hive.jdbc.HiveDriver</value>
        </set>
    </property>
    <property name="driverNoAuthMethMap">
        <map>
            <entry key="com.simba.cassandra.jdbc4.Driver" value="0"/>
            <entry key="com.simba.impala.jdbc.Driver" value="0"/>
            <entry key="com.simba.spark.jdbc.Driver" value="2"/>
        </map>
    </property>
    <property name="driverAuthMethMap">
        <map>
            <entry key="com.simba.cassandra.jdbc4.Driver" value="1"/>
            <entry key="com.simba.impala.jdbc.Driver" value="3"/>
            <entry key="com.simba.spark.jdbc.Driver" value="3"/>
        </map>
    </property>
    <property name="poolTimeout" value="900"/>
    <property name="defaultReadOnly" value="false"/>
    <property name="profileAttributesResolver" ref="profileAttributesResolver"/>
</bean>
```

### Kerberos Configuration
If the HDP cluster is secured with kerberos; additional configuration is required.
* Setup ```jaas.conf``` to enable kerberos in ```tomcat```
```
com.sun.security.jgss.initiate {
    com.sun.security.auth.module.Krb5LoginModule required
    doNotPrompt=true
    principal="<kerberos principal>"
    useKeyTab=true
    keyTab="</path/to/keytab>"
    serviceName="hive"
    useTicketCache = false
    client=true
    storeKey=true;
};
```
* Setup ```CATALINA_OPTS``` in ```setenv.sh``` to configure ```JAAS```
```bash
CATALINA_OPTS="$CATALINA_OPTS -Dsun.security.krb5.debug=true -Djavax.security.auth.useSubjectCredsOnly=false -Djava.security.krb5.conf=/etc/krb5.conf -Djava.security.krb5.realm=<your realm> -Djava.security.krb5.kdc=<kdc host> -Djava.security.auth.login.config=/path/to/jaas.conf"
```    
### LICENSE
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.