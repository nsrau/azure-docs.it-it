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
	ms.date="05/31/2016"
	ms.author="spelluru"/>

# Servizi collegati di calcolo

Questo articolo spiega i diversi ambienti di calcolo che è possibile utilizzare per elaborare o una trasformare dati. Fornisce inoltre informazioni dettagliate sulle diverse configurazioni (on-demand e bring your own) supportate da Data Factory durante la configurazione di servizi collegati che collegano questi ambienti a una data factory di Azure.

## Ambiente di calcolo “on-demand”

In questo tipo di configurazione, l'ambiente informatico è completamente gestito dal servizio Azure Data Factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico.

> [AZURE.NOTE] La configurazione su richiesta è attualmente supportata solo per i cluster HDInsight di Azure.

## Servizio collegato Azure HDInsight su richiesta
Il servizio Data factory di Azure può creare automaticamente un cluster HDInsight su richiesta basato su Windows o Linux per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione (proprietà linkedServiceName in JSON) associato al cluster.

Tenere presente i seguenti punti **importanti**sul servizio collegato HDInsight su richiesta:

- Non verrà visualizzato il cluster HDInsight su richiesta creato nella sottoscrizione di Azure; il servizio di Azure Data Factory gestisce il cluster HDInsight su richiesta per conto dell'utente.
- I registri per i processi eseguiti su un cluster HDInsight su richiesta vengono copiati nell'account di archiviazione associato al cluster HDInsight. È possibile accedere a questi log dal portale di Azure nel pannello **Dettagli di esecuzione di attività**. Per informazioni dettagliate, vedere l'articolo [Monitoraggio e gestione delle pipeline](data-factory-monitor-manage-pipelines.md).
- Ti verrà addebitato solo il tempo in cui il cluster HDInsight è attivo e i processi in esecuzione.

> [AZURE.IMPORTANT] Richiede in genere più di**15 minuti**per il provisioning di un cluster HDInsight di Azure su richiesta.

### Esempio
Il codice JSON seguente definisce un servizio collegato HDInsight su richiesta basato su Linux. Il servizio Data factory crea automaticamente un cluster HDInsight **basato su Linux** durante l'elaborazione di una sezione di dati.


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

Per usare un cluster HDInsight basato su Windows, impostare **osType** su **windows** oppure evitare di usare la proprietà dato che il valore predefinito è windows.

> [AZURE.IMPORTANT] 
Il cluster HDInsight crea un **contenitore predefinito** nell'archiviazione BLOB specificata nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Si tratta di un comportamento previsto da progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione.
> 
> Man mano che vengono elaborate sempre più sezioni, verranno visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. Il nome di questi contenitori segue uno schema: "adf**nomedatafactory**-**nomeserviziocollegato**-datatimestamp". Per eliminare i contenitori nell'archiviazione BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

### Proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su **HDInsightOnDemand**. | Sì
clusterSize | Numero di nodi del ruolo di lavoro/nodi dati nel cluster. Il cluster HDInsight viene creato con 2 nodi head e il numero di nodi del ruolo di lavoro specificato per questa proprietà. I nodi sono di dimensione Standard\_D3, con 4 core, quindi un cluster di 4 nodi del ruolo di lavoro avrà 24 core, ossia 4*4 per nodi del ruolo di lavoro + 2*4 per nodi head. Vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) per i dettagli sul livello Standard\_D3. | Sì
timetolive | Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight su richiesta rimane attivo dopo il completamento di un'attività eseguita se non sono presenti altri processi attivi del cluster.<br/><br/>Ad esempio, se un'esecuzione di attività accetta 6 minuti e timetolive è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'attività. Se un'altra attività viene eseguita entro i 6 minuti consentiti, verrà elaborata dal cluster stesso.<br/><br/>La creazione di un cluster HDInsight su richiesta è un'operazione costosa (potrebbe richiedere alcuni minuti), quindi utilizzare questa impostazione per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.<br/><br/>Se si imposta il valore della proprietà timetolive su 0, il cluster viene eliminato non appena l'attività in elaborazione termina. D'altra parte, se si imposta un valore elevato, il cluster può rimanere inattivo inutilmente causando costi elevati. È quindi importante impostare il valore appropriato in base alle esigenze.<br/><br/>Più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta se il valore della proprietà timetolive è impostato in modo appropriato | Sì
version | Versione del cluster HDInsight Il valore predefinito è 3.1 per cluster Windows e 3.2 per cluster Linux. | No
linkedServiceName | L'archivio BLOB che il cluster su richiesta deve utilizzare per l'archiviazione e l'elaborazione dei dati. | Sì
additionalLinkedServiceNames | Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight in modo che il servizio Data Factory possa registrarli per conto dell'utente. | No
osType | Tipo di sistema operativo. I valori consentiti sono: Windows (impostazione predefinita) e Linux | No
hcatalogLinkedServiceName | Il nome del servizio collegato di Azure SQL che fa riferimento al database HCatalog. Verrà creato il cluster HDInsight su richiesta utilizzando il database SQL di Azure come metastore. | No


#### Esempio di codice JSON additionalLinkedServiceNames

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### Proprietà avanzate

È inoltre possibile specificare le seguenti proprietà per la configurazione granulare del cluster HDInsight su richiesta.

Proprietà | Descrizione | Obbligatorio
:-------- | :----------- | :--------
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

