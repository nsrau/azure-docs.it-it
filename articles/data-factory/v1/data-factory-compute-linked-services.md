---
title: Ambienti di calcolo supportati da Azure Data Factory | Microsoft Docs
description: "Informazioni sugli ambienti di calcolo che è possibile usare nelle pipeline di Azure Data Factory (ad esempio, Azure HDInsight) per trasformare o elaborare i dati."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 410fb74d8f8ec6196bbd4cc19cc97704649b75c9
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambienti di calcolo supportati da Azure Data Factory
> [!NOTE]
> Questo articolo si applica alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Servizi collegati di calcolo nella versione 2](../compute-linked-services.md).

Questo articolo illustra gli ambienti di calcolo che è possibile usare per elaborare o trasformare i dati. Fornisce anche informazioni dettagliate sulle diverse configurazioni (on demand e Bring Your Own) supportate da Data Factory quando si configurano servizi collegati che collegano questi ambienti di calcolo a una data factory di Azure.

La tabella seguente presenta un elenco degli ambienti di calcolo supportati da Data Factory e le attività eseguibili in tali ambienti. 

| Ambiente di calcolo                      | attività                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight di Azure on demand](#azure-hdinsight-on-demand-linked-service) o [il proprio cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Attività di Machine Learning: esecuzione batch e aggiornamento risorse](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics.](#azure-data-lake-analytics-linked-service) | [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Attività stored procedure](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versioni di HDInsight supportate in Data Factory
Azure HDInsight supporta più versioni del cluster Hadoop che è possibile distribuire in qualsiasi momento. Ogni versione supportata crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti nella distribuzione. 

Microsoft aggiorna l'elenco delle versioni di HDInsight supportate con i componenti dell'ecosistema Hadoop e le correzioni più recenti. Per informazioni dettagliate, vedere [Versioni supportate di HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight versione 3.3 per Linux è stato ritirato il 31 luglio 2017. I clienti dei servizi collegati HDInsight on demand di Data Factory versione 1 hanno avuto tempo fino al 15 dicembre 2017 per testare ed eseguire l'aggiornamento a una versione di HDInsight più recente. Il 31 luglio 2018 verrà ritirato HDInsight per Windows.
>
> 

### <a name="after-the-retirement-date"></a>Dopo la data di ritiro 

Dopo il 15 dicembre 2017:

- Non è più possibile creare cluster HDInsight versione 3.3 (o versioni precedenti) per Linux usando un servizio collegato HDInsight on demand di Data Factory versione 1. 
- Se le [proprietà **osType** e **Version**](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) non vengono specificate in modo esplicito nella definizione JSON di un servizio collegato HDInsight on demand di Data Factory versione 1 esistente, il valore predefinito passa da **Version=3.1, osType=Windows** a **Version=\<versione predefinita HDI più recente\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType=Linux**.

Dopo il 31 luglio 2018:

- Non è più possibile creare versioni di cluster HDInsight per Windows usando un servizio collegato HDInsight on demand di Data Factory versione 1. 

### <a name="recommended-actions"></a>Azioni consigliate 

- Per essere certi di poter usare le correzioni e i componenti più recenti dell'ecosistema Hadoop, aggiornare le [proprietà **osType** e **Version**](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) delle definizioni del servizio collegato HDInsight on demand di Data Factory versione 1 interessato alle versioni di HDInsight per Linux (HDInsight 3.6) più recenti. 
- Prima del 15 dicembre 2017, testare le attività di streaming di Hive, Pig, MapReduce e Hadoop di Data Factory versione 1 che fanno riferimento al servizio collegato interessato. Assicurarsi che siano compatibili con i nuovi valori predefiniti di **osType** e **Version** (**Version=3.6**, **osType=Linux**) o con la versione di HDInsight esplicita e il tipo di sistema operativo a cui si sta eseguendo l'aggiornamento. 
  Per altre informazioni sulla compatibilità, vedere [Eseguire la migrazione da un cluster HDInsight per Windows a un cluster HDInsight per Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [Componenti e versioni di Hadoop disponibili con HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Per continuare a usare il servizio collegato HDInsight on demand di Data Factory versione 1 per creare cluster HDInsight per Windows, impostare esplicitamente **osType** su **Windows** prima del 15 dicembre 2017. È consigliabile eseguire la migrazione ai cluster HDInsight basati su Linux prima del 31 luglio 2018. 
- Se si usa un servizio collegato HDInsight on demand per eseguire l'attività personalizzata DotNet di Data Factory versione 1, aggiornare la definizione JSON dell'attività personalizza DotNet per usare invece un servizio collegato di Azure Batch. Per altre informazioni, vedere l'articolo [Usare attività personalizzate in una pipeline di Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Se si usa il dispositivo collegato al cluster HDInsight Bring Your Own esistente in Data Factory versione 1 o un servizio collegato HDInsight Bring Your Own e on demand in Azure Data Factory versione 2, non sono necessarie azioni. In tali scenari i criteri di supporto dell'ultima versione dei cluster HDInsight sono già applicati. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente di calcolo “on-demand”
In una configurazione on demand Data Factory gestisce completamente l'ambiente di calcolo. Data Factory crea automaticamente l'ambiente di calcolo prima che un processo venga inviato per l'elaborazione dei dati. Al termine del processo, Data Factory rimuove l'ambiente di calcolo. 

È possibile creare un servizio collegato per un ambiente di calcolo on demand. Usare il servizio collegato per configurare l'ambiente di calcolo e controllare le impostazioni granulari per l'esecuzione del processo, la gestione del cluster e le azioni di avvio automatico.

> [!NOTE]
> La configurazione on demand è attualmente supportata solo per i cluster HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Servizio collegato Azure HDInsight su richiesta
Data Factory può creare automaticamente un cluster HDInsight on demand basato su Windows o su Linux per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione associato al cluster. Usare la proprietà JSON **linkedServiceName** per creare il cluster.

Tenere presente i seguenti punti *chiave* sul servizio collegato HDInsight on demand:

* Il cluster HDInsight on demand non viene visualizzato nella sottoscrizione di Azure. Il servizio Data Factory gestisce il cluster HDInsight on demand per conto dell'utente.
* I registri per i processi eseguiti su un cluster HDInsight on demand vengono copiati nell'account di archiviazione associato al cluster HDInsight. Per accedere a questi log, nel portale di Azure andare al riquadro **Dettagli di esecuzione di attività**. Per altre informazioni, vedere [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md).
* Viene addebitato solo il tempo in cui il cluster HDInsight è attivo e i processi in esecuzione.

> [!IMPORTANT]
> Richiede in genere almeno *20 minuti* per il provisioning di un cluster HDInsight on demand.
>
> 

### <a name="example"></a>Esempio
Il codice JSON seguente definisce un servizio collegato HDInsight su richiesta basato su Linux. Data factory crea automaticamente un cluster HDInsight *basato su Linux* quando elabora una sezione di dati. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Il cluster HDInsight crea un *contenitore predefinito* nell'archivio BLOB di Azure specificato nella proprietà JSON **linkedServiceName**. Per impostazione predefinita, HDInsight non elimina il contenitore quando viene eliminato il cluster. In un servizio collegato HDInsight on demand viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**). Il cluster viene eliminato al termine dell'elaborazione. 
>
> Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB. Se i contenitori non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adf<your Data Factory name>-<linked service name>-<date and time>`. È possibile usare uno strumento come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) per eliminare i contenitori nell'archivio BLOB.
>
> 

### <a name="properties"></a>Properties
| Proprietà                     | DESCRIZIONE                              | Obbligatoria |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Impostare la proprietà type su **HDInsightOnDemand**. | Sì      |
| clusterSize                  | Numero di nodi del ruolo di lavoro e di dati nel cluster. Il cluster HDInsight viene creato con 2 nodi head, oltre al numero di nodi del ruolo di lavoro specificato per questa proprietà. I nodi sono di dimensione Standard_D3, con 4 core. Un cluster di 4 nodi del ruolo di lavoro ha 24 core, ossia 4\*4 = 16 core per i nodi del ruolo di lavoro + 2\*4 = 8 core per i nodi head. Per informazioni dettagliate sul livello Standard_D3, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sì      |
| timeToLive                   | Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight on demand rimane attivo quando un'esecuzione attività viene completata, se non sono presenti altri processi attivi del cluster.<br /><br />Ad esempio, se un'esecuzione attività richiede 6 minuti e **timeToLive** è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'esecuzione attività. Se un'altra attività viene eseguita nella finestra di 6 minuti, viene elaborata dallo stesso cluster.<br /><br />La creazione di un cluster HDInsight su richiesta è un'operazione dispendiosa. Potrebbe infatti potrebbe richiedere alcuni minuti. Usare questa impostazione a seconda delle necessità per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.<br /><br />Se si imposta il valore di **timeToLive** su **0**, il cluster viene eliminato non appena l'esecuzione attività termina. Se tuttavia si imposta un valore elevato, il cluster potrebbe rimanere inattivo inutilmente causando costi elevati. È importante impostare il valore appropriato in base alle esigenze.<br /><br />Se il valore di **timeToLive** è impostato in modo appropriato, più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta. | Sì      |
| version                      | Versione del cluster HDInsight. Per le versioni di HDInsight consentite, vedere [Versioni supportate di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Se questo valore non viene specificato, viene usata l'[ultima versione di HDI predefinita](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions). | No        |
| linkedServiceName            | Servizio collegato Archiviazione di Azure che il cluster on demand deve usare per l'archiviazione e l'elaborazione dei dati. Il cluster HDInsight viene creato nella stessa area dell'account di archiviazione.<p>Non è attualmente possibile creare un cluster HDInsight on demand che usa Azure Data Lake Store come risorsa di archiviazione. Per archiviare i dati dei risultati dell'elaborazione di HDInsight in Data Lake Store, usare l'attività di copia per copiare i dati dall'archivio BLOB a Data Lake Store. </p> | Sì      |
| additionalLinkedServiceNames | Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight. Data Factory registra gli account di archiviazione per conto dell'utente. Questi account di archiviazione devono trovarsi nella stessa area del cluster HDInsight. La creazione del cluster HDInsight avviene nella stessa area dell'account di archiviazione specificato dalla proprietà **linkedServiceName**. | No        |
| osType                       | Tipo di sistema operativo. I valori consentiti sono **Linux** e **Windows**. Se questo valore non viene specificato, viene usato **Linux**.  <br /><br />È consigliabile usare cluster HDInsight basati su Linux. La data di ritiro di HDInsight per Windows è il 31 luglio 2018. | No        |
| hcatalogLinkedServiceName    | Nome del servizio collegato SQL di Azure che fa riferimento al database HCatalog. Viene creato il cluster HDInsight on demand usando il database SQL come metastore. | No        |

#### <a name="example-linkedservicenames-json"></a>Esempio: codice JSON LinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Proprietà avanzate
Per la configurazione granulare del cluster HDInsight on demand, è anche possibile specificare le proprietà seguenti:

| Proprietà               | DESCRIZIONE                              | Obbligatoria |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Specifica i parametri di configurazione di base (core-site.xml) per il cluster HDInsight da creare. | No        |
| hBaseConfiguration     | Specifica i parametri di configurazione HBase (hbase-site.xml) per il cluster HDInsight. | No        |
| hdfsConfiguration      | Specifica i parametri di configurazione HDFS (hdfs-site.xml) per il cluster HDInsight. | No        |
| hiveConfiguration      | Specifica i parametri di configurazione Hive (hive-site.xml) per il cluster HDInsight. | No        |
| mapReduceConfiguration | Specifica i parametri di configurazione MapReduce (mapred-site.xml) per il cluster HDInsight. | No        |
| oozieConfiguration     | Specifica i parametri di configurazione Oozie (oozie-site.xml) per il cluster HDInsight. | No        |
| stormConfiguration     | Specifica i parametri di configurazione Storm (storm-site.xml) per il cluster HDInsight. | No        |
| yarnConfiguration      | Specifica i parametri di configurazione YARN (yarn-site.xml) per il cluster HDInsight. | No        |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Esempio: configurazione del cluster HDInsight on demand con le proprietà avanzate

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Dimensioni dei nodi
Per specificare le dimensioni dei nodi head, di dati e ZooKeeper, usare le proprietà seguenti: 

| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Imposta le dimensioni del nodo head. Il valore predefinito è **Standard_D3**. Per informazioni dettagliate, vedere [Specificare le dimensioni dei nodi](#specify-node-sizes). | No        |
| dataNodeSize      | Imposta le dimensioni del nodo di dati. Il valore predefinito è **Standard_D3**. | No        |
| zookeeperNodeSize | Imposta le dimensioni del nodo ZooKeeper. Il valore predefinito è **Standard_D3**. | No        |

#### <a name="specify-node-sizes"></a>Specificare le dimensioni dei nodi
Per i valori delle stringhe che è necessario specificare per le proprietà descritte nella sezione precedente, vedere [Dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md). I valori devono essere conforme ai cmdlet e alle API a cui si fa riferimento in [Dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md). Le dimensioni dei nodi di dati grandi (impostazione predefinita) offrono 7 GB di memoria, che potrebbero non essere sufficienti per lo scenario. 

Per creare nodi head e nodi del ruolo di lavoro di dimensioni D4, specificare **Standard_D4** come valore per le proprietà **headNodeSize** e **dataNodeSize**: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se si imposta un valore non corretto per queste proprietà, è possibile che venga visualizzato il messaggio seguente:

  Non è stato possibile creare il cluster. Eccezione: impossibile completare l'operazione di creazione del cluster. L'operazione non è riuscita con codice '400'. Nello stato del cluster è apparso il messaggio 'Errore'. Messaggio: ’PreClusterCreationValidationFailure’. 
  
Se questo messaggio viene visualizzato, assicurarsi di usare i nomi di cmdlet e API indicati nella tabella in [Dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Data Factory non supporta attualmente i cluster HDInsight che usano Data Lake Store come archivio primario. Usare Archiviazione di Azure come archivio primario per i cluster HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Ambiente di calcolo Bring Your Own
È possibile registrare un ambiente di calcolo esistente come servizio collegato in Data Factory. L'ambiente di calcolo viene gestito dall'utente. Il servizio Data Factory usa l'ambiente di calcolo per eseguire le attività.

Questo tipo di configurazione è supportato per gli ambienti di calcolo seguenti:

* HDInsight di Azure
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics.
* Database SQL di Azure, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Servizio collegato Azure HDInsight
È possibile creare un servizio collegato HDInsight per registrare il proprio cluster HDInsight con Data Factory.

### <a name="example"></a>Esempio

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Properties
| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| ----------------- | ---------------------------------------- | -------- |
| type              | Impostare la proprietà type su **HDInsight**. | Sì      |
| clusterUri        | L'URI del cluster HDInsight.        | Sì      |
| username          | Nome dell'account utente da usare per connettersi a un cluster HDInsight esistente. | Sì      |
| password          | La password per l'account utente.   | Sì      |
| linkedServiceName | Nome del servizio collegato di archiviazione che fa riferimento all'archivio BLOB usato dal cluster HDInsight. <p>Attualmente non è possibile specificare un servizio collegato Data Lake Store per questa proprietà. Se il cluster HDInsight ha accesso a Data Lake Store, è possibile accedere ai dati in Data Lake Store da script Hive o Pig. </p> | Sì      |

## <a name="azure-batch-linked-service"></a>Servizio collegato Azure Batch
È possibile creare un servizio collegato Batch per registrare un pool di Batch di macchine virtuali (VM) in una data factory. È possibile eseguire le attività Microsoft .NET personalizzate usando Batch o HDInsight.

Se non si ha familiarità con l'uso del servizio Batch:

* Vedere [Informazioni su Azure Batch](../../batch/batch-technical-overview.md).
* Vedere le informazioni sul cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx). Usare questo cmdlet per creare un account Batch oppure creare l'account Batch usando il [portale di Azure](../../batch/batch-account-create-portal.md). Per informazioni dettagliate sull'uso del cmdlet, vedere [Using PowerShell to manage a Batch account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) (Uso di PowerShell per gestire un account Batch).
* Vedere le informazioni sul cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx). Usare questo cmdlet per creare un pool di Batch.

### <a name="example"></a>Esempio

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Per la proprietà **accountName**, aggiungere **.\<nome area\>** al nome dell'account Batch. Ad esempio: 

```json
"accountName": "mybatchaccount.eastus"
```

Un'altra opzione consiste nello specificare l'endpoint **batchUri**. Ad esempio: 

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Properties
| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| ----------------- | ---------------------------------------- | -------- |
| type              | Impostare la proprietà type su **AzureBatch**. | Sì      |
| accountName       | Nome dell'account Batch.         | Sì      |
| accessKey         | Chiave di accesso per l'account Batch.  | Sì      |
| poolName          | Nome del pool di VM.    | Sì      |
| linkedServiceName | Nome del servizio collegato di archiviazione associato a questo servizio collegato Batch. Questo servizio collegato viene usato per eseguire un'installazione di appoggio dei file necessari per eseguire l'attività e archiviare i log di esecuzione dell'attività. | Sì      |

## <a name="azure-machine-learning-linked-service"></a>Servizio collegato di Azure Machine Learning
È possibile creare un servizio collegato Machine Learning per registrare un endpoint di assegnazione dei punteggi batch di Machine Learning in una data factory.

### <a name="example"></a>Esempio

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Properties
| Proprietà   | DESCRIZIONE                              | Obbligatoria |
| ---------- | ---------------------------------------- | -------- |
| type       | Impostare la proprietà type su **AzureML**. | Sì      |
| mlEndpoint | L’URL del batch punteggio.                   | Sì      |
| apiKey     | Modello dell'area di lavoro pubblicato di API.     | Sì      |

## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
È possibile creare un servizio collegato Data Lake Analytics per collegare un servizio di calcolo di Data Lake Analytics a una data factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

La tabella seguente descrive le proprietà generiche usate nella definizione JSON:

| Proprietà                 | DESCRIZIONE                              | Obbligatoria                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Impostare la proprietà type su **AzureDataLakeAnalytics**. | Sì                                      |
| accountName          | Nome dell'account Data Lake Analytics.  | Sì                                      |
| dataLakeAnalyticsUri | URI di Data Lake Analytics.           | No                                        |
| subscriptionId       | ID sottoscrizione di Azure.                    | No <br /><br />Se non specificata, viene usata la sottoscrizione della data factory. |
| resourceGroupName    | Nome del gruppo di risorse di Azure.                | No <br /><br /> Se non specificata, viene usato il gruppo di risorse della data factory. |

### <a name="authentication-options"></a>Opzioni di autenticazione
Per il servizio collegato Data Lake Analytics, è possibile scegliere tra autenticazione tramite un'entità servizio o le credenziali utente.

#### <a name="service-principal-authentication-recommended"></a>Autenticazione basata su entità servizio (opzione consigliata)
Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD), quindi concedere ad Azure AD l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:
* ID applicazione
* Chiave applicazione 
* ID tenant

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà                | DESCRIZIONE                              | Obbligatoria |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID client dell'applicazione.     | Sì      |
| servicePrincipalKey | Chiave dell'applicazione.           | Sì      |
| tenant              | Informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Per ottenere queste informazioni, passare il puntatore del mouse sull'angolo superiore destro del portale di Azure. | Sì      |

**Esempio: autenticazione basata su entità servizio**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Autenticazione basata su credenziali utente
Per l'autenticazione delle credenziali utente per Data Lake Analytics, specificare le proprietà seguenti:

| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| autorizzazione | Nell'editor di Data Factory selezionare il pulsante **Autorizza**. Immettere le credenziali che assegnano l'URL dell'autorizzazione generato automaticamente a questa proprietà. | Sì      |
| sessionId     | ID sessione OAuth dalla sessione di autorizzazione OAuth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì      |

**Esempio: autenticazione basata su credenziali utente**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Scadenza del token
Il codice di autorizzazione generato selezionando il pulsante **Autorizza** scade dopo un intervallo impostato. 

È possibile che venga visualizzato il seguente messaggio di errore alla scadenza del token di autenticazione: 

  Errore dell'operazione relativa alle credenziali: invalid_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z.

La tabella seguente indica le scadenze in base al tipo di account utente: 

| Tipo di utente                                | Scade dopo                            |
| :--------------------------------------- | :--------------------------------------- |
| Account utente *non* gestiti da Azure AD (Hotmail, Live e così via) | 12 ore.                                 |
| Account utente *gestiti* da Azure AD | 14 giorni dopo l'esecuzione dell'ultima sezione. <br /><br />90 giorni, se viene eseguita una sezione basata su un servizio collegato OAuth almeno una volta ogni 14 giorni. |

Per evitare o correggere questo errore, alla scadenza del token ripetere l'autorizzazione selezionando il pulsante **Autorizza**, quindi ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice seguente:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Per informazioni dettagliate sulle classi di Data Factory usate in questo esempio di codice, vedere:
* [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) (Classe AuthorizationSessionGetResponse)

Aggiungere un riferimento a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe **WindowsFormsWebAuthenticationDialog**. 

## <a name="azure-sql-linked-service"></a>Servizio collegato SQL di Azure
È possibile creare un servizio collegato SQL e usarlo con l'[attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline di Data Factory. Per altre informazioni, vedere [Connettore SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Servizio collegato di Azure SQL Data Warehouse
È possibile creare un servizio collegato SQL Data Warehouse e usarlo con l'[attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline Data Factory. Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Servizio collegato di SQL Server
È possibile creare un servizio collegato SQL Server e usarlo con l'[attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline di Data Factory. Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

