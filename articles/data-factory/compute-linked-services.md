---
title: Ambienti di calcolo supportati da Azure Data Factory | Microsoft Docs
description: "Informazioni sugli ambienti di calcolo che è possibile usare nelle pipeline di Azure Data Factory (ad esempio, Azure HDInsight) per trasformare o elaborare i dati."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: 07d702e34e1574161a64af9a4724a66879a0ba05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambienti di calcolo supportati da Azure Data Factory
Questo articolo spiega i diversi ambienti di calcolo che è possibile utilizzare per elaborare o una trasformare dati. Fornisce inoltre informazioni dettagliate sulle diverse configurazioni (on-demand e bring your own) supportate da Data Factory durante la configurazione di servizi collegati che collegano questi ambienti a una data factory di Azure.

La seguente tabella presenta un elenco degli ambienti di calcolo supportati da Data Factory e le attività eseguibili in tali ambienti. 

| Ambiente di calcolo                      | attività                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight su richiesta](#azure-hdinsight-on-demand-linked-service) o [il proprio cluster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Streaming di Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Impostazione personalizzata](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Attività di Machine Learning: esecuzione batch e aggiornamento risorse](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics.](#azure-data-lake-analytics-linked-service) | [Attività U-SQL di Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Stored procedure](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Ambiente di calcolo “on-demand”
In questo tipo di configurazione, l'ambiente informatico è completamente gestito dal servizio Azure Data Factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico.

> [!NOTE]
> La configurazione su richiesta è attualmente supportata solo per i cluster HDInsight di Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Servizio collegato Azure HDInsight su richiesta
Il servizio Azure Data Factory può creare automaticamente un cluster HDInsight su richiesta per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione (proprietà linkedServiceName in JSON) associato al cluster. L'account di archiviazione deve essere un account di archiviazione di Azure standard per utilizzo generico. 

Tenere presente i seguenti punti **importanti** sul servizio collegato HDInsight su richiesta:

* Il cluster HDInsight su richiesta verrà creato nella sottoscrizione di Azure. È possibile visualizzare il cluster nel portale di Azure quando questo è attivo e in esecuzione. 
* I registri per i processi eseguiti su un cluster HDInsight su richiesta vengono copiati nell'account di archiviazione associato al cluster HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definiti nella definizione del servizio collegato vengono usati per accedere al cluster per la risoluzione approfondita dei problemi durante il ciclo di vita del cluster. 
* Viene addebitato solo il tempo in cui il cluster HDInsight è attivo e i processi in esecuzione.

> [!IMPORTANT]
> Richiede in genere almeno **20 minuti** per il provisioning di un cluster HDInsight di Azure su richiesta.
>
> 

### <a name="example"></a>Esempio
Il codice JSON seguente definisce un servizio collegato HDInsight su richiesta basato su Linux. Il servizio Data factory crea automaticamente un cluster HDInsight **basato su Linux** per elaborare un'attività richiesta. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione. 
>
> Man mano che vengono eseguite le attività, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) .
>
> 

