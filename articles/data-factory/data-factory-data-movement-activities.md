<properties 
	pageTitle="Attività di spostamento dei dati" 
	description="Informazioni sulle entità di Data factory che è possibile usare per spostare dati in una pipeline di Data factory." 
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
	ms.date="12/01/2015" 
	ms.author="spelluru"/>

# Attività di spostamento dei dati
L'[attività di copia](#copyactivity) esegue lo spostamento dei dati in Data factory di Azure ed è avviata da un [servizio di spostamento dei dati disponibile a livello globale](#global), in grado di copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Il servizio sceglie automaticamente l'area geografica ottimale per eseguire l'operazione di spostamento dei dati in base alla posizione degli archivi dati di origine e sink. Attualmente viene usata l'area più vicina all'archivio dati sink.
 
Di seguito vengono illustrati i modi in cui avviene lo spostamento dei dati in scenari diversi.

## Copia di dati tra due archivi dati cloud
Nei casi in cui sia l'archivio dati di origine che l'archivio dati sink (destinazione) si trovano nel cloud, l'attività di copia prevede le fasi seguenti per copiare/spostare i dati dall'origine al sink. Servizio di spostamento dei dati

1. Legge i dati dall'archivio dati di origine
2.	Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo in base alle configurazioni dei set di dati di input e output e all'attività di copia 
3.	Scrive i dati nell'archivio dati di destinazione

