<properties
	pageTitle="Spostare dati con l'attività di copia | Microsoft Azure"
	description="Informazioni sullo spostamento di dati in pipeline di Data Factory: migrazione di dati tra archivi cloud e tra archivi locali e cloud. Usare l'attività di copia."
	keywords="copiare dati, spostamento di dati, migrazione di dati, trasferire dati"
	services="data-factory"
	documentationCenter=""
	authors="linda33wj"
	manager="jhubbard"
	editor="monicar"/>  

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="jingwang"/>  

# Spostare dati con l'attività di copia

## Overview
In Data Factory di Azure, è possibile usare l'attività di copia per copiare i dati di diverse forme da varie origini dati locali e nel cloud in Azure. Dopo la copia, è possibile trasformare o analizzare ulteriormente i dati. L'attività di copia può essere usata anche per pubblicare risultati di trasformazione e analisi per Business Intelligence (BI) e l'uso delle applicazioni.

![Ruolo dell'attività di copia](media/data-factory-data-movement-activities/copy-activity.png)  

L'attività di copia è un servizio [disponibile a livello globale](#global) sicuro, affidabile e scalabile. Questo articolo fornisce informazioni dettagliate sullo spostamento dei dati in Data factory e sull'attività di copia.

Prima di tutto, verrà illustrato come avviene la migrazione dei dati tra due archivi dati cloud e tra un archivio dati locale e un archivio dati cloud.

> [AZURE.NOTE] Per informazioni sulle attività in generale, vedere l'articolo [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md).

### Copiare dati tra due archivi dati cloud
Nei casi in cui sia l'archivio dati di origine che l'archivio dati sink si trovano nel cloud, l'attività di copia viene sottoposta alle fasi seguenti per copiare i dati dall'origine al sink. Il servizio su cui si basa l'attività di copia:

1. Legge i dati dall'archivio dati di origine.
2. Esegue la serializzazione/deserializzazione, compressione/decompressione, il mapping di colonne e la conversione dei tipi. Esegue tali operazioni sulla base delle configurazioni del set di dati di input, del set di dati di output e dell'attività di copia.
3.	Scrive i dati nell'archivio dati di destinazione.

Il servizio sceglie automaticamente l'area ottimale per eseguire lo spostamento dei dati. Questa area è in genere quella più vicino all'archivio dati sink.

![Copia da cloud a cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)  


### Copiare dati tra un archivio dati locale e un archivio dati cloud
Per spostare in modo sicuro i dati tra gli archivi dati locali e un archivio dati cloud, è necessario installare il gateway di gestione dati sul computer locale. Il Gateway di gestione dati è un agente che consente lo spostamento e l'elaborazione ibridi dei dati. Il Gateway di gestione dati può essere installato sullo stesso computer dell'archivio dati o su un computer separato che è in grado di accedere all'archivio dati.

In questo scenario, il Gateway di gestione dati esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo. I dati non transitano attraverso il servizio Data Factory di Azure. Gateway di gestione dati scrive direttamente i dati nell'archivio di destinazione.

![Copia da locale a cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)  

Per un'introduzione e una procedura dettagliata, vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md). Per informazioni dettagliate sull'agente, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md).

È anche possibile spostare dati da/da archivi dati supportati ospitati su macchine virtuali IaaS di Azure tramite il Gateway di gestione dati. In questo caso, è possibile installare il Gateway di gestione dati sulla stessa macchina virtuale dell'archivio dati o su una macchina virtuale separata con accesso all'archivio dati.

## Archivi dati e formati supportati
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Per spostare dati da e verso un archivio dati che non è supportato dall'attività di copia, usare l'**attività personalizzata** in Data Factory con la logica personalizzata per copiare o spostare i dati. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

### Formati di file supportati
È possibile usare l'attività di copia per copiare i file così come sono tra due archivi dati basati su file, come BLOB di Azure, File System e Hadoop Distributed File System (HDFS). A tale scopo, è possibile ignorare la [sezione Formato](data-factory-create-datasets.md) in entrambe le definizioni di set di dati di input e output. I dati vengono copiati in modo efficiente senza serializzazione/deserializzazione.

L'attività di copia legge e scrive anche nei file in formati specificati: testo, Avro, ORC, Parquet e JSON. È possibile eseguire le attività di copia seguenti, ad esempio:

-	Copiare i dati in formato testo (CSV) dal BLOB di Azure e scrivere nel database SQL Azure.
-	Copiare i file in formato testo (CSV) dal File System locale e scrivere nel BLOB di Azure nel formato Avro.
-	Copiare i dati nel database SQL di Azure e scrivere in HDFS locale in formato ORC.



## <a name="global"></a>Spostamento dei dati disponibile a livello globale
Azure Data Factory è disponibile solo nelle seguenti aree: Stati Uniti occidentali, Stati Uniti orientali ed Europa settentrionale. Tuttavia, il servizio alla base dell'attività di copia è disponibile a livello globale nelle aree geografiche seguenti. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Per la disponibilità del servizio Data Factory e lo spostamento dei dati in un'area, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services).

