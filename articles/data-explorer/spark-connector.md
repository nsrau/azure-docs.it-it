---
title: Usare Azure Esplora dati Connector per Apache Spark per spostare i dati tra i cluster Azure Esplora dati e Spark.
description: In questo argomento viene illustrato come spostare i dati tra i cluster Esplora dati e Apache Spark di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208601"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Connettore Azure Esplora dati per Apache Spark

[Apache Spark](https://spark.apache.org/) è un motore di analisi unificato per l'elaborazione di dati su larga scala. Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale su grandi volumi di dati. 

Azure Esplora dati Connector per Spark è un [progetto open source](https://github.com/Azure/azure-kusto-spark) che può essere eseguito in qualsiasi cluster Spark. Implementa l'origine dati e il sink di dati per lo stato di trasferimento dei dati tra i cluster Azure Esplora dati e Spark. Usando Esplora dati e Apache Spark di Azure, è possibile creare applicazioni veloci e scalabili destinate a scenari basati sui dati. Ad esempio Machine Learning (ML), Extract-Transform-Load (ETL) e Log Analytics. Con il connettore, Azure Esplora dati diventa un archivio dati valido per le operazioni di origine e sink standard di Spark, ad esempio Write, Read e writeStream.

È possibile scrivere in Esplora dati di Azure in modalità batch o di flusso. La lettura da Azure Esplora dati supporta l'eliminazione di colonne e il predicato distribuzione, che filtra i dati in Azure Esplora dati, riducendo il volume dei dati trasferiti.

Questo argomento descrive come installare e configurare il connettore Azure Esplora dati Spark e spostare i dati tra i cluster Azure Esplora dati e Apache Spark.

> [!NOTE]
> Sebbene alcuni degli esempi seguenti facciano riferimento a un cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, il connettore Azure Esplora dati Spark non accetta dipendenze dirette da databricks o da qualsiasi altra distribuzione di Spark.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un cluster e un database di Azure Esplora dati](/azure/data-explorer/create-cluster-database-portal) 
* Creare un cluster Spark
* Installare la libreria di Azure Esplora dati Connector:
    * Librerie predefinite per [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Repository maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) installato

> [!TIP]
> sono supportate anche le versioni 2.3. x, ma potrebbero essere necessarie alcune modifiche nelle dipendenze POM. XML.

## <a name="how-to-build-the-spark-connector"></a>Come compilare il connettore Spark

> [!NOTE]
> Questo passaggio è facoltativo. Se si usano le librerie predefinite, passare alla [configurazione del cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Prerequisiti di compilazione

1. Installare le librerie elencate in [dipendenze](https://github.com/Azure/azure-kusto-spark#dependencies) , incluse le librerie [Java SDK kusto](/azure/kusto/api/java/kusto-java-client-library) seguenti:
    * [Client di dati kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client di inserimento kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Vedere [questa origine](https://github.com/Azure/azure-kusto-spark) per la compilazione del connettore Spark.

1. Per le applicazioni scala/Java che usano le definizioni di progetto Maven, collegare l'applicazione con l'artefatto seguente (la versione più recente può essere diversa):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Comandi di compilazione

Per compilare file con estensione jar ed eseguire tutti i test:

```
mvn clean package
```

Per compilare jar, eseguire tutti i test e installare jar nel repository maven locale:

```
mvn clean install
```

Per altre informazioni, vedere [utilizzo del connettore](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configurazione del cluster Spark

> [!NOTE]
> È consigliabile usare la versione più recente di Azure Esplora dati Spark Connector quando si eseguono i passaggi seguenti.

1. Configurare le impostazioni del cluster Spark seguenti, basate su Azure Databricks cluster con Spark 2.4.4 e scala 2,11:

    ![Impostazioni del cluster databricks](media/spark-connector/databricks-cluster.png)
    
1. Installare la versione più recente della libreria Spark-kusto-Connector da Maven:
    
    ![Importare le](media/spark-connector/db-libraries-view.png) ![librerie selezionare Spark-kusto-Connector](media/spark-connector/db-dependencies.png)

1. Verificare che tutte le librerie richieste siano installate:

    ![Verificare le librerie installate](media/spark-connector/db-libraries-view.png)

1. Per l'installazione tramite un file con estensione JAR, verificare che siano state installate dipendenze aggiuntive:

    ![Aggiungere le dipendenze](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentication

Il connettore Azure Esplora dati Spark consente di eseguire l'autenticazione con Azure Active Directory (Azure AD) usando uno dei metodi seguenti:
* [Applicazione Azure ad](#azure-ad-application-authentication)
* Un [token di accesso Azure ad](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Autenticazione del dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (per gli scenari non di produzione)
* Un [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) per accedere alla risorsa key Vault, installare il pacchetto dell'insieme di credenziali delle app di Azure e specificare le credenziali dell'applicazione.

### <a name="azure-ad-application-authentication"></a>Autenticazione dell'applicazione Azure AD

Azure AD l'autenticazione dell'applicazione è il metodo di autenticazione più semplice e più comune ed è consigliato per il connettore Azure Esplora dati Spark.

|Proprietà  |Descrizione  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificatore Azure AD applicazione (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorità di autenticazione Azure AD. ID Azure AD directory (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chiave dell'applicazione Azure AD per il client.     |

### <a name="azure-data-explorer-privileges"></a>Privilegi di Azure Esplora dati

Concedere i privilegi seguenti in un cluster di Esplora dati di Azure:

* Per la lettura (origine dati), l'identità Azure AD deve disporre dei privilegi di *Visualizzatore* per il database di destinazione o dei privilegi di *amministratore* per la tabella di destinazione.
* Per la scrittura (sink di dati), l'identità del Azure AD *deve disporre dei* privilegi di inserimento nel database di destinazione. Deve inoltre disporre di privilegi *utente* sul database di destinazione per la creazione di nuove tabelle. Se la tabella di destinazione esiste già, è necessario configurare i privilegi di *amministratore* per la tabella di destinazione.
 
Per altre informazioni sui ruoli principali di Azure Esplora dati, vedere [autorizzazione basata sui ruoli](/azure/kusto/management/access-control/role-based-authorization). Per la gestione dei ruoli di sicurezza, vedere [gestione dei ruoli di sicurezza](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Sink Spark: scrittura in Azure Esplora dati

1. Configurare i parametri del sink:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Scrivere un dataframe Spark in Azure Esplora dati cluster come batch:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   In alternativa, usare la sintassi semplificata:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Scrivere i dati di streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origine Spark: lettura da Azure Esplora dati

1. Quando si leggono [piccole quantità di dati](/azure/kusto/concepts/querylimits), definire la query di dati:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Facoltativo: se **si** specifica l'archiviazione BLOB temporanea (e non Azure Esplora dati), i BLOB vengono creati sotto la responsabilità del chiamante. Questo include il provisioning dell'archiviazione, la rotazione delle chiavi di accesso e l'eliminazione di elementi temporanei. 
    Il modulo KustoBlobStorageUtils contiene le funzioni di supporto per l'eliminazione di BLOB in base alle coordinate di account e contenitore e alle credenziali dell'account oppure a un URL SAS completo con autorizzazioni di scrittura, lettura ed elenco. Quando il RDD corrispondente non è più necessario, ogni transazione archivia gli artefatti BLOB temporanei in una directory separata. Questa directory viene acquisita come parte dei log delle informazioni sulle transazioni di lettura segnalati nel nodo del driver Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Nell'esempio precedente, non è possibile accedere al Key Vault usando l'interfaccia del connettore; viene usato un metodo più semplice per usare i segreti di databricks.

1. Leggi da Esplora dati di Azure.

    * Se **si** fornisce l'archiviazione BLOB temporanea, leggere da Azure Esplora dati come indicato di seguito:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Se **azure Esplora dati** fornisce l'archiviazione BLOB temporanea, leggere da Azure Esplora dati come indicato di seguito:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sul [connettore Azure Esplora dati Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Codice di esempio per Java e Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
