---
title: Usare Apache Spark per leggere e scrivere dati nel database SQL di Azure
description: Informazioni su come configurare una connessione tra un cluster HDInsight Spark e un database SQL di Azure. Per leggere dati, scrivere dati e trasmettere dati in un database SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: c04280bf1cffea08204e1ea5ab54dbb87c23cf9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193208"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usare il cluster HDInsight Spark per leggere e scrivere dati nel database SQL di Azure

Informazioni su come connettere un cluster Apache Spark in Azure HDInsight con un database SQL di Azure. Quindi leggere, scrivere e trasmettere i dati nel database SQL. Le istruzioni in questo articolo usano un Jupyter Notebook per eseguire i frammenti di codice scala. Tuttavia, è possibile creare un'applicazione autonoma in scala o Python ed eseguire le stesse attività.

## <a name="prerequisites"></a>Prerequisiti

* Cluster Azure HDInsight Spark.  Seguire le istruzioni riportate in [Creare un cluster Apache Spark in HDInsight](apache-spark-jupyter-spark-sql.md).

* Database SQL di Azure. Seguire le istruzioni riportate in [creare un database SQL di Azure](../../sql-database/sql-database-get-started-portal.md). Assicurarsi di creare un database con lo schema e i dati dell'esempio **AdventureWorksLT**. Assicurarsi inoltre di creare una regola del firewall a livello di server per consentire all'indirizzo IP del client di accedere al database SQL sul server. Le istruzioni per aggiungere la regola del firewall sono disponibili nello stesso articolo. Dopo aver creato il database SQL di Azure, assicurarsi di avere a portata di mano i valori seguenti. che saranno necessari per connettersi al database da un cluster Spark.

    * Nome del server che ospita il database SQL di Azure.
    * Nome del database SQL di Azure.
    * Nome utente/password amministratore del database SQL di Azure.

