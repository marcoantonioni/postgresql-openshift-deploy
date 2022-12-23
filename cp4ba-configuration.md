# BAStudio datasource and jdbc library configuration



  bastudio_configuration:
    custom_xml: |-
      <?xml version="1.0" encoding="UTF-8"?>
        <server> 
          <library id="PostgreSQLLibCustom">
            <fileset dir="/shared/resources/jdbc/postgresql" includes="* postgresql-42.3.3.jar  postgresql.jar"/>
          </library>

          <dataSource commitOrRollbackOnCleanup="commit" id="jdbc/aon" isolationLevel="TRANSACTION_READ_COMMITTED" jndiName="jdbc/aon" type="javax.sql.XADataSource">
            <jdbcDriver libraryRef="PostgreSQLLibCustom"/>
            <connectionManager maxPoolSize="50" minPoolSize="2"/>
            <properties.postgresql URL="jdbc:postgresql://my-postgresql-1.cp4ba.svc.cluster.local:5432/aon?currentSchema=aon" user="postgres" password="post01gres"/>
          </dataSource> 

          <transaction recoverOnStartup="true" waitForRecovery="false" heuristicRetryInterval="10" transactionLogDBTableSuffix="aon" recoveryGroup="recover-group-bastudio" recoveryIdentity="_RICustom">
            <dataSource transactional="false">
              <jdbcDriver libraryRef="PostgreSQLLibCustom"/>
              <connectionManager maxPoolSize="50" minPoolSize="2"/>
              <properties.postgresql URL="jdbc:postgresql://my-postgresql-1.cp4ba.svc.cluster.local:5432/aon?currentSchema=aon" user="postgres" password="post01gres"/>
            </dataSource>
          </transaction> 
        </server>
