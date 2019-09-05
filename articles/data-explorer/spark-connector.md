---
title: Usare Azure Esplora dati Connector per Apache Spark per spostare i dati tra i cluster Azure Esplora dati e Spark.
description: In questo argomento viene illustrato come spostare i dati tra i cluster Esplora dati e Apache Spark di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383059"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Connettore Azure Esplora dati per Apache Spark (anteprima)

[Apache Spark](https://spark.apache.org/) è un motore di analisi unificato per l'elaborazione di dati su larga scala. Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale su grandi volumi di dati. 

Azure Esplora dati Connector per Spark implementa l'origine dati e il sink di dati per lo trasferimento dei dati tra i cluster Azure Esplora dati e Spark per usare entrambe le funzionalità. Con Esplora dati di Azure e Apache Spark è possibile compilare applicazioni scalabili e veloci destinate a scenari basati sui dati, ad esempio Machine Learning, Extract-Transform-Load (ETL) e Log Analytics. La scrittura in Esplora dati di Azure può essere eseguita in modalità batch e flusso.
La lettura da Azure Esplora dati supporta l'eliminazione di colonne e il predicato distribuzione, che riduce il volume dei dati trasferiti filtrando i dati in Azure Esplora dati.

Il connettore Azure Esplora dati Spark è un [progetto open source](https://github.com/Azure/azure-kusto-spark) che può essere eseguito in qualsiasi cluster Spark. Il connettore Azure Esplora dati Spark rende Azure Esplora dati un archivio dati valido per le operazioni di origine e sink di Spark standard, ad esempio Write, Read e writeStream. 

> [!NOTE]
> Sebbene alcuni degli esempi seguenti facciano riferimento a un cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, il connettore Azure Esplora dati Spark non accetta dipendenze dirette da databricks o da qualsiasi altra distribuzione di Spark.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un database e un cluster di Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal) 
* Creare un cluster Spark
* Installare la libreria di Azure Esplora dati Connector e le librerie elencate in [dipendenze](https://github.com/Azure/azure-kusto-spark#dependencies) , incluse le librerie [Java SDK kusto](/azure/kusto/api/java/kusto-java-client-library) seguenti:
    * [Client di dati kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client di inserimento kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Librerie predefinite per [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Come compilare il connettore Spark

Il connettore Spark può essere compilato da [origini](https://github.com/Azure/azure-kusto-spark) come descritto di seguito.

> [!NOTE]
> Questo passaggio è facoltativo. Se si utilizzano librerie predefinite [la configurazione del cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Prerequisiti di compilazione

* Java 1,8 SDK installato
* [Maven 3. x](https://maven.apache.org/download.cgi) installato
* Apache Spark versione 2.4.0 o successiva

> [!TIP]
> sono supportate anche le versioni 2.3. x, ma potrebbero essere necessarie alcune modifiche nelle dipendenze POM. XML.

Per le applicazioni scala/Java che usano le definizioni di progetto Maven, collegare l'applicazione con l'artefatto seguente (la versione più recente può essere diversa):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> Si consiglia di usare la versione più recente di Azure Esplora dati Spark Connector quando si eseguono i passaggi seguenti:

1. Impostare le impostazioni del cluster Spark seguenti, basate su Azure Databricks cluster con Spark 2,4 e scala 2,11: 

    ![Impostazioni del cluster databricks](media/spark-connector/databricks-cluster.png)

1. Importare la libreria di Azure Esplora dati Connector:

    ![Importa la libreria Esplora dati di Azure](media/spark-connector/db-create-library.png)

1. Aggiungere altre dipendenze (non necessarie se usate da Maven):

    ![Aggiungere le dipendenze](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > La versione di rilascio Java corretta per ogni versione Spark è disponibile [qui](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Verificare che tutte le librerie richieste siano installate:

    ![Verificare le librerie installate](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Il connettore Azure Esplora dati Spark consente di eseguire l'autenticazione con Azure Active Directory (Azure AD) usando un' [applicazione Azure ad](#azure-ad-application-authentication), [Azure ad token di accesso](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [l'autenticazione del dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (per gli scenari non di produzione) o la chiave di [Azure ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)Insieme di credenziali. L'utente deve installare il pacchetto dell'insieme di credenziali delle risorse di Azure e fornire le credenziali dell'applicazione per accedere alla risorsa Key Vault.

### <a name="azure-ad-application-authentication"></a>Autenticazione dell'applicazione Azure AD

Metodo di autenticazione più semplice e comune. Questo metodo è consigliato per l'utilizzo del connettore Spark per Azure Esplora dati.

|Properties  |Descrizione  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificatore Azure AD applicazione (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorità di autenticazione Azure AD. ID Azure AD directory (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chiave dell'applicazione Azure AD per il client.     |

### <a name="azure-data-explorer-privileges"></a>Privilegi di Azure Esplora dati

Per un cluster di Azure Esplora dati è necessario concedere i privilegi seguenti:

* Per la lettura (origine dati), Azure AD applicazione deve disporre dei privilegi di *Visualizzatore* per il database di destinazione o dei privilegi di *amministratore* per la tabella di destinazione.
* Per la scrittura (sink di dati), Azure AD applicazione *deve disporre dei* privilegi di inserimento nel database di destinazione. Deve inoltre disporre di privilegi *utente* sul database di destinazione per la creazione di nuove tabelle. Se la tabella di destinazione esiste già, è possibile configurare i privilegi di *amministratore* per la tabella di destinazione.
 
Per altre informazioni sui ruoli principali di Azure Esplora dati, vedere [autorizzazione basata sui ruoli](/azure/kusto/management/access-control/role-based-authorization). Per la gestione dei ruoli di sicurezza, vedere [gestione dei ruoli di sicurezza](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Sink Spark: Scrittura in Esplora dati di Azure

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
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origine Spark: Lettura da Azure Esplora dati

1. Quando si leggono piccole quantità di dati, definire la query di dati:

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

1. Durante la lettura di grandi quantità di dati, è necessario fornire l'archiviazione BLOB temporanei. Specificare la chiave SAS del contenitore di archiviazione o il nome dell'account di archiviazione, la chiave dell'account e il nome del contenitore. Questo passaggio è necessario solo per la versione di anteprima corrente del connettore Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Nell'esempio precedente, non è possibile accedere al Key Vault usando l'interfaccia del connettore. In alternativa, viene usato un metodo più semplice per usare i segreti di databricks.

1. Leggi da Esplora dati di Azure:

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