### Copiare dati tra archivi dati cloud
Quando sia gli archivi dati di origine che gli archivi dati sink risiedono nel cloud, Data Factory usa una distribuzione del servizio nell'area più vicina al sink nella stessa area geografica per spostare i dati. Consultare la tabella seguente per il mapping:

Area dell'archivio dati di destinazione | Area usata per lo spostamento dei dati
:----------------------------------- | :----------------------------
Stati Uniti orientali | Stati Uniti orientali
Stati Uniti orientali 2 | Stati Uniti orientali 2
Stati Uniti occidentali | Stati Uniti occidentali
Stati Uniti occidentali 2 | Stati Uniti occidentali
Stati Uniti centrali | Stati Uniti centrali
Stati Uniti centro-occidentali | Stati Uniti centrali
Stati Uniti centro-settentrionali | Stati Uniti centro-settentrionali
Stati Uniti centro-meridionali | Stati Uniti centro-meridionali
Europa settentrionale | Europa settentrionale
Europa occidentale | Europa occidentale
Asia sudorientale | Asia sudorientale
Asia orientale | Asia sudorientale
Giappone orientale | Giappone orientale
Giappone occidentale | Giappone orientale
Brasile meridionale | Brasile meridionale
Australia orientale | Australia orientale
Australia sudorientale | Australia sudorientale
India centrale | India centrale
India meridionale | India centrale
India occidentale | India centrale


> [AZURE.NOTE] Se l'area dell'archivio dati di destinazione non è nell'elenco precedente, l'attività di copia non viene completata invece di passare attraverso un'area alternativa.

### Copiare dati tra un archivio dati locale e un archivio dati cloud
Quando i dati vengono copiati da archivi locali (o macchina virtuale Azure/IaaS) ad archivi cloud, lo spostamento dei dati viene eseguito dal [Gateway di gestione dati](data-factory-data-management-gateway.md) su un computer locale o una macchina virtuale. I dati non passano attraverso il servizio nel cloud, a meno che non si usi la funzionalità di [Copia di staging](data-factory-copy-activity-performance.md#staged-copy). In questo caso, i dati passano attraverso l'archiviazione BLOB di Azure temporanea prima che vengano scritto nell'archivio dati sink.

## Creare una pipeline con attività di copia
È possibile creare una pipeline con l'attività di copia in alcuni modi:

### Con la copia guidata
La copia guidata di Data Factory aiuta a creare una pipeline con l'attività di copia. Questa pipeline consente di copiare dati dalle origini supportate nelle destinazioni *senza scrivere definizioni JSON* per i servizi collegati, i set di dati e le pipeline. Per informazioni dettagliate sulla procedura guidata, vedere [Copia guidata di Data Factory](data-factory-copy-wizard.md).

### Con gli script JSON
È possibile usare l'Editor di Data Factory nel portale di Azure, in Visual Studio o in Azure PowerShell per creare una definizione JSON per una pipeline con l'attività di copia. A questo punto, è possibile eseguire la distribuzione in modo da creare la pipeline in Data Factory. Per un'esercitazione con istruzioni dettagliate, vedere [Esercitazione: Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Per tutti i tipi di attività sono disponibili proprietà JSON come nome, descrizione, tabelle di input e output e criteri. Le proprietà disponibili nella sezione `typeProperties` dell'attività variano per ogni tipo di attività.

Per l'attività di copia, la sezione `typeProperties` varia a seconda dei tipi di origine e sink. Fare clic su un'origine o un sink nella sezione relativa [alle origini e ai sink supportati](#supported-data-stores) per informazioni sulle proprietà supportate dall'attività di copia per l'archivio dati.

Di seguito è riportata una definizione JSON di esempio:

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from Azure blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputBlobTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputSQLTable"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	}

La pianificazione definita nel set di dati di output determina quando viene eseguita l'attività (ad esempio **daily**: frequency: **day** e interval: **1**). L'attività esegue la copia dei dati da un set di dati di input (**origine**) in un set di dati di output (**sink**).

È possibile specificare più di un set di dati di input per l'attività di copia. Vengono usati per verificare le dipendenze prima dell'esecuzione dell'attività. Tuttavia, vengono copiati solo i dati dal primo set di dati al set di dati di destinazione. Per altre informazioni, vedere [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).

## Prestazioni e ottimizzazione
Vedere l'articolo [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Vengono anche elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

## Pianificazione e copia sequenziale
Vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md) per informazioni dettagliate sul funzionamento della pianificazione e dell'esecuzione in Data Factory. È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Vedere la sezione relativa alla [copia ordinata](data-factory-scheduling-and-execution.md#ordered-copy).

## Conversioni di tipi
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi sink con il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET.
2. Conversione dal tipo .NET al tipo di sink nativo.

Il mapping da un sistema di tipo nativo a un tipo .NET per un archivio dati è disponibile nell'articolo corrispondente sull'archivio dati. Fare clic sul link specifico della [Archivi dati e formati supportati](#supported-data-stores). È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.


## Passaggi successivi
- Per ulteriori informazioni sull'attività di copia, vedere [Copiare i dati dall'archiviazione BLOB di Azure al database SQL di Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Per informazioni sullo spostamento dei dati da un archivio di dati locale a un archivio di dati cloud, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

<!---HONumber=AcomDC_0928_2016-->