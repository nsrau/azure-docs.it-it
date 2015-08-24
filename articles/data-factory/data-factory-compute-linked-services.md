<properties 
	pageTitle="Servizi collegati di calcolo | Microsoft Azure" 
	description="Informazioni sugli ambienti di calcolo che è possibile utilizzare nelle pipeline di Data Factory di Azure per trasformare/elaborare i dati.." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Servizi collegati di calcolo

Questo articolo spiega i diversi ambienti di calcolo che è possibile utilizzare per elaborare o una trasformare dati. Fornisce inoltre informazioni dettagliate sulle diverse configurazioni (on-demand e bring your own) supportate da Data Factory durante la configurazione di servizi collegati che collegano questi ambienti a una data factory di Azure.

## Ambiente di calcolo “on-demand”

In questo tipo di configurazione, l'ambiente informatico è completamente gestito dal servizio Azure Data Factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico.

> [AZURE.NOTE]La configurazione su richiesta è attualmente supportata solo per i cluster HDInsight di Azure.

## Servizio collegato Azure HDInsight su richiesta

Il cluster HDInsight su richiesta viene creato e gestito dal servizio Data Factory di Azure per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione (proprietà linkedServiceName in JSON) associato al cluster.

Tenere presente i seguenti punti **importanti**sul servizio collegato HDInsight su richiesta:

- Non verrà visualizzato il cluster HDInsight su richiesta creato nella sottoscrizione di Azure; il servizio di Azure Data Factory gestisce il cluster HDInsight su richiesta per conto dell'utente.
- I registri per i processi eseguiti su un cluster HDInsight su richiesta vengono copiati nell'account di archiviazione associato al cluster HDInsight. È possibile accedere a questi log dal portale di Azure nel pannello **Dettagli di esecuzione di attività**. Per informazioni dettagliate, vedere l'articolo [Monitoraggio e gestione delle pipeline](data-factory-monitor-manage-pipelines.md). 
- Ti verrà addebitato solo il tempo in cui il cluster HDInsight è attivo e i processi in esecuzione.

> [AZURE.IMPORTANT]Richiede in genere più di**15 minuti**per il provisioning di un cluster HDInsight di Azure su richiesta.

### Esempio

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### Proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su **HDInsightOnDemand**. | Sì
clusterSize | La dimensione del cluster su richiesta. Specifica il numero di nodi che si desidera per questo cluster su richiesta. | Sì 
jobscontainer | Il contenitore di BLOB che contiene i dati utilizzati dai processi pig/hive/package e in cui verranno archiviati i registri cluster. | Sì
timetolive | <p>Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight su richiesta rimane attivo dopo il completamento di un'attività eseguita se non sono presenti altri processi attivi del cluster.</p><p>Ad esempio, se un'esecuzione di attività accetta 6 minuti e timetolive è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'attività. Se un'altra attività viene eseguita entro i 6 minuti consentiti, verrà elaborata dal cluster stesso.</p><p>La creazione di un cluster HDInsight su richiesta è un'operazione costosa (potrebbe richiedere alcuni minuti), quindi utilizzare questa impostazione per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.</p><p>Se si imposta il valore della proprietà timetolive su 0, il cluster viene eliminato non appena l'attività in elaborazione termina. D'altra parte, se si imposta un valore elevato, il cluster può rimanere inattivo inutilmente causando costi elevati. Pertanto, è importante impostare il valore appropriato in base alle esigenze.</p><p>Più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta se il valore della proprietà timetolive è impostato in modo appropriato</p> | Sì
version | Versione del cluster HDInsight | No
linkedServiceName | L'archivio BLOB che il cluster su richiesta deve utilizzare per l'archiviazione e l'elaborazione dei dati. | Sì
additionalLinkedServiceNames | Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight in modo che il servizio Data Factory possa registrarli per conto dell'utente. | No

### Proprietà avanzate

