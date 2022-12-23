# BAStudio datasource and jdbc library configuration

Configuring 'CP4BA deployment' for the 'icp4adeploy' CR (in a Starter deployment profile).

Let's add the 'custom_xml' subsection in the 'bastudio_configuration' section with an xml snippet that defines the driver and datasource for accessing our db server.

Pay attention to the internal values your deployment (namespaces, services, db name/schema, etc...).

Add the 'custom_xml' section like in the example here:

```
  bastudio_configuration:

    custom_xml: |-
      <?xml version="1.0" encoding="UTF-8"?>
        <server> 
          <library id="PostgreSQLLibForMyDb">
            <fileset dir="/shared/resources/jdbc/postgresql" includes="* postgresql-42.3.3.jar  postgresql.jar"/>
          </library>

          <dataSource commitOrRollbackOnCleanup="commit" id="jdbc/mydb" isolationLevel="TRANSACTION_READ_COMMITTED" jndiName="jdbc/mydb" type="javax.sql.XADataSource">
            <jdbcDriver libraryRef="PostgreSQLLibForMyDb"/>
            <connectionManager maxPoolSize="50" minPoolSize="2"/>
            <properties.postgresql URL="jdbc:postgresql://my-postgresql.test-postgres.svc.cluster.local:5432/mydb?currentSchema=myschema" user="postgres" password="post01gres"/>
          </dataSource> 

          <transaction recoverOnStartup="true" waitForRecovery="false" heuristicRetryInterval="10" transactionLogDBTableSuffix="mydb" recoveryGroup="recover-group-bastudio" recoveryIdentity="_RIForMyDb">
            <dataSource transactional="false">
              <jdbcDriver libraryRef="PostgreSQLLibForMyDb"/>
              <connectionManager maxPoolSize="50" minPoolSize="2"/>
              <properties.postgresql URL="jdbc:postgresql://my-postgresql.test-postgres.svc.cluster.local:5432/mydb?currentSchema=myschema" user="postgres" password="post01gres"/>
            </dataSource>
          </transaction> 
        </server>
```

Save the CR 'icp4adeploy' and wait for the CP4BA operator to update the content of the config map 'icp4adeploy-bastudio-overrides-configmap'; when the value with key 'custom.xml' will appear

```
Data

custom.xml
<?xml version="1.0" encoding="UTF-8"?>
  <server> 
    <library id="PostgreS...
```

you can force restart the pod 'icp4adeploy-bastudio-deployment-0'.

Once the new pod has been restarted, check for the presence of the xml snippet in the folder

```
oc exec -n cp4ba icp4adeploy-bastudio-deployment-0 -- ls -al /opt/ibm/wlp/usr/servers/defaultServer/configDropins/overrides

oc exec -n cp4ba icp4adeploy-bastudio-deployment-0 -- cat /opt/ibm/wlp/usr/servers/defaultServer/configDropins/overrides/custom.xml
```

Now you may may deploy your apps using the jdbc datasource named "jdbc/mydb". 

You may also try to deploy the test application in 'apps' folder of this repo.
