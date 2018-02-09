---
title: Usare Apache Spark per leggere e scrivere dati nel database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare una connessione tra un cluster HDInsight Spark e un database SQL di Azure per leggere dati, scrivere dati e trasmettere dati in un database SQL
services: hdinsight
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: nitinme
ms.openlocfilehash: 28ed6b9774bb85c7ec806c7775c34f8bc3d66bde
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usare un cluster HDInsight Spark per leggere e scrivere dati nel database SQL di Azure

Informazioni su come connettere un cluster Apache Spark in HDInsight di Azure con un database SQL di Azure e quindi leggere, scrivere e trasmettere dati nel database SQL. Le istruzioni riportate in questo articolo usano un notebook Jupyter per eseguire i frammenti di codice Scala. È possibile tuttavia creare un'applicazione autonoma in Scala o Python ed eseguire le stesse attività. 

## <a name="prerequisites"></a>Prerequisiti

* **Cluster Azure HDInsight Spark**.  Seguire le istruzioni riportate in [Creare un cluster Apache Spark in HDInsight](apache-spark-jupyter-spark-sql.md).

* **Database SQL di Azure**. Seguire le istruzioni riportate in [Creare un database SQL di Azure](../../sql-database/sql-database-get-started-portal.md). Assicurarsi di creare un database con lo schema e i dati dell'esempio **AdventureWorksLT**. Assicurarsi inoltre di creare una regola del firewall a livello di server per consentire all'indirizzo IP del client di accedere al database SQL sul server. Le istruzioni per aggiungere la regola del firewall sono disponibili nello stesso articolo. Dopo aver creato il database SQL di Azure, prendere nota dei valori seguenti, che saranno necessari per connettersi al database da un cluster Spark.

    * Nome del server che ospita il database SQL di Azure
    * Nome del database SQL di Azure
    * Nome utente / password amministratore del database SQL di Azure

