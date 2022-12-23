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
