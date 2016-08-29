<properties 
	pageTitle="Spostare dati con l'attività di copia | Microsoft Azure" 
	description="Informazioni sullo spostamento di dati in pipeline di Data Factory: migrazione di dati tra archivi cloud e tra archivi locali e cloud. Usare l'attività di copia." 
	keywords="copiare dati, spostamento di dati, migrazione di dati, trasferire dati"
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
	ms.date="08/08/2016" 
	ms.author="spelluru"/>

# Spostare dati con l'attività di copia

## Overview
Azure Data Factory consente di usare l'attività di copia per copiare i dati di diverse forme da una vasta gamma di origini dati locali e cloud in Azure, per poter ulteriormente trasformarli e analizzarli. L'attività di copia può essere usata anche per pubblicare risultati di trasformazione e analisi per Business Intelligence (BI) e l'utilizzo delle applicazioni.

![Ruolo dell'attività di copia](media/data-factory-data-movement-activities/copy-activity.png)

L'attività di copia è un servizio [disponibile a livello globale](#global) sicuro, affidabile e scalabile. Questo articolo fornisce informazioni dettagliate sullo spostamento dei dati in Data factory e sull'attività di copia. Prima di tutto, verrà illustrato come avviene la migrazione dei dati tra due archivi dati cloud e tra un archivio dati locale e un archivio dati cloud

> [AZURE.NOTE] Per informazioni sulle attività in generale, vedere l'articolo [Pipeline e attività in Azure Data factory](data-factory-create-pipelines.md).

### Copiare dati tra due archivi dati cloud
Nei casi in cui sia l'archivio dati di origine che l'archivio dati sink (destinazione) si trovano nel cloud, l'attività di copia prevede le fasi seguenti per copiare/spostare i dati dall'origine al sink. Il servizio su cui si basa l'attività di copia esegue queste operazioni:

1. Legge i dati dall'archivio dati di origine
2. Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo in base alle configurazioni dei set di dati di input e output e all'attività di copia
3.	Scrive i dati nell'archivio dati di destinazione

Il servizio sceglie automaticamente l'area ottimale per lo spostamento dei dati, in genere l'area più vicina all'archivio dati sink.

![copia da cloud a cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### Copiare dati tra un archivio dati locale e un archivio dati cloud
Per spostare in modo sicuro i dati tra gli archivi dati locali protetti da firewall aziendale e un archivio dati cloud, è necessario installare il Gateway di gestione dati, ovvero un agente che consente lo spostamento e l'elaborazione ibridi dei dati sul computer locale. Il Gateway di gestione dati può essere installato sullo stesso computer dell'archivio dati o su un computer separato che è in grado di accedere all'archivio dati. In questo scenario, la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo vengono eseguiti dal Gateway di gestione dati. In questo caso, i dati non transitano attraverso il servizio Data factory di Azure. Gateway di gestione dati scrive direttamente i dati nell'archivio di destinazione.

![copia da locale a cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Per l'introduzione e la procedura dettagliata leggere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md), mentre l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) fornisce informazioni dettagliate sul gateway di gestione dei dati.

È anche possibile spostare dati da/a archivi dati supportati ospitati su VM IaaS (macchine virtuali Infrastructure-as-a-Service) di Azure tramite il Gateway di gestione dati. In questo caso, il Gateway di gestione dati può essere installato sulla stessa macchina virtuale di Azure dell'archivio dati o su una macchina virtuale separata in grado di accedere all'archivio dati.

## Archivi dati e formati supportati
L'attività di copia esegue la copia dei dati da un archivio dati **di origine** a un archivio dati **sink**. Data factory supporta gli archivi dati seguenti ed **è possibile scrivere i dati da qualsiasi origine in qualsiasi sink**. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

Categoria | Archivio dati | Supportato come origine | Supportato come sink
:------- | :--------- | :------------------ | :-----------------
Azure | [BLOB Azure](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [Database SQL di Azure](data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Tabella di Azure](data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ 
Database | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; 
File | [File System](data-factory-onprem-file-system-connector.md)* <br/> [Hadoop Distributed File System (HDFS)](data-factory-hdfs-connector.md)* | ✓ <br/> ✓ <br/> | ✓ <br/> &nbsp; 
Altro | [Salesforce](data-factory-salesforce-connector.md)<br/> [ODBC generico](data-factory-odbc-connector.md)* <br/> [OData generico](data-factory-odata-connector.md) <br/> [Tabella Web (tabella da HTML)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] Gli archivi dati contrassegnati da un asterisco (*) possono essere locali o in IaaS di Azure e richiederanno l'installazione del [Gateway di gestione dati](data-factory-data-management-gateway.md) in un computer IaaS locale o in Azure.

Per spostare i dati da e verso un archivio dati che non è supportato dall'**attività di copia** è possibile usare l'**attività personalizzata** in Data Factory con la logica richiesta per copiare o spostare i dati. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

### Formati di file supportati
L'attività di copia può copiare i file così come sono tra due archivi dati basati su file, come Blob di Azure, File System e Hadoop Distributed File System (HDFS). A tale scopo, è possibile ignorare la [sezione formato](data-factory-create-datasets.md) nelle definizioni dei set di dati di input e di output per copiare i dati in modo efficiente senza alcuna serializzazione/deserializzazione.

L'attività di copia legge e scrive anche nei file in formati specificati: testo, Avro, ORC e JSON. Di seguito sono riportati alcuni esempi di attività di copia che è possibile ottenere:

-	Copiare i dati in formato testo (CSV) dal Blob di Azure e scrivere in SQL Azure
-	Copiare i file in formato testo (CSV) dal File System locale e scrivere nel Blob di Azure nel formato Avro
-	Copiare i dati nel Database SQL di Azure e scrivere in HDFS locale in formato ORC



## <a name="global"></a>Spostamento dei dati disponibile a livello globale
Il servizio su cui si basa l'attività di copia è disponibile a livello globale nelle aree geografiche seguenti, anche se Azure Data Factory è disponibile solo negli Stati Uniti occidentali, negli Stati Uniti orientali e in Europa settentrionale. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Per la disponibilità del servizio Data Factory e lo spostamento dei dati in un'area, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services).

### Copiare dati tra archivi dati cloud
Quando sia gli archivi dati di origine che gli archivi dati sink risiedono nel cloud, Azure Data Factory usa una distribuzione del servizio nell'area più vicina alla posizione del sink nella stessa area geografica per eseguire lo spostamento dei dati. Consultare la tabella seguente per il mapping:

Area dell'archivio dati di destinazione | Area usata per lo spostamento dei dati
:----------------------------------- | :----------------------------
Stati Uniti orientali | Stati Uniti orientali
Stati Uniti orientali 2 | Stati Uniti orientali 2
Stati Uniti centrali | Stati Uniti centrali
Stati Uniti occidentali | Stati Uniti occidentali
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
Quando i dati vengono copiati da locale (o macchina virtuale Azure IaaS) a cloud, lo spostamento dei dati viene eseguito dal [Gateway di gestione dati](data-factory-data-management-gateway.md) su un computer locale o una macchina virtuale IaaS di Azure. I dati non passano attraverso il servizio nel cloud, a meno che non si usi la funzionalità di [copia di staging](data-factory-copy-activity-performance.md#staged-copy), nel qual caso i dati passano attraverso l'archiviazione BLOB di Azure temporanea prima di essere scritti nell'archivio dati sink.


## Creare una pipeline con attività di copia 
È possibile creare una pipeline con l'attività di copia in alcuni modi:

### Con la Copia guidata
**Copia guidata di Data Factory** consente di creare una pipeline con un'attività di copia per copiare dati dalle origini supportate nelle destinazioni **senza scrivere definizioni JSON** per i servizi collegati, i set di dati e le pipeline. Per informazioni dettagliate sulla procedura guidata, vedere [Copia guidata di data factory](data-factory-copy-wizard.md).

### Con gli script JSON
È possibile usare l'Editor di Data factory nel Portale di Azure (oppure Visual Studio o Azure PowerShell) per creare una definizione JSON per una pipeline con l'attività di copia e distribuirla per creare la pipeline in Data factory. Vedere [Esercitazione: Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per un'esercitazione rapida sull'uso dell'attività di copia.

Per tutti i tipi di attività sono disponibili proprietà JSON come nome, descrizione, tabelle di input e output, criteri e così via. D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività.

Quando viene eseguita un'attività di copia, la sezione **typeProperties** varia a seconda dei tipi di origine e sink. Fare clic su un'origine o un sink nella sezione relativa [alle origini e ai sink supportati](#supported-data-stores) per informazioni sulle proprietà supportate dall'attività di copia per l'archivio dati.

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

La pianificazione definita nel set di dati di output determina quando viene eseguita l'attività (ad esempio **daily**: frequency: day e interval: 1). L'attività esegue la copia dei dati da un set di dati di input (**origine**) in un set di dati di output (**sink**). È possibile specificare più di un set di dati di input per l'attività di copia, che verranno usati per verificare le dipendenze prima che l'attività sia eseguita, ma solo i dati dal primo set di dati saranno copiati nel set di dati di destinazione. Per altre informazioni, vedere la sezione [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).

## Prestazioni e ottimizzazione 
Vedere l’articolo [Guida alle prestazione delle attività di copia e all’ottimizzazione](data-factory-copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Inoltre, vengono elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

## Pianificazione e copia sequenziale
Vedere l'articolo [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md)per informazioni dettagliate sul funzionamento della pianificazione e dell'esecuzione in Data factory.

È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Vedere la sezione [Copia ordinata](data-factory-scheduling-and-execution.md#ordered-copy) nell'articolo.

## Conversioni di tipi 
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi sink con il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Per trovare il mapping di un sistema di tipi nativo a .NET per uno specifico archivio dati, vedere gli argomenti relativi all'archivio dati corrispondente (fare clic sul rispettivo collegamento nella tabella [Archivi dati supportati](#supported-data-stores)). È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.

 
## Passaggi successivi
- Per informazioni su come usare l'attività di copia per spostare i dati da un archivio dati di origine a un archivio dati sink in generale, vedere l'articolo [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Per informazioni sullo spostamento dei dati da un archivio di dati locale a un archivio di dati cloud, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).
 

<!---HONumber=AcomDC_0817_2016-->