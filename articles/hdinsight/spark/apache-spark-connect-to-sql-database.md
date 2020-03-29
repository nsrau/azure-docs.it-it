---
title: Usare Apache Spark per leggere e scrivere dati nel database SQL di AzureUse Apache Spark to read and write data to Azure SQL Database
description: Informazioni su come configurare una connessione tra il cluster HDInsight Spark e un database SQL di Azure per leggere dati, scrivere dati e trasmettere dati in un database SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 4e0c1626582297aa7d80cbbd4241b6f81e314f8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927450"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usare il cluster HDInsight Spark per leggere e scrivere dati nel database SQL di AzureUse HDInsight Spark cluster to read and write data to Azure SQL Database

Informazioni su come connettere un cluster Apache Spark in Azure HDInsight con un database SQL di Azure e quindi leggere, scrivere e trasmettere dati nel database SQL. Le istruzioni contenute in questo articolo usano un blocco appunti di Jupyter per eseguire i frammenti di codice Scala.The instructions in this article use a [Jupyter Notebook](https://jupyter.org/) to run the Scala code snippets. È possibile tuttavia creare un'applicazione autonoma in Scala o Python ed eseguire le stesse attività.

## <a name="prerequisites"></a>Prerequisiti

* Cluster Azure HDInsight Spark.  Seguire le istruzioni riportate in [Creare un cluster Apache Spark in HDInsight](apache-spark-jupyter-spark-sql.md).

* Database SQL di Azure. Seguire le istruzioni in [Creare un database SQL](../../sql-database/sql-database-get-started-portal.md)di Azure . Assicurarsi di creare un database con lo schema e i dati dell'esempio **AdventureWorksLT**. Assicurarsi inoltre di creare una regola del firewall a livello di server per consentire all'indirizzo IP del client di accedere al database SQL sul server. Le istruzioni per aggiungere la regola del firewall sono disponibili nello stesso articolo. Dopo aver creato il database SQL di Azure, assicurarsi di tenere a portata di mano i valori seguenti. che saranno necessari per connettersi al database da un cluster Spark.

    * Server name hosting the Azure SQL Database.
    * Nome del database SQL di Azure.Azure SQL Database name.
    * Nome utente/password di amministratore del database SQL di Azure.Azure SQL Database admin user name/ password.

* SQL Server Management Studio (SSMS). Seguire le istruzioni riportate in [Usare SQL Server Management Studio per connettersi ed eseguire query sui dati](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Creare un oggetto Jupyter Notebook

Iniziare creando un [blocco appunti Jupyter](https://jupyter.org/) associato al cluster Spark. Usare quindi il notebook per eseguire i frammenti di codice illustrati in questo articolo.

1. Dal [Portale di Azure](https://portal.azure.com/), aprire il cluster.
1. Selezionare **Jupyter Notebook** in **Dashboard del cluster** sul lato destro.  Se i dashboard del cluster non sono **visualizzati,** selezionare **Panoramica** dal menu a sinistra. Se richiesto, immettere le credenziali per il cluster.

    ![Taccuino Jupyter su Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Taccuino Jupyter su Spark")

   > [!NOTE]  
   > Per accedere al notebook di Jupyter nel cluster Spark è possibile anche aprire l'URL seguente nel browser. **Sostituire CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Nel notebook di Jupyter, nell'angolo superiore destro, fare clic su **Nuovo** e quindi su **Spark** per creare un notebook di Scala. I notebook di Jupyter presenti nel cluster HDInsight Spark forniscono anche il kernel **PySpark** per le applicazioni Python2 e il kernel **PySpark3** per le applicazioni Python3. In questo articolo si creerà un notebook di Scala.

    ![Kernel per notebook Jupyter su Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernel per notebook Jupyter su Spark")

    Per altre informazioni sui kernel, vedere [Usare i kernel per Jupyter Notebook con cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > In questo articolo viene usato un kernel Spark (Scala) perché il flusso di dati da Spark al database SQL è attualmente supportato solo in Scala e Java. Anche se le operazioni di lettura e scrittura in SQL possono essere eseguite con Python, per coerenza a livello di articolo si userà Scala per tutte e tre le operazioni.

1. Viene aperto ora un nuovo notebook con un nome predefinito, **Senza titolo**. Fare clic sul nome del notebook e immettere un nome a scelta.

    ![Specificare un nome per il notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Specificare un nome per il notebook")

È ora possibile iniziare a creare l'applicazione.

## <a name="read-data-from-azure-sql-database"></a>Leggere dati dal database SQL di AzureRead data from Azure SQL Database

In questa sezione si leggeranno i dati di una tabella (ad esempio, **SalesLT.Address**) presente nel database AdventureWorks.

1. In a new Jupyter notebook, in a code cell, paste the following snippet and replace the placeholder values with the values for your Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. Usare il frammento di codice seguente per compilare un URL JDBC che è possibile passare alle API del frame di dati Spark.Use the snippet below to build a JDBC URL that you can pass to the Spark dataframe APIs. Il codice `Properties` crea un oggetto per contenere i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Usare il frammento di codice seguente per creare un frame di dati con i dati di una tabella nel database SQL di Azure.Use the snippet below to create a data frame with the data from a table in your Azure SQL Database. In questo frammento `SalesLT.Address` viene usata una tabella disponibile come parte del database **AdventureWorksLT.** Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. È ora possibile eseguire operazioni sul dataframe, ad esempio ottenere lo schema dei dati:

       sqlTableDF.printSchema

    L'output visualizzato sarà simile al seguente:

    ![output dello schema](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "output dello schema")

1. È ora possibile eseguire operazioni come recuperare le prime 10 righe

       sqlTableDF.show(10)

1. o recuperare colonne specifiche dal set di dati.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Scrivere dati nel database SQL di AzureWrite data into Azure SQL Database

In questa sezione viene usato un file CSV di esempio disponibile nel cluster per creare una tabella nel database SQL di Azure e popolarla con i dati. Il file CSV di esempio (**HVAC.csv**) è disponibile in tutti i cluster HDInsight al percorso `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. In a new Jupyter notebook, in a code cell, paste the following snippet and replace the placeholder values with the values for your Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. The following snippet builds a JDBC URL that you can pass to the Spark dataframe APIs. Il codice `Properties` crea un oggetto per contenere i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Usare il frammento di codice seguente per estrarre lo schema dei dati in HVAC.csv e usarlo per caricare i dati dal file CSV in un dataframe, `readDf`. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Utilizzare il dataframe `readDf` per creare una tabella temporanea, `temphvactable`. Usare quindi la tabella temporanea per creare una tabella hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Infine, usare la tabella hive per creare una tabella nel database SQL di Azure.Finally, use the hive table to create a table in Azure SQL Database. Il frammento `hvactable` di codice seguente viene creato nel database SQL di Azure.The following snippet creates in Azure SQL Database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Connettersi al database SQL di Azure usando `dbo.hvactable` SSMS e verificare che venga visualizzato un'area di accesso.

    a. Avviare SSMS e connettersi al database SQL di Azure fornendo i dettagli di connessione, come illustrato nella schermata seguente.

    ![Connettersi al database SQL tramite SSMS1Connect to SQL database using SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Connettersi al database SQL tramite SSMS1Connect to SQL database using SSMS1")

    b. In **Esplora oggetti**espandere il database SQL di Azure e il nodo Tabella per visualizzare il file **dbo.hvactable** creato.

    ![Connettersi al database SQL tramite SSMS2Connect to SQL database using SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Connettersi al database SQL tramite SSMS2Connect to SQL database using SSMS2")

1. Eseguire una query in SSMS per visualizzare le colonne nella tabella.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Trasmettere i dati nel database SQL di AzureStream data into Azure SQL Database

In questa sezione vengono trasmessi i `hvactable` dati nel database SQL di Azure già creato nella sezione precedente.

1. Come primo passaggio, assicurarsi che `hvactable`non siano presenti record nel file . Eseguire la query seguente nella tabella tramite SQL Server Management Studio.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Caricare un nuovo notebook di Jupyter nel cluster HDInsight Spark. Incollare il frammento di codice seguente in una cella di codice e quindi premere **MAIUSC+INVIO**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Inviamo i dati dal **file HVAC.csv** nel `hvactable`file . Il file HVAC.csv è `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`disponibile nel cluster all'indirizzo . Nel frammento di codice seguente, prima si ottiene lo schema dei dati da trasmettere e quindi si usa lo schema per creare un dataframe di streaming. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. L'output mostra lo schema di **HVAC.csv**, Il `hvactable` ha lo stesso schema pure. Nell'output vengono elencate le colonne presenti nella tabella.

    ![tabella dello schema hdinsight Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema della tabella")

1. Infine, usare il frammento di codice seguente per leggere i `hvactable` dati dal file HVAC.csv e trasmetterlo nel database SQL di Azure.Finally, use the following snippet to read data from the HVAC.csv and stream it into the in Azure SQL Database. Incollare il frammento di codice in una cella di codice, sostituire i valori segnaposto con i valori per il database SQL di Azure e quindi premere **MAIUSC e INVIO** per eseguire.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

1. Verificare che i dati vengono `hvactable` trasmessi in fase di streaming nella query seguente in SQL Server Management Studio (SSMS). Ogni volta che si esegue la query, il numero di righe nella tabella aumenta.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il cluster HDInsight Spark per analizzare i dati in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Elaborare eventi di flusso strutturati con EventHub](apache-spark-eventhub-structured-streaming.md)
* [usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
