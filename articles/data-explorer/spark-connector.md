---
title: Usare il connettore di Azure Data Explorer per Apache Spark per spostare i dati tra azure Data Explorer e i cluster Spark.Use the Azure Data Explorer connector for Apache Spark to move data between Azure Data Explorer and Spark clusters.
description: Questo argomento illustra come spostare i dati tra i cluster Azure Data Explorer e Apache Spark.This topic shows you how to move data between Azure Data Explorer and Apache Spark clusters.
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
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer Connector for Apache Spark

[Apache Spark](https://spark.apache.org/) è un motore di analisi unificato per l'elaborazione di dati su larga scala. Azure Data Explorer è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale su grandi volumi di dati. 

Il connettore di Azure Data Explorer per Spark è un progetto open source che può essere eseguito in qualsiasi cluster Spark.The Azure Data Explorer connector for Spark is an [open source project](https://github.com/Azure/azure-kusto-spark) that can run on any Spark cluster. Implementa l'origine dati e il sink di dati per lo spostamento dei dati tra i cluster Azure Data Explorer e Spark.It implements data source and data sink for moving data across Azure Data Explorer and Spark clusters. Usando Azure Data Explorer e Apache Spark, è possibile creare applicazioni veloci e scalabili destinate a scenari basati sui dati. Ad esempio, Machine Learning (ML), Extract-Transform-Load (ETL) e Log Analytics. Con il connettore, Azure Data Explorer diventa un archivio dati valido per le operazioni di origine e sink Spark standard, ad esempio write, read e writeStream.With the connector, Azure Data Explorer becomes a valid data store for standard Spark source and sink operations, such as write, read, and writeStream.

È possibile scrivere in Azure Data Explorer in modalità batch o di flusso. La lettura da Azure Data Explorer supporta l'eliminazione delle colonne e il pushdown dei predicati, che filtra i dati in Azure Data Explorer, riducendo il volume dei dati trasferiti.

In questo argomento viene descritto come installare e configurare il connettore Spark di Azure Data Explorer e spostare i dati tra i cluster Azure Data Explorer e Apache Spark.This topic describes how to install and configure the Azure Data Explorer Spark connector and move data between Azure Data Explorer and Apache Spark clusters.

> [!NOTE]
> Anche se alcuni degli esempi seguenti fanno riferimento a un cluster Spark di [Azure Databricks,](https://docs.azuredatabricks.net/) il connettore Spark di Azure Data Explorer non accetta dipendenze dirette su Databrick o su qualsiasi altra distribuzione Spark.Although some of the examples below refer to an Azure Databricks Spark cluster, Azure Data Explorer Spark connector does not take direct dependencies on Databricks or any other Spark distribution.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un cluster e un database di Azure Data ExplorerCreate an Azure Data Explorer cluster and database](/azure/data-explorer/create-cluster-database-portal) 
* Creare un cluster Spark
* Installare la libreria dei connettori di Azure Data Explorer:Install Azure Data Explorer connector library:
    * Librerie predefinite per [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Repo Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) installato

> [!TIP]
> Anche le versioni 2.3.x sono supportate, ma potrebbero richiedere alcune modifiche nelle dipendenze pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Come creare il connettore Spark

> [!NOTE]
> Questo passaggio è facoltativo. Se si utilizzano librerie predefinite, passare a [Configurazione cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Prerequisiti di compilazioneBuild prerequisites

1. Installare le librerie elencate nelle [dipendenze,](https://github.com/Azure/azure-kusto-spark#dependencies) incluse le seguenti librerie Java SDK di [Kusto:](/azure/kusto/api/java/kusto-java-client-library)
    * [Client dati Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Cliente](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Fare riferimento a [questa origine](https://github.com/Azure/azure-kusto-spark) per la creazione di Spark Connector.

1. Per le applicazioni Scala/Java che utilizzano le definizioni di progetto Maven, collegare l'applicazione con l'elemento seguente (la versione più recente potrebbe essere diversa):
    
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

Per creare un jar, eseguire tutti i test e installare jar nel repository Maven locale:

```
mvn clean install
```

Per ulteriori informazioni, vedere [Utilizzo del connettore](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configurazione cluster Spark

> [!NOTE]
> È consigliabile usare la versione più recente del connettore di Azure Data Explorer Spark quando si esegue la procedura seguente.

1. Configurare le impostazioni del cluster Spark seguenti, in base al cluster Di Azure Databricks usando Spark 2.4.4 e Scala 2.11:Configure the following Spark cluster settings, based on Azure Databricks cluster using Spark 2.4.4 and Scala 2.11:

    ![Impostazioni cluster Databricks](media/spark-connector/databricks-cluster.png)
    
1. Installare la libreria spark-kusto-connector più recente da Maven:
    
    ![Importare](media/spark-connector/db-libraries-view.png) ![raccolte Selezionare Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Verificare che siano installate tutte le librerie necessarie:

    ![Verificare le librerie installate](media/spark-connector/db-libraries-view.png)

1. Per l'installazione utilizzando un file JAR, verificare che siano state installate dipendenze aggiuntive:

    ![Aggiungere le dipendenze](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentication

Il connettore Spark di Azure Data Explorer consente di eseguire l'autenticazione con Azure Active Directory (Azure AD) usando uno dei metodi seguenti:Azure Data Explorer Spark connector enables you to authenticate with Azure Active Directory (Azure AD) using one of the following methods:
* [Un'applicazione Azure ADAn Azure AD application](#azure-ad-application-authentication)
* Un token di [accesso di Azure ADAn Azure AD access token](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Autenticazione del dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (per scenari non di produzione)Device authentication (for non-production scenarios)
* Un [insieme di](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) credenziali delle chiavi di Azure per accedere alla risorsa dell'insieme di credenziali delle chiavi, installare il pacchetto azure-keyvault e fornire le credenziali dell'applicazione.

### <a name="azure-ad-application-authentication"></a>Autenticazione delle applicazioni Azure ADAzure AD application authentication

L'autenticazione dell'applicazione Azure AD è il metodo di autenticazione più semplice e comune ed è consigliata per il connettore Spark di Azure Data Explorer.Azure AD application authentication is the most simplest and most common authentication method and is recommended for the Azure Data Explorer Spark connector.

|Proprietà  |Descrizione  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificatore dell'applicazione Azure AD (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorità di autenticazione di Azure AD. ID directory (tenant) di Azure AD.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chiave dell'applicazione Azure AD per il client.     |

### <a name="azure-data-explorer-privileges"></a>Privilegi di Azure Data ExplorerAzure Data Explorer privileges

Concedere i privilegi seguenti in un cluster di Azure Data Explorer:Grant the following privileges on an Azure Data Explorer cluster:

* Per la lettura (origine dati), l'identità di Azure AD deve disporre dei privilegi di *visualizzatore* per il database di destinazione o dei privilegi di *amministratore* per la tabella di destinazione.
* Per la scrittura (sink di dati), l'identità di Azure AD deve disporre dei privilegi *di* gestore nel database di destinazione. Deve inoltre disporre dei privilegi *utente* sul database di destinazione per creare nuove tabelle. Se la tabella di destinazione esiste già, è necessario configurare i privilegi di *amministratore* per la tabella di destinazione.
 
Per altre informazioni sui ruoli principali di Azure Data Explorer, vedere [Autorizzazione basata sui ruoli.](/azure/kusto/management/access-control/role-based-authorization) Per la gestione dei ruoli di sicurezza, vedere [Gestione dei ruoli](/azure/kusto/management/security-roles)di sicurezza .

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Sink di Spark: scrittura in Azure Data ExplorerSpark sink: writing to Azure Data Explorer

1. Impostare i parametri del sink:

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

1. Scrivere Spark DataFrame nel cluster di Azure Data Explorer come batch:Write Spark DataFrame to Azure Data Explorer cluster as batch:

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
    
   In alternativa, utilizzare la sintassi semplificata:
   
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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origine Spark: lettura da Azure Data ExplorerSs source: reading from Azure Data Explorer

1. Durante la lettura [di piccole quantità di dati](/azure/kusto/concepts/querylimits), definire la query dei dati:

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

1. Facoltativo: se **si** specifica l'archiviazione BLOB temporanea (e non Azure Data Explorer), i BLOB vengono creati sotto la responsabilità del chiamante. Ciò include il provisioning dell'archiviazione, la rotazione delle chiavi di accesso e l'eliminazione di elementi temporanei. 
    Il modulo KustoBlobStorageUtils contiene funzioni di supporto per l'eliminazione di BLOB in base alle coordinate di account e contenitore e alle credenziali dell'account oppure un URL SAS completo con autorizzazioni di scrittura, lettura ed elenco. Quando il RDD corrispondente non è più necessario, ogni transazione archivia gli elementi BLOB temporanei in una directory separata. Questa directory viene acquisita come parte dei registri delle informazioni sulle transazioni di lettura segnalati nel nodo Driver spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Nell'esempio precedente, l'insieme di credenziali delle chiavi non è accessibile tramite l'interfaccia del connettore. viene utilizzato un metodo più semplice per utilizzare i segreti di Databricks.

1. Leggere da Azure Data Explorer.Read from Azure Data Explorer.

    * Se **si** fornisce l'archiviazione BLOB temporanea, leggere da Azure Data Explorer come segue:If you provide the transient blob storage, read from Azure Data Explorer as follows:

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

    * Se Azure Data Explorer fornisce l'archiviazione BLOB temporanea, leggere da Azure Data Explorer come segue:If **Azure Data Explorer** provides the transient blob storage, read from Azure Data Explorer as follows:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Data Explorer Spark ConnectorLearn](https://github.com/Azure/azure-kusto-spark/tree/master/docs) more about the Azure Data Explorer Spark Connector
* [Codice di esempio per Java e Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
