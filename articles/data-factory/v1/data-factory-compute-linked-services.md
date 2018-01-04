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
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: b7686dc5c52737106a8bc819c160b67baaffd147
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambienti di calcolo supportati da Azure Data Factory
> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Compute linked services in V2](../compute-linked-services.md) (Servizi collegati di calcolo nella versione 2).

Questo articolo spiega i diversi ambienti di calcolo che è possibile utilizzare per elaborare o una trasformare dati. Fornisce inoltre informazioni dettagliate sulle diverse configurazioni (on-demand e bring your own) supportate da Data Factory durante la configurazione di servizi collegati che collegano questi ambienti a una data factory di Azure.

La seguente tabella presenta un elenco degli ambienti di calcolo supportati da Data Factory e le attività eseguibili in tali ambienti. 

| Ambiente di calcolo                      | attività                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight su richiesta](#azure-hdinsight-on-demand-linked-service) o [il proprio cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Attività di Machine Learning: esecuzione batch e aggiornamento risorse](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics.](#azure-data-lake-analytics-linked-service) | [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Stored procedure](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versioni supportate di HDInsight in Azure Data Factory
Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. Microsoft aggiorna continuamente l'elenco delle versioni supportate di HDInsight per offrire i componenti dell'ecosistema Hadoop e le correzioni più recenti. Per altre dettagliate, vedere le [versioni di HDInsight supportate](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight versione 3.3 per Linux è stato ritirato il 31 luglio 2017. I clienti dei servizi collegati HDInsight su richiesta di Data Factory v1 hanno tempo fino al 15 dicembre 2017 per testare ed eseguire l'aggiornamento a una versione di HDInsight più recente. Il 31 luglio 2018 verrà ritirato HDInsight per Windows.
>
> 

**Cosa succede dopo la data di ritiro** 

Dopo il 15 dicembre 2017:

- Non sarà più possibile creare cluster HDInsight versione 3.3 (o versioni precedenti) per Linux usando il servizio collegato HDInsight su richiesta di Azure Data Factory v1. 

- Se la [proprietà osType e/o Version](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) non viene specificata esplicitamente nelle definizioni JSON esistenti del servizio collegato HDInsight su richiesta di Azure Data Factory v1, il valore predefinito verrà modificato da **Version=3.1, osType=Windows** in **Version=3.6, osType=Linux**.

Dopo il 31 luglio 2018:

- Non sarà più possibile creare alcuna versione dei cluster HDInsight per Windows usando il servizio collegato HDInsight su richiesta di Azure Data Factory v1. 

 **Azioni consigliate** 

- Eseguire l'aggiornamento della [proprietà osType e/o Version](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) delle definizioni interessate del servizio collegato HDInsight su richiesta di Azure Data Factory v1 alle versioni di HDInsight per Linux (HDInsight 3.6) più recenti per poter usare le correzioni e i componenti più recenti dell'ecosistema Hadoop. 
- Prima del 15 dicembre 2017, testare le attività di streaming di Hive, Pig, MapReduce e Hadoop in Azure Data Factory V1 che fanno riferimento al servizio collegato interessato, per assicurarsi che siano compatibili con il nuovo valore predefinito di *osType* e/o *Version* (Version=3.6, osType=Linux) o di osType o version espliciti di HDInsight a cui si sta eseguendo l'aggiornamento. Per altre informazioni sulla compatibilità, vedere le pagine Web della documentazione [Migrate     from a Windows-based HDInsight cluster to a Linux-based cluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) (Migrare da un cluster HDInsight per Windows a un cluster HDInsight per Linux) e [What are the Hadoop components and versions available with     HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) (Quali sono i componenti e le versioni Hadoop disponibili con HDInsight?). 
- Impostare esplicitamente osType su Windows prima del 15 dicembre 2017, se si intende continuare a usare il servizio collegato HDInsight su richiesta di Azure Data Factory v1 per creare cluster HDInsight per Windows. È comunque consigliabile eseguire la migrazione ai cluster HDInsight basati su Linux prima del 31 luglio 2018. 
- Aggiornare la definizione JSON dell'attività personalizza DotNet per usare invece un servizio collegato di Azure Batch, se si usa il servizio collegato HDInsight su richiesta per eseguire l'attività personalizzata DotNet di Azure Data Factory v1. Per altre informazioni, vedere la pagina Web della documentazione [Usare attività personalizzate in una pipeline di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

>[!Note]
>Per i clienti che usano il servizio collegato HDInsight Bring Your Own Cluster (BYOC) in Azure Data Factory v1 o i clienti che usano il servizio collegato HDInsight su richiesta e BYOC in Azure Data Factory v2, i criteri di supporto della versione più recenti dei cluster Azure HDInsight sono già applicati, pertanto non è richiesta alcuna azione. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente di calcolo “on-demand”
In questo tipo di configurazione, l'ambiente informatico è completamente gestito dal servizio Azure Data Factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico.

> [!NOTE]
> La configurazione su richiesta è attualmente supportata solo per i cluster HDInsight di Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Servizio collegato Azure HDInsight su richiesta
Il servizio Data factory di Azure può creare automaticamente un cluster HDInsight su richiesta basato su Windows o Linux per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione (proprietà linkedServiceName in JSON) associato al cluster.

Tenere presente i seguenti punti **importanti** sul servizio collegato HDInsight su richiesta:

* Non viene visualizzato il cluster HDInsight su richiesta creato nella sottoscrizione di Azure. Il servizio Azure Data Factory gestisce il cluster HDInsight su richiesta per conto dell'utente.
* I registri per i processi eseguiti su un cluster HDInsight su richiesta vengono copiati nell'account di archiviazione associato al cluster HDInsight. È possibile accedere a questi log dal portale di Azure nel pannello **Dettagli di esecuzione di attività** . Per informazioni dettagliate, vedere l'articolo [Monitoraggio e gestione delle pipeline](data-factory-monitor-manage-pipelines.md) .
* Viene addebitato solo il tempo in cui il cluster HDInsight è attivo e i processi in esecuzione.

> [!IMPORTANT]
> Richiede in genere almeno **20 minuti** per il provisioning di un cluster HDInsight di Azure su richiesta.
>
> 

### <a name="example"></a>Esempio
Il codice JSON seguente definisce un servizio collegato HDInsight su richiesta basato su Linux. Il servizio Data factory crea automaticamente un cluster HDInsight **basato su Linux** durante l'elaborazione di una sezione di dati. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Per usare un cluster HDInsight basato su Windows, impostare **osType** su **windows** oppure evitare di usare la proprietà dato che il valore predefinito è windows.  

> [!IMPORTANT]
> Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione. 
>
> Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) .
>
> 

### <a name="properties"></a>Properties
| Proprietà                     | DESCRIZIONE                              | Obbligatoria |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | La proprietà type deve essere impostata su **HDInsightOnDemand**. | Sì      |
| clusterSize                  | Numero di nodi del ruolo di lavoro/nodi dati nel cluster. Il cluster HDInsight viene creato con 2 nodi head e il numero di nodi del ruolo di lavoro specificato per questa proprietà. I nodi sono di dimensione Standard_D3, con 4 core, quindi un cluster di 4 nodi del ruolo di lavoro ha 24 core, ossia 4\*4 = 16 core per i nodi del ruolo di lavoro + 2\*4 = 8 core per i nodi head. Vedere [Creare cluster Hadoop basati su Linux in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) per i dettagli sul livello Standard_D3. | Sì      |
| timeToLive                   | Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight su richiesta rimane attivo dopo il completamento di un'attività eseguita se non sono presenti altri processi attivi del cluster.<br/><br/>Ad esempio, se un'esecuzione di attività accetta 6 minuti e timetolive è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'attività. Se un'altra attività viene eseguita entro i 6 minuti consentiti, verrà elaborata dallo stesso cluster.<br/><br/>Poiché la creazione di un cluster HDInsight su richiesta è un'operazione che usa un numero elevato di risorse e potrebbe richiedere alcuni minuti, usare questa impostazione a seconda delle necessità per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.<br/><br/>Se si imposta il valore della proprietà timetolive su 0, il cluster viene eliminato non appena l'esecuzione dell'attività viene completata. Se si imposta un valore elevato, tuttavia, il cluster può rimanere inattivo inutilmente causando costi elevati. È quindi importante impostare il valore appropriato in base alle esigenze.<br/><br/>Se il valore della proprietà timetolive è impostato in modo appropriato, più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta. | Sì      |
| version                      | Versione del cluster HDInsight Il valore predefinito è 3.1 per cluster Windows e 3.2 per cluster Linux. | No        |
| linkedServiceName            | Servizio collegato Archiviazione di Azure che il cluster su richiesta deve usare per l'archiviazione e l'elaborazione dei dati. Il cluster HDInsight viene creato nella stessa area dell'account di Archiviazione di Azure.<p>Non è attualmente possibile creare un cluster HDInsight su richiesta che usa Azure Data Lake Store come risorsa di archiviazione. Per archiviare i dati dei risultati dell'elaborazione di HDInsight in un'istanza di Azure Data Lake Store, usare un'attività di copia per copiare i dati dall'archivio BLOB di Azure in Azure Data Lake Store. </p> | Sì      |
| additionalLinkedServiceNames | Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight in modo che il servizio Data Factory possa registrarli per conto dell'utente. Questi account di archiviazione devono essere nella stessa area del cluster HDInsight, che viene creato nella stessa area dell'account di archiviazione specificato da linkedServiceName. | No        |
| osType                       | Tipo di sistema operativo. I valori consentiti sono: Windows (impostazione predefinita) e Linux | No        |
| hcatalogLinkedServiceName    | Il nome del servizio collegato di Azure SQL che fa riferimento al database HCatalog. Viene creato il cluster HDInsight su richiesta usando il database SQL di Azure come metastore. | No        |

#### <a name="additionallinkedservicenames-json-example"></a>Esempio di codice JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Advanced Properties
È inoltre possibile specificare le seguenti proprietà per la configurazione granulare del cluster HDInsight su richiesta.

| Proprietà               | DESCRIZIONE                              | Obbligatoria |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Specifica i parametri di configurazione di base (ad esempio core-site.xml) per il cluster HDInsight da creare. | No        |
| hBaseConfiguration     | Specifica i parametri di configurazione HBase (hbase-site.xml) per il cluster HDInsight. | No        |
| hdfsConfiguration      | Specifica i parametri di configurazione HDFS (hdfs-site.xml) per il cluster HDInsight. | No        |
| hiveConfiguration      | Specifica i parametri di configurazione hive (hive-site.xml) per il cluster HDInsight. | No        |
| mapReduceConfiguration | Specifica i parametri di configurazione MapReduce (mapred-site.xml) per il cluster HDInsight. | No        |
| oozieConfiguration     | Specifica i parametri di configurazione Oozie (oozie-site.xml) per il cluster HDInsight. | No        |
| stormConfiguration     | Specifica i parametri di configurazione Storm (storm-site.xml) per il cluster HDInsight. | No        |
| yarnConfiguration      | Specifica i parametri di configurazione Yarn (yarn-site.xml) per il cluster HDInsight. | No        |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Esempio: configurazione del cluster HDInsight su richiesta con le proprietà avanzate

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
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
È possibile specificare le dimensioni dei nodi head, di dati e zookeeper usando le proprietà seguenti: 

| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Specifica le dimensioni del nodo head Il valore predefinito è Standard_D3. Vedere la sezione **Specificare le dimensioni dei nodi** per informazioni dettagliate. | No        |
| dataNodeSize      | Specifica le dimensioni del nodo dei dati. Il valore predefinito è Standard_D3. | No        |
| zookeeperNodeSize | Specifica le dimensioni del nodo Zookeeper. Il valore predefinito è Standard_D3. | No        |

#### <a name="specifying-node-sizes"></a>Specificare le dimensioni dei nodi
Vedere l'articolo relativo alle [dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md) per i valori della stringa che è necessario specificare per le proprietà indicate nella sezione precedente. I valori devono essere conformi a **CMDLET e API** a cui si fa riferimento nell'articolo. Come si vede nell'articolo, il nodo dei dati di grandi dimensioni (per impostazione predefinita) ha 7 GB di memoria, che potrebbe non essere adatto al proprio scenario. 

Per creare nodi head e nodi del ruolo di lavoro di dimensioni D4, è necessario specificare **Standard_D4** come valore per le proprietà headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se si specifica un valore errato per queste proprietà, potrebbe essere visualizzato il seguente **errore:** impossibile creare il cluster. Eccezione: impossibile completare l'operazione di creazione del cluster. L'operazione non è riuscita con codice '400'. Nello stato del cluster è apparso il messaggio 'Errore'. Messaggio: ’PreClusterCreationValidationFailure’. Quando si riceve questo errore, assicurarsi di usare il nome di **CMDLET e API** della tabella dell'articolo relativo alle [dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Azure Data Factory non supporta attualmente i cluster HDInsight con Azure Data Lake Store come archivio primario. È necessario usare Archiviazione di Azure come archivio primario per i cluster HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Ambiente di calcolo “bring your own”
In questo tipo di configurazione, gli utenti possono registrare un ambiente informatico già esistente come servizio collegato in Data Factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data Factory viene utilizzato per eseguire le attività.

Questo tipo di configurazione è supportato per gli ambienti di calcolo seguenti:

* HDInsight di Azure
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics.
* Azure SQL DB, Azure SQL DW e SQL Server

## <a name="azure-hdinsight-linked-service"></a>Servizio collegato Azure HDInsight
È possibile creare un servizio collegato Azure HDInsight per registrare il proprio cluster HDInsight con Data Factory.

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
| type              | La proprietà type deve essere impostata su **HDInsight**. | Sì      |
| clusterUri        | L'URI del cluster HDInsight.        | Sì      |
| username          | Specifica il nome dell'utente da utilizzare per connettersi a un cluster HDInsight esistente. | Sì      |
| password          | Specifica la password per l'account utente.   | Sì      |
| linkedServiceName | Nome del servizio collegato all'archiviazione di Azure che fa riferimento all'archiviazione BLOB di Azure usata dal cluster HDInsight. <p>Attualmente non è possibile specificare un servizio collegato di Azure Data Lake Store per questa proprietà. Se il cluster HDInsight dispone di accesso a Data Lake Store, è possibile accedere ai dati in Azure Data Lake Store da script Hive/Pig. </p> | Sì      |

## <a name="azure-batch-linked-service"></a>Servizio collegato Azure Batch
È possibile creare un servizio collegato di Azure Batch per registrare un pool di Batch di macchine virtuali (VM) a una data factory. È possibile eseguire le attività .NET personalizzate utilizzando Batch Azure o Azure HDInsight.

Vedere i seguenti argomenti se non si ha familiarità con il servizio di Azure Batch:

* [Nozioni di base di Azure Batch](../../batch/batch-technical-overview.md) per una panoramica del servizio Azure Batch.
* Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) per creare un account Azure Batch oppure [portale di Azure](../../batch/batch-account-create-portal.md) per creare l'account Azure Batch usando il portale di Azure. Per istruzioni dettagliate sull'utilizzo del cmdlet, consultare [Utilizzo di Azure PowerShell per gestire l'account di Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) per creare un pool di Batch di Azure.

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

Aggiungere "**.\<nome area\>**" al nome dell'account Batch per la proprietà **accountName**. Esempio:

```json
"accountName": "mybatchaccount.eastus"
```

Un'altra opzione consiste nell'indicazione dell'endpoint batchUri, come illustrato nell'esempio seguente:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Properties
| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| ----------------- | ---------------------------------------- | -------- |
| type              | La proprietà type deve essere impostata su **AzureBatch**. | Sì      |
| accountName       | Nome dell'account Azure Batch.         | Sì      |
| accessKey         | Chiave di accesso per l'account Azure Batch.  | Sì      |
| poolName          | Nome del pool di macchine virtuali.    | Sì      |
| linkedServiceName | Nome dello spazio di archiviazione del servizio collegato Azure associato al servizio collegato Azure Batch. Questo servizio collegato viene utilizzato per organizzare i file necessari per eseguire l'attività e archiviare i log di esecuzione dell’attività. | Sì      |

## <a name="azure-machine-learning-linked-service"></a>Servizio collegato di Azure Machine Learning
Si crea un servizio collegato di Azure Machine Learning per registrare un endpoint di punteggio batch Machine Learning a una data factory.

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
| type       | La proprietà type deve essere impostata su **AzureML**. | Sì      |
| mlEndpoint | L’URL del batch punteggio.                   | Sì      |
| apiKey     | Modello dell'area di lavoro pubblicato di API.     | Sì      |

## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data Factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

La tabella seguente fornisce le descrizioni delle proprietà generiche usate nella definizione JSON. È possibile scegliere anche tra l'autenticazione basata sull'entità servizio e l'autenticazione basata sulle credenziali utente.

| Proprietà                 | DESCRIZIONE                              | Obbligatoria                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. | Sì                                      |
| **accountName**          | Nome dell'account di Azure Data Lake Analytics.  | Sì                                      |
| **dataLakeAnalyticsUri** | URI di Azure Data Lake Analytics.           | No                                        |
| **subscriptionId**       | ID sottoscrizione di Azure                    | No (se non specificata, viene usata la sottoscrizione della Data factory). |
| **resourceGroupName**    | Nome del gruppo di risorse di Azure                | No (se non specificata, viene usato il gruppo di risorse di Data Factory). |

### <a name="service-principal-authentication-recommended"></a>Autenticazione basata su entità servizio (opzione consigliata)
Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere a tale entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:
* ID applicazione
* Chiave applicazione 
* ID tenant

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà                | DESCRIZIONE                              | Obbligatoria |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Specificare l'ID client dell'applicazione.     | Sì      |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione.           | Sì      |
| **tenant**              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì      |

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

### <a name="user-credential-authentication"></a>Autenticazione basata su credenziali utente
In alternativa, è possibile usare l'autenticazione delle credenziali dell'utente per Data Lake Analytics specificando le proprietà seguenti:

| Proprietà          | DESCRIZIONE                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| **authorization** | Fare clic sul pulsante **Autorizza** nell'editor di Data Factory e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì      |
| **sessionId**     | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì      |

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
Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato un messaggio di errore simile al seguente: Errore dell'operazione relativa alle credenziali: invalid_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z.

| Tipo di utente                                | Scade dopo                            |
| :--------------------------------------- | :--------------------------------------- |
| Account utente NON gestiti da Azure Active Directory (@hotmail.com, @live.com e così via). | 12 ore                                 |
| Account utente gestiti da Azure Active Directory (AAD) | 14 giorni dopo l'esecuzione dell'ultima sezione. <br/><br/>90 giorni, se viene eseguita una sezione basata sul servizio collegato OAuth almeno una volta ogni 14 giorni. |

Per evitare/risolvere questo problema, alla **scadenza del token** ripetere l'autorizzazione usando il pulsante **Autorizza** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice seguente:

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

Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Aggiungere un riferimento a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe WindowsFormsWebAuthenticationDialog. 

## <a name="azure-sql-linked-service"></a>Servizio collegato di Azure SQL
Si crea un servizio collegato di Azure SQL e lo si utilizza con l’ [Attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una procedura stored da una pipeline Data Factory. Vedere l’articolo [Connettore di Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="azure-sql-data-warehouse-linked-service"></a>Servizio collegato di Azure SQL Data Warehouse
Si crea un servizio collegato di Azure SQL Data Warehouse e lo si usa con l' [attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline Data Factory. Vedere l'articolo [Proprietà del servizio collegato di Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="sql-server-linked-service"></a>Servizio collegato di SQL Server
Si crea un servizio collegato di SQL Server e lo si usa con l' [attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline Data Factory. Vedere l'articolo [Proprietà del servizio collegato SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