### <a name="properties"></a>Proprietà
| Proprietà                     | Descrizione                              | Obbligatorio |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | La proprietà type deve essere impostata su **HDInsightOnDemand**. | Sì      |
| clusterType                  | Tipo di cluster HDInsight da creare. I valori consentiti sono "hadoop" e "spark". Se non è specificato, il valore predefinito è hadoop. | No       |
| clusterSize                  | Numero di nodi del ruolo di lavoro/nodi dati nel cluster. Il cluster HDInsight viene creato con 2 nodi head e il numero di nodi del ruolo di lavoro specificato per questa proprietà. I nodi sono di dimensione Standard_D3, con 4 core, quindi un cluster di 4 nodi del ruolo di lavoro ha 24 core, ossia 4\*4 = 16 core per i nodi del ruolo di lavoro + 2\*4 = 8 core per i nodi head. Per altre informazioni vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sì      |
| timeToLive                   | Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight su richiesta rimane attivo dopo il completamento di un'attività eseguita se non sono presenti altri processi attivi del cluster. Il valore minimo consentito è 5 minuti (00:05:00).<br/><br/>Ad esempio, se un'esecuzione di attività accetta 6 minuti e timetolive è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'attività. Se un'altra attività viene eseguita entro i 6 minuti consentiti, verrà elaborata dallo stesso cluster.<br/><br/>Poiché la creazione di un cluster HDInsight su richiesta è un'operazione che usa un numero elevato di risorse e potrebbe richiedere alcuni minuti, usare questa impostazione a seconda delle necessità per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.<br/><br/>Se si imposta il valore della proprietà timetolive su 0, il cluster viene eliminato non appena l'esecuzione dell'attività viene completata. Invece, se si imposta un valore elevato, il cluster può rimanere inattivo per consentire all'utente di effettuare l'accesso al fine di risolvere i problemi; tuttavia questo può comportare costi elevati. È quindi importante impostare il valore appropriato in base alle esigenze.<br/><br/>Se il valore della proprietà timetolive è impostato in modo appropriato, più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta. | Sì      |
| version                      | Versione del cluster HDInsight Se non specificato, si usa la versione attuale predefinita da HDInsight. | No       |
| linkedServiceName            | Servizio collegato Archiviazione di Azure che il cluster su richiesta deve usare per l'archiviazione e l'elaborazione dei dati. Il cluster HDInsight viene creato nella stessa area dell'account di Archiviazione di Azure. Azure HDInsight applica un limite al numero totale di core che è possibile usare in ogni area di Azure supportata. Assicurarsi di avere sufficienti quote di core in tale area di Azure per soddisfare il clusterSize necessario. Per altre informazioni vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Non è attualmente possibile creare un cluster HDInsight su richiesta che usa Azure Data Lake Store come risorsa di archiviazione. Per archiviare i dati dei risultati dell'elaborazione di HDInsight in un'istanza di Azure Data Lake Store, usare un'attività di copia per copiare i dati dall'archivio BLOB di Azure in Azure Data Lake Store. </p> | Sì      |
| hostSubscriptionId           | L'ID della sottoscrizione di Azure usato per creare il cluster di HDInsight. Se non specificato, si usa l'ID della sottoscrizione del contesto di accesso di Azure. | No       |
| clusterResourceGroup         | In questo gruppo di risorse viene creato il cluster di HDInsight. | Sì      |
| sparkVersion                 | Versione di Spark se il tipo di cluster è "Spark" | No       |
| additionalLinkedServiceNames | Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight in modo che il servizio Data Factory possa registrarli per conto dell'utente. Questi account di archiviazione devono essere nella stessa area del cluster HDInsight, che viene creato nella stessa area dell'account di archiviazione specificato da linkedServiceName. | No       |
| osType                       | Tipo di sistema operativo. I valori consentiti sono: Linux e Windows, solo per HDInsight 3.3. Il valore predefinito è Linux. | No       |
| hcatalogLinkedServiceName    | Il nome del servizio collegato di Azure SQL che fa riferimento al database HCatalog. Viene creato il cluster HDInsight su richiesta usando il database SQL di Azure come metastore. | No       |
| connectVia                   | Runtime di integrazione da usare per inviare le attività a questo servizio collegato di HDInsight. Per il servizio collegato di HDInsight su richiesta, supporta solo il runtime di integrazione di Azure. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No       |