![copia da cloud a cloud](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copia dei dati tra un archivio dati locale e un archivio dati cloud
Per [spostare in modo sicuro i dati tra gli archivi dati locali protetti da firewall aziendale e un archivio dati cloud](#moveonpremtocloud), è necessario installare il Gateway di gestione dati, ovvero un agente che consente lo spostamento e l'elaborazione ibridi dei dati sul computer locale. Il Gateway di gestione dati può essere installato sullo stesso computer dell'archivio dati o su un computer separato che è in grado di accedere all'archivio dati. In questo scenario, la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo vengono eseguiti dal Gateway di gestione dati. Il servizio di spostamento dei dati non viene usato in questo scenario.

![copia da locale a cloud](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copiare dati da/a un archivio dati in una macchina virtuale IaaS di Azure 
È anche possibile spostare dati da/a archivi dati supportati ospitati su VM IaaS (macchine virtuali Infrastructure-as-a-Service) di Azure tramite il Gateway di gestione dati. In questo caso, il Gateway di gestione dati può essere installato sulla stessa macchina virtuale di Azure dell'archivio dati o su una macchina virtuale separata in grado di accedere all'archivio dati.

## Archivi dati supportati
L'attività di copia esegue la copia dei dati da un archivio dati **di origine** a un archivio dati **sink**. Data factory supporta gli archivi dati seguenti ed **è possibile scrivere i dati da qualsiasi origine in qualsiasi sink**. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

| Origini| Sink |
|:------- | :---- |
| <ul><li>[BLOB di Azure](data-factory-azure-blob-connector.md)</li><li>[Tabella di Azure](data-factory-azure-table-connector.md)</li><li>[Database SQL di Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (vedere la nota riportata di seguito)](data-factory-azure-documentdb-connector.md)</li><li>[Archivio Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server locale/IaaS di Azure](data-factory-sqlserver-connector.md)</li><li>[File system locale/IaaS di Azure](data-factory-onprem-file-system-connector.md)</li><li>[Database Oracle locale/IaaS di Azure](data-factory-onprem-oracle-connector.md)</li><li>[Database MySQL locale/IaaS di Azure](data-factory-onprem-mysql-connector.md)</li><li>[Database DB2 locale/IaaS di Azure](data-factory-onprem-db2-connector.md)</li><li>[Database Teradata locale/IaaS di Azure](data-factory-onprem-teradata-connector.md)</li><li>[Database di Sybase locale/IaaS di Azure](data-factory-onprem-sybase-connector.md)</li><li>[Database PostgreSQL locale/IaaS di Azure](data-factory-onprem-postgresql-connector.md)</li><li>[Origini dati ODBC](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS)](data-factory-hdfs-connector.md)</li></ul> | <ul><li>[BLOB di Azure](data-factory-azure-blob-connector.md)</li><li>[Tabella di Azure](data-factory-azure-table-connector.md)</li><li>[Database SQL di Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (vedere la nota riportata di seguito)](data-factory-azure-documentdb-connector.md)</li><li>[Archivio Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server locale/IaaS di Azure](data-factory-sqlserver-connector.md)</li><li>[File system locale/IaaS di Azure](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] È possibile spostare solo da e verso Azure DocumentDB da e verso altri servizi di Azure come BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Azure DocumentDB e Archivio Azure Data Lake. Anche la matrice completa per Azure Document DB sarà supportata a breve.

## Esercitazione
Per un'esercitazione rapida sull'uso dell'attività di copia, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](data-factory-get-started.md). Nell'esercitazione si userà l'attività di copia per copiare dati da un'archiviazione BLOB di Azure a un database SQL di Azure. Nella sezione seguente sono elencate tutte le origini e i sink supportati dall'attività di copia.

## <a name="copyactivity"></a>Attività di copia
L'attività di copia usa un set di dati di input (**origine**) e un set di dati di output (**sink**). La copia dei dati viene eseguita in modalità batch in base alla pianificazione specificata per l'attività. Per informazioni sulla definizione generale di attività a livello elevato, ad esempio alcune sezioni JSON e proprietà disponibili per tutte le attività, vedere l'articolo [Informazioni su pipeline e attività](data-factory-create-pipelines.md).

L'attività di copia offre le funzionalità seguenti:

### <a name="global"></a>Spostamento dei dati disponibile a livello globale
Nonostante Data factory di Azure sia disponibile solo negli Stati Uniti occidentali e in Nord Europa, il servizio di spostamento dei dati che avvia l'attività di copia è disponibile a livello globale nelle aree geografiche seguenti. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e nella maggior parte dei casi consente di evitare passaggi tra diverse aree.

| Area | Area geografica |
| ------ | --------- | 
| Stati Uniti centrali | Stati Uniti |
| Stati Uniti Orientali | Stati Uniti |
| Stati Uniti Orientali 2 | Stati Uniti |
| Stati Uniti centro-settentrionali | Stati Uniti |
| Stati Uniti centro-meridionali | Stati Uniti |
| Stati Uniti occidentali | Stati Uniti |
| Brasile meridionale | America Latina |
| Europa settentrionale | Europa, Medio Oriente e Africa |
| Europa occidentale | Europa, Medio Oriente e Africa |
| Asia sudorientale | Asia Pacifico |
| Giappone orientale | Asia Pacifico |

Tenere presente quanto segue:

- Se si stanno copiando dati da un'**origine dati locale** al **cloud** o viceversa (ad esempio: SQL Server locale -> BLOB di Azure), lo spostamento dei dati viene eseguito dal **Gateway di gestione dati** nell'ambiente locale, senza usare il servizio di spostamento dei dati.
- Se si stanno copiando dati da un'**origine cloud** a una **destinazione cloud** (ad esempio: BLOB di Azure -> SQL Azure), il **servizio di spostamento dei dati** sceglie la distribuzione **più vicina alla posizione del sink nella stessa area geografica** per eseguire il trasferimento. Ad esempio, se si esegue la copia dall'Asia sud-orientale al Giappone occidentale, viene usata la distribuzione del servizio di spostamento dei dati in Giappone orientale per eseguire la copia. Quando l'origine e la destinazione si trovano nella stessa area geografica e in tale area geografica non è disponibile un servizio di spostamento dei dati (come avviene attualmente in Australia), l'attività di copia avrà esito negativo anziché usare un'area geografica alternativa. Nota: il servizio di spostamento dei dati verrà esteso anche all'Australia. 

### <a name="moveonpremtocloud"></a>Spostamento sicuro dei dati tra un ambiente locale e il cloud
Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento uniforme di dati da ambienti locali al cloud e viceversa. Il Gateway di gestione dati è un agente che è possibile installare in locale per abilitare le pipeline di dati ibride.

Il Gateway di gestione dati offre le funzionalità seguenti:

1.	Consente di gestire in modo sicuro l'accesso ad archivi di dati locali.
2.	Consente di modellare gli archivi dati locali e nel cloud all'interno di un'unica istanza di Data factory e di spostare i dati al suo interno.
3.	Consente di monitorare e gestire lo stato del gateway in un'unica schermata attraverso un dashboard di Data factory basato sul cloud.

È necessario considerare l'origine dati come un'origine dati locale, ovvero come se fosse protetta da firewall, anche quando si usa **ExpressRoute** e **il gateway** per stabilire la connettività tra il servizio e l'origine dati.

Per altre informazioni, vedere [Spostare dati tra origini locali e il cloud mediante il Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

### Spostamento di dati affidabile e conveniente
L'attività di copia è progettata per spostare in modo affidabile volumi elevati di dati provenienti da una vasta gamma di origini dati, evitando gli errori temporanei. È possibile copiare i dati a un costo conveniente, con la possibilità di abilitare la compressione durante la connessione.

### Conversioni dei tipi tra diversi sistemi di tipi
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi sink mediante il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo sink nativo

Per trovare il mapping di un sistema di tipi nativo a .NET per uno specifico archivio dati, vedere gli argomenti relativi al connettore archivio dati corrispondente. È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.

### Uso di diversi formati di file
L'attività di copia supporta una vasta gamma di formati di file, inclusi file binari, file di testo e Avro per gli archivi basati su file. È possibile usare l'attività di copia per convertire i dati da un formato a un altro. Esempio: testo (CSV) ad Avro. Se i dati non sono strutturati, è possibile omettere la proprietà **Structure** nella definizione JSON del [set di dati](data-factory-create-datasets.md).

### Proprietà dell'attività di copia
Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via. D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività.

Quando viene eseguita un'attività di copia, nella sezione **typeProperties** vengono visualizzate proprietà diverse a seconda dei tipi di origine e sink. Ciascuna pagina relativa agli archivi dati elencati sopra contiene informazioni su queste proprietà, che variano in base al tipo di archivio dati.


### Prestazioni delle attività di copia e ottimizzazione 
Vedere l’articolo [Guida alle prestazione delle attività di copia e all’ottimizzazione](data-factory-copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Inoltre, vengono elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

<!---HONumber=AcomDC_0128_2016-->