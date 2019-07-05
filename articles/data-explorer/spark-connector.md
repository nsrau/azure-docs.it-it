---
title: Usare il connettore di Esplora dati di Azure per Apache Spark per spostare dati tra i cluster Spark e Azure Data Explorer.
description: In questo argomento illustra come spostare dati tra i cluster Apache Spark ed Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441344"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Connettore di Esplora dati di Azure per Apache Spark (anteprima)

[Apache Spark](https://spark.apache.org/) è un motore di analitica unificata per l'elaborazione dati su larga scala. Esplora i dati di Azure è un servizio di analitica dei dati rapida e completamente gestito per l'analisi in tempo reale su grandi volumi di dati. 

Connettore Azure di Esplora dati per Spark implementa origine dati e sink di dati per lo spostamento dei dati tra i cluster Spark e Azure Data Explorer usare entrambe le funzionalità. Con Esplora dati di Azure e Apache Spark è possibile compilare applicazioni scalabili e veloci destinate a scenari basati sui dati, ad esempio Machine Learning, Extract-Transform-Load (ETL) e Log Analytics. La scrittura in Esplora dati di Azure può essere eseguita in batch e modalità di streaming.
La lettura da Esplora dati di Azure supporta l'eliminazione di colonne e la distribuzione del predicato, riducendo così il volume dei dati trasferiti, filtrando i dati in Esplora dati di Azure.

Connettore Spark di Esplora dati per Azure è un' [progetto open source](https://github.com/Azure/azure-kusto-spark) che possono essere eseguiti in qualsiasi cluster Spark.

> [!NOTE]
> Sebbene alcuni degli esempi seguenti si riferiscano a un [Azure Databricks](https://docs.azuredatabricks.net/) cluster Spark, connettore Spark per Azure Data Explorer non accetta dipendenze dirette in Databricks o qualsiasi altra distribuzione di Spark.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un database e un cluster di Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal) 
* Creare un cluster Spark
* Installare libreria connettore Esplora dati di Azure e le librerie elencate nelle [dipendenze](https://github.com/Azure/azure-kusto-spark#dependencies) , incluse le seguenti [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) librerie:
    * [Client di Data Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client di inserimento di Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Pre-compilato librerie per [2.4 di Spark, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Come creare il connettore Spark

Connettore Spark può essere creato da [origini](https://github.com/Azure/azure-kusto-spark) come descritto di seguito.

> [!NOTE]
> Questo passaggio è facoltativo. Se si utilizzano librerie predefinite [la configurazione del cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Prerequisiti di compilazione

* Java 1.8 SDK installato
* [Maven 3.x](https://maven.apache.org/download.cgi) installato
* Apache Spark versione 2.4.0 o versione successiva

> [!TIP]
> versioni 2.3.x sono inoltre supportate, ma possono richiedere alcune modifiche nelle dipendenze POM. Xml.

Per applicazioni Scala/Java usando definizioni di progetto Maven, collegare la tua applicazione con il seguente elemento (ultima versione può variare):

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

Per compilare file jar, eseguire tutti i test e installare i file jar al repository Maven locale:

```
mvn clean install
```

Per altre informazioni, vedere [utilizzo di connettore](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configurazione del cluster Spark

> [!NOTE]
> È consigliabile usare la versione più recente di connettore Spark di Esplora dati di Azure quando si esegue la procedura seguente:

1. Impostare le impostazioni del cluster in base a cluster di Azure Databricks tramite 2.4 di Spark e Scala 2.11 Spark seguenti: 

    ![Impostazioni del cluster Databricks](media/spark-connector/databricks-cluster.png)

1. Importare la libreria di connettore di Esplora dati di Azure:

    ![Libreria di importazione Esplora dati di Azure](media/spark-connector/db-create-library.png)

1. Aggiungere dipendenze aggiuntive:

    ![Aggiungere le dipendenze](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Viene trovata la versione di java corretta per ogni versione di Spark [qui](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Verificare che tutte le richieste di librerie vengono installate:

    ![Verificare siano installate le librerie](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Connettore Spark di Esplora dati di Azure consente di eseguire l'autenticazione con Azure Active Directory (Azure AD) usando un [applicazione di Azure AD](#azure-ad-application-authentication), [token di accesso di Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [l'autenticazione del dispositivo ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (per gli scenari non di produzione), oppure [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). L'utente deve installare il pacchetto azure-keyvault e fornire le credenziali dell'applicazione per accedere alla risorsa Key Vault.

### <a name="azure-ad-application-authentication"></a>Autenticazione dell'applicazione di Azure AD

La maggior parte dei metodo di autenticazione semplici e comuni. Questo metodo è consigliato per l'utilizzo di connettore Spark di Esplora dati di Azure.

|Properties  |Descrizione  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificatore di applicazione (client) di Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorità di autenticazione di Azure AD. ID di Azure Active Directory (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chiave dell'applicazione AD Azure per il client.     |

### <a name="azure-data-explorer-privileges"></a>Privilegi di Esplora dati di Azure

In un Cluster di Esplora dati di Azure, è necessario concedere i privilegi seguenti:

* Per la lettura (origine dati), è necessario disporre dell'applicazione di Azure AD *viewer* privilegi sul database di destinazione, oppure *admin* privilegi per la tabella di destinazione.
* Per la scrittura (dati sink), è necessario disporre dell'applicazione di Azure AD *ingestor* privilegi nel database di destinazione. È necessario che abbia *utente* privilegi nel database di destinazione per creare nuove tabelle. Se la tabella di destinazione esiste già, *admin* privilegi per la tabella di destinazione possono essere configurati.
 
Per altre informazioni sui ruoli dell'entità di Esplora dati di Azure, vedere [autorizzazione basata sui ruoli](/azure/kusto/management/access-control/role-based-authorization). Per la gestione dei ruoli di sicurezza, vedere [la gestione dei ruoli di sicurezza](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Sink di Spark: La scrittura in Esplora dati di Azure

1. Configurare i parametri del sink:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Scrive i DataFrame di Spark in cluster Esplora dati di Azure come batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Scrittura dati di streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origine Spark: La lettura da Esplora dati di Azure

1. Durante la lettura di piccole quantità di dati, definire la query sui dati:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Quando si leggono quantità elevate di dati, è necessario specificare nell'archivio blob temporanei. Fornire una chiave di firma di accesso condiviso contenitore di archiviazione, o nome account di archiviazione, chiave dell'account e nome del contenitore. Questo passaggio è solo necessario per la versione di anteprima corrente del connettore Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Nell'esempio precedente, non è possibile accedere il Key Vault usando l'interfaccia connector. In alternativa, utilizziamo un metodo più semplice di usare i segreti di Databricks.

1. Lettura da Esplora dati di Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