> [!IMPORTANT]
> HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. L'elenco delle versioni supportate di HDInsight viene continuamente aggiornato per offrire i componenti dell'ecosistema Hadoop e le correzioni più recenti. Verificare sempre di fare riferimento alle informazioni più recenti della [Versione supportata di HDInsight e del tipo di sistema operativo](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) per assicurarsi di usare la versione supportata di HDInsight. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>Esempio di codice JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    },
    "otherLinkedServiceName2": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    }
]
```

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Il servizio collegato di HDInsight su richiesta richiede un'autenticazione dell'entità servizio per creare i cluster HDInsight per conto dell'utente. Per usare l'autenticazione dell'entità servizio, registrare un'entità di applicazione in Azure Active Directory, ovvero Azure AD, e concedere a questa il ruolo di **Collaboratore** della sottoscrizione o il gruppo di risorse in cui viene creato il cluster HDInsight. Per la procedura dettagliata vedere [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione 
- ID tenant

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà                | Descrizione                              | Obbligatorio |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Specificare l'ID client dell'applicazione.     | Sì      |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione.           | Sì      |
| **tenant**              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì      |

### <a name="advanced-properties"></a>Advanced Properties

È inoltre possibile specificare le seguenti proprietà per la configurazione granulare del cluster HDInsight su richiesta.

| Proprietà               | Descrizione                              | Obbligatorio |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Specifica i parametri di configurazione di base (ad esempio core-site.xml) per il cluster HDInsight da creare. | No       |
| hBaseConfiguration     | Specifica i parametri di configurazione HBase (hbase-site.xml) per il cluster HDInsight. | No       |
| hdfsConfiguration      | Specifica i parametri di configurazione HDFS (hdfs-site.xml) per il cluster HDInsight. | No       |
| hiveConfiguration      | Specifica i parametri di configurazione hive (hive-site.xml) per il cluster HDInsight. | No       |
| mapReduceConfiguration | Specifica i parametri di configurazione MapReduce (mapred-site.xml) per il cluster HDInsight. | No       |
| oozieConfiguration     | Specifica i parametri di configurazione Oozie (oozie-site.xml) per il cluster HDInsight. | No       |
| stormConfiguration     | Specifica i parametri di configurazione Storm (storm-site.xml) per il cluster HDInsight. | No       |
| yarnConfiguration      | Specifica i parametri di configurazione Yarn (yarn-site.xml) per il cluster HDInsight. | No       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Esempio: configurazione del cluster HDInsight su richiesta con le proprietà avanzate

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
        "clusterSize": 16,
        "timeToLive": "01:30:00",
        "hostSubscriptionId": "<subscription ID>",
        "servicePrincipalId": "<service principal ID>",
        "servicePrincipalKey": {
          "value": "<service principal key>",
          "type": "SecureString"
        },
        "tenant": "<tenent id>",
        "clusterResourceGroup": "<resource group name>",
        "version": "3.6",
        "osType": "Linux",
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
          },
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
              "otherLinkedServiceName1": {
                  "referenceName": "datafeeds1",
                  "type": "LinkedServiceReference"
              },
              "otherLinkedServiceName2": {
                  "referenceName": "datafeeds2",
                  "type": "LinkedServiceReference"
              }
          ]}
  },
    "connectVia": {
    "referenceName": "<name of Integration Runtime>",
    "type": "IntegrationRuntimeReference"
  }
}
```

### <a name="node-sizes"></a>Dimensioni dei nodi
È possibile specificare le dimensioni dei nodi head, di dati e zookeeper usando le proprietà seguenti: 

| Proprietà          | Descrizione                              | Obbligatorio |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Specifica le dimensioni del nodo head Il valore predefinito è Standard_D3. Vedere la sezione **Specificare le dimensioni dei nodi** per informazioni dettagliate. | No       |
| dataNodeSize      | Specifica le dimensioni del nodo dei dati. Il valore predefinito è Standard_D3. | No       |
| zookeeperNodeSize | Specifica le dimensioni del nodo Zookeeper. Il valore predefinito è Standard_D3. | No       |

#### <a name="specifying-node-sizes"></a>Specificare le dimensioni dei nodi
Vedere l'articolo relativo alle [dimensioni delle macchine virtuali](../virtual-machines/linux/sizes.md) per i valori della stringa che è necessario specificare per le proprietà indicate nella sezione precedente. I valori devono essere conformi a **CMDLET e API** a cui si fa riferimento nell'articolo. Come si vede nell'articolo, il nodo dei dati di grandi dimensioni (per impostazione predefinita) ha 7 GB di memoria, che potrebbe non essere adatto al proprio scenario. 