È inoltre possibile specificare le seguenti proprietà per la configurazione granulare del cluster HDInsight su richiesta.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
coreConfiguration | Specifica i parametri di configurazione di base (ad esempio core-site.xml) per il cluster HDInsight da creare. | No
hBaseConfiguration | Specifica i parametri di configurazione HBase (hbase-site.xml) per il cluster HDInsight. | No
hdfsConfiguration | Specifica i parametri di configurazione HDFS (hdfs-site.xml) per il cluster HDInsight. | No
hiveConfiguration | Specifica i parametri di configurazione hive (hive-site.xml) per il cluster HDInsight. | No
mapReduceConfiguration | Specifica i parametri di configurazione MapReduce (mapred-site.xml) per il cluster HDInsight. | No
oozieConfiguration | Specifica i parametri di configurazione Oozie (oozie-site.xml) per il cluster HDInsight. | No
stormConfiguration | Specifica i parametri di configurazione Storm (storm-site.xml) per il cluster HDInsight. | No
yarnConfiguration | Specifica i parametri di configurazione Yarn (yarn-site.xml) per il cluster HDInsight. | No

#### Esempio: configurazione del cluster HDInsight su richiesta con le proprietà avanzate

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

## Ambiente di calcolo “bring your own” 

In questo tipo di configurazione, gli utenti possono registrare un ambiente informatico già esistente come servizio collegato in Data Factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data Factory viene utilizzato per eseguire le attività.
 
Questo tipo di configurazione è supportato per gli ambienti di calcolo seguenti:

- HDInsight di Azure
- Azure Batch 
- Azure Machine Learning

## Servizio collegato Azure HDInsight

È possibile creare un servizio collegato Azure HDInsight per registrare il proprio cluster HDInsight con Data Factory.

### Esempio

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su **HDInsight**. | Sì
clusterUri | L'URI del cluster HDInsight. | Sì
username | Specifica il nome dell'utente da utilizzare per connettersi a un cluster HDInsight esistente. | Sì
password | Specifica la password per l'account utente. | Sì
location | Specifica il percorso del cluster HDInsight (ad esempio: WestUS). | Sì
linkedServiceName | Nome del servizio collegato per l'archiviazione di BLOB utilizzato da questo cluster HDInsight. | Sì

## Servizio collegato Azure Batch

È possibile creare un servizio collegato di Azure Batch per registrare un pool di Batch di macchine virtuali (VM) a una data factory. È possibile eseguire le attività .NET personalizzate utilizzando Batch Azure o Azure HDInsight.

Vedere i seguenti argomenti se non si ha familiarità con il servizio di Azure Batch:
 

- [Panoramica tecnica di Azure Batch](../batch/batch-technical-overview.md)per una panoramica del servizio Azure Batch.
- cmdlet [New AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx)per creare un account Azure Batch (o)[Portale di gestione Azure](../batch/batch-technical-overview.md)per creare l'account Azure Batch tramite il portale di gestione di Azure. Per istruzioni dettagliate sull'utilizzo del cmdlet, consultare [Utilizzo di Azure PowerShell per gestire l'account di Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
- cmdlet [New AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx)per creare un pool di Batch di Azure.

### Esempio

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

Aggiungere "**.<region name**" al nome dell'account di batch per la proprietà **accountName**. Esempio:
	
			"accountName": "mybatchaccount.eastus" 

Un'altra opzione consiste nel fornire l’endpoint batchUri come illustrato di seguito.

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### Proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su **AzureBatch**. | Sì
accountName | Nome dell'account Azure Batch. | Sì
accessKey | Chiave di accesso per l'account Azure Batch. | Sì
poolName | Nome del pool di macchine virtuali. | Sì
linkedServiceName | Nome dello spazio di archiviazione del servizio collegato Azure associato al servizio collegato Azure Batch. Questo servizio collegato viene utilizzato per organizzare i file necessari per eseguire l'attività e archiviare i log di esecuzione dell’attività. | Sì


## Servizio collegato di Azure Machine Learning

Si crea un servizio collegato di Azure Machine Learning per registrare un endpoint di punteggio batch Machine Learning a una data factory.

### Esempio

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

### Proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
Tipo | La proprietà type deve essere impostata su **AzureML**. | Sì
mlEndpoint | L’URL del batch punteggio. | Sì
apiKey | Modello dell'area di lavoro pubblicato di API. | Sì


## Servizio collegato di Azure SQL

Si crea un servizio collegato di Azure SQL e lo si utilizza con l’[Attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una procedura stored da una pipeline Data Factory. Vedere l’articolo [Connettore di Azure SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) per informazioni dettagliate su questo servizio collegato.


  



     
 
   

<!---HONumber=August15_HO7-->