* SQL Server Management Studio (SSMS). Seguire le istruzioni riportate in [Usare SQL Server Management Studio per connettersi ed eseguire query sui dati](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Creare un oggetto Jupyter Notebook

Iniziare creando una Jupyter Notebook associata al cluster Spark. Usare quindi il notebook per eseguire i frammenti di codice illustrati in questo articolo.

1. Dal [Portale di Azure](https://portal.azure.com/), aprire il cluster.
1. Selezionare **Jupyter Notebook** in **Dashboard del cluster** sul lato destro.  Se non vengono visualizzati i **Dashboard del cluster**, scegliere **Panoramica** dal menu a sinistra. Se richiesto, immettere le credenziali per il cluster.

    ![Notebook di Jupyter su Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Notebook di Jupyter in Spark")

   > [!NOTE]  
   > Per accedere al notebook di Jupyter nel cluster Spark è possibile anche aprire l'URL seguente nel browser. Sostituire **clustername** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Nel notebook di Jupyter, nell'angolo superiore destro, fare clic su **Nuovo** e quindi su **Spark** per creare un notebook di Scala. I notebook di Jupyter presenti nel cluster HDInsight Spark forniscono anche il kernel **PySpark** per le applicazioni Python2 e il kernel **PySpark3** per le applicazioni Python3. In questo articolo si creerà un notebook di Scala.

    ![Kernel per il notebook di Jupyter in Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernel per il notebook di Jupyter in Spark")

    Per altre informazioni sui kernel, vedere [Usare i kernel per Jupyter Notebook con cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > In questo articolo viene usato un kernel Spark (Scala) perché il flusso di dati da Spark al database SQL è attualmente supportato solo in Scala e Java. Anche se le operazioni di lettura e scrittura in SQL possono essere eseguite con Python, per coerenza a livello di articolo si userà Scala per tutte e tre le operazioni.

1. Verrà aperto un nuovo notebook con un nome predefinito, senza **titolo**. Fare clic sul nome del notebook e immettere un nome a scelta.

    ![Specificare un nome per il notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Specificare un nome per il notebook")

È ora possibile iniziare a creare l'applicazione.

## <a name="read-data-from-azure-sql-database"></a>Leggere dati dal database SQL di Azure

In questa sezione si leggeranno i dati di una tabella (ad esempio, **SalesLT.Address**) presente nel database AdventureWorks.

1. In un nuovo notebook di Jupyter, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori per il database SQL di Azure.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. Usare il frammento di codice riportato di seguito per creare un URL JDBC che è possibile passare alle API di Spark dataframe. Il codice crea un `Properties` oggetto per conservare i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Usare il frammento di codice seguente per creare un frame di dati con i dati di una tabella nel database SQL di Azure. In questo frammento viene utilizzata una `SalesLT.Address` tabella disponibile come parte del database **AdventureWorksLT** . Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. È ora possibile eseguire operazioni sul dataframe, ad esempio ottenere lo schema di dati:

    ```scala
    sqlTableDF.printSchema
    ```

    Viene visualizzato un output simile all'immagine seguente:

    ![output dello schema](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "output dello schema")

1. È anche possibile eseguire operazioni come, recuperare le prime 10 righe.

    ```scala
    sqlTableDF.show(10)
    ```

1. o recuperare colonne specifiche dal set di dati.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Scrivere dati nel database SQL di Azure

In questa sezione viene usato un file CSV di esempio disponibile nel cluster per creare una tabella nel database SQL di Azure e popolarla con i dati. Il file CSV di esempio (**HVAC.csv**) è disponibile in tutti i cluster HDInsight al percorso `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. In un nuovo notebook di Jupyter, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori per il database SQL di Azure.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. Il frammento di codice seguente compila un URL JDBC che è possibile passare alle API del frame di frame Spark. Il codice crea un `Properties` oggetto per conservare i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Usare il frammento di codice seguente per estrarre lo schema dei dati in HVAC.csv e usarlo per caricare i dati dal file CSV in un dataframe, `readDf`. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Utilizzare il dataframe `readDf` per creare una tabella temporanea, `temphvactable`. Usare quindi la tabella temporanea per creare una tabella hive, `hvactable_hive`.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Usare infine la tabella hive per creare una tabella nel database SQL di Azure. Il frammento di `hvactable` codice seguente crea un database SQL di Azure.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Connettersi al database SQL di Azure con SSMS e verificare che sia `dbo.hvactable` presente.

    a. Avviare SSMS e connettersi al database SQL di Azure fornendo i dettagli della connessione, come illustrato nella schermata seguente.

    ![Connettersi al database SQL tramite SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Connettersi al database SQL tramite SSMS1")

    b. Da **Esplora oggetti**, espandere il database SQL di Azure e il nodo della tabella per visualizzare il **dbo. oggetto hvactable** creato.

    ![Connettersi al database SQL tramite SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Connettersi al database SQL tramite SSMS2")

1. Eseguire una query in SSMS per visualizzare le colonne nella tabella.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Trasmettere i dati nel database SQL di Azure

In questa sezione vengono trasmessi i dati `hvactable` nel database SQL di Azure già creato nella sezione precedente.

1. Come primo passaggio, assicurarsi che non siano presenti record in `hvactable`. Eseguire la query seguente nella tabella tramite SQL Server Management Studio.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Caricare un nuovo notebook di Jupyter nel cluster HDInsight Spark. Incollare il frammento di codice seguente in una cella di codice e quindi premere **MAIUSC+INVIO**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. I dati vengono trasmessi da **HVAC. csv** a `hvactable`. Il file HVAC. csv è disponibile nel cluster all' `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`indirizzo. Nel frammento di codice seguente, prima si ottiene lo schema dei dati da trasmettere e quindi si usa lo schema per creare un dataframe di streaming. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. L'output mostra lo schema di **HVAC.csv**, `hvactable` Ha anche lo stesso schema. Nell'output vengono elencate le colonne presenti nella tabella.

    ![' HDInsight Apache Spark schema table '](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema della tabella")

1. Infine, usare il frammento di codice seguente per leggere i dati dal file HVAC. csv e `hvactable` trasmetterli nel database SQL di Azure. Incollare il frammento in una cella di codice, sostituire i valori segnaposto con i valori per il database SQL di Azure e quindi premere **MAIUSC + INVIO** per eseguire.

    ```scala
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
    ```

1. Verificare che i dati vengano trasmessi nel `hvactable` eseguendo la query seguente in SQL Server Management Studio (SSMS). Ogni volta che si esegue la query, il numero di righe nella tabella aumenta.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il cluster HDInsight Spark per analizzare i dati in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](apache-spark-load-data-run-query.md)
* [usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