### Dimensioni dei nodi
È possibile specificare le dimensioni di head, dei dati e dei nodi zookeeper usando le seguenti proprietà.

Proprietà | Descrizione | Obbligatorio
:-------- | :----------- | :--------
headNodeSize | Specifica le dimensioni del nodo head Il valore predefinito è Standard\_D3. Vedere la sezione **Specificare le dimensioni dei nodi** illustrata di seguito per informazioni dettagliate. | No
dataNodeSize | Specifica le dimensioni del nodo dei dati. Il valore predefinito è Standard\_D3. | No
zookeeperNodeSize | Specifica le dimensioni del nodo Zookeeper. Il valore predefinito è Standard\_D3. | No
 
#### Specificare le dimensioni dei nodi
Vedere l’articolo [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) per i valori della stringa che è necessario specificare per le proprietà precedenti. I valori devono essere conformi ai **CMDLET e API** a cui si fa riferimento nell'articolo. Come si vede nell'articolo, il nodo dei dati di grandi dimensioni (per impostazione predefinita) ha 7 GB di memoria, il che potrebbe non andare abbastanza bene per il proprio scenario.

Se si desidera creare nodi head e nodi del ruolo di lavoro di dimensioni D4, è necessario specificare **Standard\_D4** come valore per le proprietà headNodeSize e dataNodeSize.

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

Se si specifica un valore errato per queste proprietà, potrebbe essere visualizzato il seguente **errore:** impossibile creare il cluster. Eccezione: impossibile completare l'operazione di creazione del cluster. Operazione non riuscita con codice ’400’. Nello stato del cluster è apparso il messaggio 'Errore'. Messaggio: ’PreClusterCreationValidationFailure’. Quando si riceve questo errore, assicurarsi che si stia utilizzando il nome **CMDLET e API** dalla tabella presente nell'articolo precedente.



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
linkedServiceName | Nome del servizio collegato per l'archiviazione di BLOB utilizzato da questo cluster HDInsight. | Sì

## Servizio collegato Azure Batch

È possibile creare un servizio collegato di Azure Batch per registrare un pool di Batch di macchine virtuali (VM) a una data factory. È possibile eseguire le attività .NET personalizzate utilizzando Batch Azure o Azure HDInsight.

Vedere i seguenti argomenti se non si ha familiarità con il servizio di Azure Batch:


- [Nozioni di base di Azure Batch](../batch/batch-technical-overview.md) per una panoramica del servizio Azure Batch.
- Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) per creare un account Azure Batch oppure [portale di Azure](../batch/batch-account-create-portal.md)per creare l'account Azure Batch tramite il portale di Azure. Per istruzioni dettagliate sull'utilizzo del cmdlet, consultare l’argomento [Utilizzo di Azure PowerShell per gestire l'account Batch di Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
- Cmdlet [New AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) per creare un pool di Batch di Azure.

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

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

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


## Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data factory di Azure prima di usare l'[attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) in una pipeline.

Nell'esempio seguente viene fornita la definizione JSON per un servizio collegato di Azure Data Lake Analytics.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>",
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
Tipo | La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. | Sì
accountName | Nome dell'account di Azure Data Lake Analytics. | Sì
dataLakeAnalyticsUri | URI di Azure Data Lake Analytics. | No
autorizzazione | Il codice di autorizzazione viene recuperato automaticamente dopo aver fatto clic sul pulsante **Autorizza** nell'editor di Data factory e aver completato l'accesso OAuth. | Sì
subscriptionId | ID sottoscrizione di Azure | No (se non specificata, viene usata la sottoscrizione della Data factory).
resourceGroupName | Nome del gruppo di risorse di Azure | No (se non specificata, viene usato il gruppo di risorse della Data factory).
sessionId | ID di sessione dalla sessione di autorizzazione OAuth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questo valore viene generato automaticamente nell'editor di Data factory. | Sì

Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato il messaggio di errore seguente: Credential operation error: invalid\_grant - AADSTS70002: Error validating credentials. (Errore dell'operazione relativa alle credenziali: invalid\_grant - AADSTS70002:Errore durante la convalida delle credenziali). AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z.

| Tipo di utente | Scade dopo |
| :-------- | :----------- | 
| Account utente NON gestiti da Azure Active Directory (@hotmail.com, @live.com, ecc.) | 12 ore |
| Account utente gestiti da Azure Active Directory (AAD) | 14 giorni dopo l'esecuzione dell'ultima sezione. <br/><br/>90 giorni, se viene eseguita una sezione basata sul servizio collegato OAuth almeno una volta ogni 14 giorni. |
 
Per evitare/risolvere questo problema, alla **scadenza del token** è necessario ripetere l'autorizzazione con il pulsante **Autorizza** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà sessionId e authorization a livello di codice usando il codice riportato nella sezione seguente.

### Per generare valori sessionId e authorization a livello di codice 
Il codice seguente genera i valori **sessionId** e **authorization**.

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

Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). È necessario aggiungere un riferimento a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe WindowsFormsWebAuthenticationDialog.
 

## Servizio collegato di Azure SQL

Si crea un servizio collegato di Azure SQL e lo si utilizza con l’[Attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una procedura stored da una pipeline Data Factory. Vedere l’articolo [Connettore di Azure SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) per informazioni dettagliate su questo servizio collegato.

<!---HONumber=AcomDC_0601_2016-->