* **SQL Server Management Studio**. Seguire le istruzioni riportate in [Usare SQL Server Management Studio per connettersi ed eseguire query sui dati](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Creare un notebook Jupyter

Iniziare creando un notebook Jupyter associato al cluster Spark. Usare quindi il notebook per eseguire i frammenti di codice illustrati in questo articolo. 

1. Dal [Portale di Azure](https://portal.azure.com/), aprire il cluster. 

2. Nella sezione **Collegamenti rapidi** fare clic su **Dashboard cluster** per aprire la vista **Dashboard cluster**.  Se non viene visualizzato **Collegamenti rapidi**, fare clic su **Panoramica** dal menu a sinistra del pannello.

    ![Dashboard del cluster in Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "Dashboard del cluster in Spark") 

3. Fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

    ![Notebook di Jupyter in Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Notebook di Jupyter in Spark")
   
   > [!NOTE]
   > Per accedere al notebook di Jupyter nel cluster Spark è possibile anche aprire l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. Nel notebook di Jupyter, nell'angolo superiore destro, fare clic su **Nuovo** e quindi su **Spark** per creare un notebook di Scala. I notebook di Jupyter presenti nel cluster HDInsight Spark forniscono anche il kernel **PySpark** per le applicazioni Python2 e il kernel **PySpark3** per le applicazioni Python3. In questo articolo si creerà un notebook di Scala.
   
    ![Kernel per il notebook di Jupyter in Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernel per il notebook di Jupyter in Spark")

    Per altre informazioni sui kernel, vedere [Usare i kernel per Jupyter Notebook con cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > In questo articolo viene usato un kernel Spark (Scala) perché il flusso di dati da Spark al database SQL è attualmente supportato solo in Scala e Java. Anche se le operazioni di lettura e scrittura in SQL possono essere eseguite con Python, per coerenza a livello di articolo si userà Scala per tutte e tre le operazioni.
   >

5. Viene aperto ora un nuovo notebook con un nome predefinito, **Senza titolo**. Fare clic sul nome del notebook e immettere un nome a scelta.

    ![Specificare un nome per il notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Specificare un nome per il notebook")

È ora possibile iniziare a creare l'applicazione.
    
## <a name="read-data-from-azure-sql-database"></a>Leggere dati dal database SQL di Azure

In questa sezione si leggeranno i dati di una tabella (ad esempio, **SalesLT.Address**) presente nel database AdventureWorks.

1. In un nuovo notebook di Jupyter, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori relativi al database SQL di Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

2. Il frammento di codice seguente crea un URL JDBC che è possibile passare alle API del dataframe Spark e crea un oggetto `Properties` per contenere i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. Il frammento di codice seguente crea un dataframe con i dati ottenuti da una tabella presente nel database SQL di Azure. In questo frammento di codice si usa una tabella **SalesLT.Address** disponibile nell'ambito del database **AdventureWorksLT**. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. È ora possibile eseguire operazioni sul dataframe, ad esempio ottenere lo schema dei dati:

       sqlTableDF.printSchema
   
    L'output visualizzato sarà simile al seguente:

    ![Specificare un nome per il notebook](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Specificare un nome per il notebook")

5. È ora possibile eseguire operazioni come recuperare le prime 10 righe

       sqlTableDF.show(10)

6. o recuperare colonne specifiche dal set di dati.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Scrivere dati nel database SQL di Azure

In questa sezione si userà un file CSV di esempio disponibile nel cluster per creare una tabella nel database SQL di Azure e popolarla con i dati desiderati. Il file CSV di esempio (**HVAC.csv**) è disponibile in tutti i cluster HDInsight al percorso `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. In un nuovo notebook di Jupyter, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori relativi al database SQL di Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

2. Il frammento di codice seguente crea un URL JDBC che è possibile passare alle API del dataframe Spark e crea un oggetto `Properties` per contenere i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. Il frammento di codice seguente estrae lo schema dei dati in HVAC.csv e lo usa per caricare i dati dal file CSV in un dataframe, `readDf`. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Utilizzare il dataframe `readDf` per creare una tabella temporanea, `temphvactable`. Usare quindi la tabella temporanea per creare una tabella hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Usare infine la tabella hive per creare una tabella nel database SQL di Azure. Il frammento di codice seguente crea `hvactable` nel database SQL di Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Connettersi al database SQL di Azure tramite SQL Server Management Studio e verificare che venga visualizzato un oggetto `dbo.hvactable`.

    a. Avviare SQL Server Management Studio e connettersi al database SQL di Azure specificando i dettagli della connessione, come illustrato nella schermata seguente.

    ![Connessione al database SQL tramite SQL Server Management Studio](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Connessione al database SQL tramite SQL Server Management Studio")

    b. In Esplora oggetti espandere il database SQL di Azure e il nodo Tabella per visualizzare l'oggetto **dbo.hvactable** creato.

    ![Connessione al database SQL tramite SQL Server Management Studio](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Connessione al database SQL tramite SQL Server Management Studio")

## <a name="stream-data-into-azure-sql-database"></a>Trasmettere dati nel database SQL di Azure

In questa sezione si trasmetteranno dati all'oggetto **hvactable** creato nel database SQL di Azure nel corso della sezione precedente.

1. Come primo passaggio, assicurarsi che non siano presenti record nell'oggetto **hvactable**. Eseguire la query seguente nella tabella tramite SQL Server Management Studio.

       DELETE FROM [dbo].[hvactable]

2. Caricare un nuovo notebook di Jupyter nel cluster HDInsight Spark. Incollare il frammento di codice seguente in una cella di codice e quindi premere **MAIUSC+INVIO**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Si trasmetteranno i dati da **HVAC.csv** all'oggetto hvactable. Il file HVAC.csv è disponibile nel cluster al percorso */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. Nel frammento di codice seguente, prima si ottiene lo schema dei dati da trasmettere e quindi si usa lo schema per creare un dataframe di streaming. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. L'output mostra lo schema di **HVAC.csv**, che coincide con lo schema di **hvactable**. Nell'output vengono elencate le colonne presenti nella tabella.

    ![Schema della tabella](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Schema della tabella")

5. Usare infine il frammento di codice seguente per leggere i dati da HVAC.csv e trasmetterli nell'oggetto **hvactable** nel database SQL di Azure. Incollare il frammento di codice in una cella di codice, sostituire i valori segnaposto con i valori relativi al database SQL di Azure in uso e quindi premere **MAIUSC+INVIO** per eseguirlo.

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

6. Verificare che i dati vengano trasmessi nell'oggetto **hvactable** eseguendo la query seguente. Ogni volta che si esegue la query, il numero di righe nella tabella aumenta.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Passaggi successivi

* [Usare il cluster HDInsight Spark per analizzare i dati in Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Elaborare eventi di flusso strutturati con EventHub](apache-spark-eventhub-structured-streaming.md)
* [Usare lo streaming strutturato Spark con Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