Per creare nodi head e nodi del ruolo di lavoro di dimensioni D4, è necessario specificare **Standard_D4** come valore per le proprietà headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se si specifica un valore errato per queste proprietà, potrebbe essere visualizzato il seguente **errore:** impossibile creare il cluster. Eccezione: impossibile completare l'operazione di creazione del cluster. L'operazione non è riuscita con codice '400'. Nello stato del cluster è apparso il messaggio 'Errore'. Messaggio: ’PreClusterCreationValidationFailure’. Quando si riceve questo errore, assicurarsi di usare il nome di **CMDLET e API** della tabella dell'articolo relativo alle [dimensioni delle macchine virtuali](../virtual-machines/linux/sizes.md).        

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
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Proprietà
| Proprietà          | Descrizione                              | Obbligatorio |
| ----------------- | ---------------------------------------- | -------- |
| type              | La proprietà type deve essere impostata su **HDInsight**. | Sì      |
| clusterUri        | L'URI del cluster HDInsight.        | Sì      |
| username          | Specifica il nome dell'utente da utilizzare per connettersi a un cluster HDInsight esistente. | Sì      |
| password          | Specifica la password per l'account utente.   | Sì      |
| linkedServiceName | Nome del servizio collegato all'archiviazione di Azure che fa riferimento all'archiviazione BLOB di Azure usata dal cluster HDInsight. <p>Attualmente non è possibile specificare un servizio collegato di Azure Data Lake Store per questa proprietà. Se il cluster HDInsight dispone di accesso a Data Lake Store, è possibile accedere ai dati in Azure Data Lake Store da script Hive/Pig. </p> | Sì      |
| connectVia        | Il runtime di integrazione da usare per inviare le attività a questo servizio collegato. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No       |

> [!IMPORTANT]
> HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. L'elenco delle versioni supportate di HDInsight viene continuamente aggiornato per offrire i componenti dell'ecosistema Hadoop e le correzioni più recenti. Verificare sempre di fare riferimento alle informazioni più recenti della [Versione supportata di HDInsight e del tipo di sistema operativo](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) per assicurarsi di usare la versione supportata di HDInsight. 
>

## <a name="azure-batch-linked-service"></a>Servizio collegato Azure Batch

È possibile creare un servizio collegato di Azure Batch per registrare un pool di Batch di macchine virtuali (VM) a una data factory. È possibile eseguire l'attività personalizzata usando Azure Batch.

Vedere i seguenti argomenti se non si ha familiarità con il servizio di Azure Batch:

* [Nozioni di base di Azure Batch](../batch/batch-technical-overview.md) per una panoramica del servizio Azure Batch.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) per creare un account di Azure Batch oppure [Portale di Azure](../batch/batch-account-create-portal.md) per creare l'account di Azure Batch usando il portale di Azure. Per istruzioni dettagliate sull'utilizzo del cmdlet, consultare [Utilizzo di Azure PowerShell per gestire l'account di Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) per creare un pool di Batch di Azure.

### <a name="example"></a>Esempio

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Proprietà
| Proprietà          | Descrizione                              | Obbligatorio |
| ----------------- | ---------------------------------------- | -------- |
| type              | La proprietà type deve essere impostata su **AzureBatch**. | Sì      |
| accountName       | Nome dell'account Azure Batch.         | Sì      |
| accessKey         | Chiave di accesso per l'account Azure Batch.  | Sì      |
| batchUri          | URL di indirizzamento al proprio account di Azure Batch, nel formato https://*batchaccountname.region*.batch.azure.com. | Sì      |
| poolName          | Nome del pool di macchine virtuali.    | Sì      |
| linkedServiceName | Nome dello spazio di archiviazione del servizio collegato Azure associato al servizio collegato Azure Batch. Questo servizio collegato viene usato per organizzare i file necessari per eseguire l'attività. | Sì      |
| connectVia        | Il runtime di integrazione da usare per inviare le attività a questo servizio collegato. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No       |

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
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Proprietà
| Proprietà               | Descrizione                              | Obbligatorio                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | La proprietà type deve essere impostata su **AzureML**. | Sì                                      |
| mlEndpoint             | L’URL del batch punteggio.                   | Sì                                      |
| apiKey                 | Modello dell'area di lavoro pubblicato di API.     | Sì                                      |
| updateResourceEndpoint | L'URL della risorsa di aggiornamento per un endpoint del servizio Web di Azure ML usato per aggiornare il servizio Web predittivo con il file del modello con training | No                                       |
| servicePrincipalId     | Specificare l'ID client dell'applicazione.     | Obbligatorio se è specificato updateResourceEndpoint |
| servicePrincipalKey    | Specificare la chiave dell'applicazione.           | Obbligatorio se è specificato updateResourceEndpoint |
| tenant                 | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Obbligatorio se è specificato updateResourceEndpoint |
| connectVia             | Il runtime di integrazione da usare per inviare le attività a questo servizio collegato. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data Factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

### <a name="example"></a>Esempio

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Proprietà

| Proprietà             | Descrizione                              | Obbligatorio                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. | Sì                                      |
| accountName          | Nome dell'account di Azure Data Lake Analytics.  | Sì                                      |
| dataLakeAnalyticsUri | URI di Azure Data Lake Analytics.           | No                                       |
| subscriptionId       | ID sottoscrizione di Azure                    | No (se non specificata, viene usata la sottoscrizione della Data factory). |
| resourceGroupName    | Nome del gruppo di risorse di Azure                | No (se non specificata, viene usato il gruppo di risorse di Data Factory). |
| servicePrincipalId   | Specificare l'ID client dell'applicazione.     | Sì                                      |
| servicePrincipalKey  | Specificare la chiave dell'applicazione.           | Sì                                      |
| tenant               | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì                                      |
| connectVia           | Il runtime di integrazione da usare per inviare le attività a questo servizio collegato. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No                                       |



## <a name="azure-sql-database-linked-service"></a>Servizio collegato per il database SQL Azure
Si crea un servizio collegato di Azure SQL e lo si utilizza con l’ [Attività di stored procedure](transform-data-using-stored-procedure.md) per richiamare una procedura stored da una pipeline Data Factory. Vedere l’articolo [Connettore di Azure SQL](connector-azure-sql-database.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="azure-sql-data-warehouse-linked-service"></a>Servizio collegato di Azure SQL Data Warehouse
Si crea un servizio collegato di Azure SQL Data Warehouse e lo si usa con l' [attività di stored procedure](transform-data-using-stored-procedure.md) per richiamare una stored procedure da una pipeline Data Factory. Vedere l'articolo [Proprietà del servizio collegato di Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="sql-server-linked-service"></a>Servizio collegato di SQL Server
Si crea un servizio collegato di SQL Server e lo si usa con l' [attività di stored procedure](transform-data-using-stored-procedure.md) per richiamare una stored procedure da una pipeline Data Factory. Vedere l'articolo [Proprietà del servizio collegato SQL Server](connector-sql-server.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Regole di denominazione
La tabella seguente specifica le regole di denominazione per gli elementi di Data factory.

| Nome                             | Univocità del nome                          | Controlli di convalida                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data factory                     | Univoco in Microsoft Azure. Per i nomi non viene fatta distinzione tra maiuscole e minuscole: `MyDF` e `mydf`, ad esempio, fanno riferimento alla stessa data factory. | <ul><li>Ogni data factory è collegata a una sola sottoscrizione di Azure.</li><li>I nomi degli oggetti devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Nei nomi di contenitori non sono consentiti trattini consecutivi.</li><li>Il nome può contenere da 3 a 63 caratteri.</li></ul> |
| Servizi collegati/Tabelle/Pipeline | Univoco in una data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | <ul><li>Numero massimo di caratteri nel nome di una tabella: 260.</li><li>I nomi degli oggetti devono iniziare con una lettera, un numero o un carattere di sottolineatura (_).</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\".</li></ul> |
| Gruppo di risorse                   | Univoco in Microsoft Azure. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | <ul><li>Numero massimo di caratteri: 1000.</li><li>Il nome può contenere lettere, cifre e i caratteri seguenti: "-", "_", "," e "."</li></ul> |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco delle attività di trasformazione supportate da Azure Data Factory vedere [Trasformare i dati](transform-data.md).