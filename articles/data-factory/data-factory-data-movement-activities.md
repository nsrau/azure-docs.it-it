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
	ms.date="10/12/2015" 
	ms.author="spelluru"/>

# Attività di spostamento dei dati
Data factory include un [servizio disponibile a livello globale](#global) in grado di supportare lo spostamento di dati mediante [l'attività di copia](#copyactivity) tra i diversi archivi dati elencati di seguito. In Data factory è inoltre disponibile un supporto incorporato che consente di [spostare i dati in modo sicuro tra sedi locali e cloud](#moveonpremtocloud) mediante il Gateway di gestione dati.

Per un'esercitazione rapida sull'utilizzo dell'attività di copia, vedere [Esercitazione: utilizzo di attività Copia in una Pipeline di Factory di dati di Azure](data-factory-get-started.md). Nell'esercitazione si utilizzerà l'attività di copia per copiare dati da un'archiviazione BLOB di Azure a un database SQL di Azure. Nella sezione seguente sono elencate tutte le origini e i sink supportati dall'attività di copia.


## Archivi dati con supporto per l'attività di copia
Un'attività di copia consente di copiare i dati da un archivio dati **di origine** a un archivio dati **sink**. Data factory supporta gli archivi dati e le combinazioni origine/sink seguenti. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

| **Origine** | **Sink** |
| ------ | ---- |
| [BLOB di Azure](data-factory-azure-blob-connector.md) | BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Database, Server SQL locale, SQL Server in IaaS, Azure DocumentDB, File System locale, File System locale |
| [Tabella di Azure](data-factory-azure-table-connector.md) | BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS, Azure DocumentDB |
| [Database SQL di Azure](data-factory-azure-sql-connector.md) | BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS, Azure DocumentDB |
| [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) | BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS, Azure DocumentDB |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse |
| [SQL Server in IaaS](data-factory-sqlserver-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [File System locale](data-factory-onprem-file-system-connector.md) | BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS, File System locale, File System locale |
| [Server SQL locale](data-factory-sqlserver-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database Oracle in locale](data-factory-onprem-oracle-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database MySQL in locale](data-factory-onprem-mysql-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database DB2 in locale](data-factory-onprem-db2-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database Teradata in locale](data-factory-onprem-teradata-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database Sybase in locale](data-factory-onprem-sybase-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |
| [Database PostgreSQL in locale](data-factory-onprem-postgresql-connector.md) | BLOB di Azure, Tabella di Azure, Azure SQL Database, Azure SQL Data Warehouse, Server SQL locale, SQL Server in IaaS |

## <a name="copyactivity"></a>Attività di copia
L'attività di copia acquisisce un set di dati di input (**origine**) e copia i dati per ciascuna configurazione di attività in un set di dati di output (**sink**). La copia dei dati viene eseguita in modalità batch in base alla pianificazione specificata per l'attività.

> [AZURE.NOTE]Per informazioni sulla definizione generale di attività a un livello elevato, ad esempio per visualizzare alcune sezioni sul formato JSON e conoscere le proprietà disponibili per tutte le attività, vedere l'articolo relativo a [pipeline e attività](data-factory-create-pipelines.md).

L'attività di copia offre le funzionalità seguenti:

### <a name="global"></a>Spostamento di dati disponibile a livello globale
Il servizio di spostamento di dati alla base dell'attività di copia è disponibile a livello globale nelle aree geografiche seguenti. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e nella maggior parte dei casi consente di evitare passaggi tra diverse aree.

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

### <a name="moveonpremtocloud"></a>Spostamento sicuro di dati tra un ambiente locale e il cloud
Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento uniforme di dati da ambienti locali al cloud e viceversa. Il Gateway di gestione dati è un agente che è possibile installare in locale per abilitare le pipeline di dati ibride.

Il Gateway di gestione dati offre le funzionalità seguenti:

1.	Consente di gestire in modo sicuro l'accesso ad archivi di dati locali.
2.	Consente di modellare gli archivi dati locali e nel cloud all'interno di un'unica istanza di Data factory e di spostare i dati al suo interno.
3.	Consente di monitorare e gestire lo stato del gateway in un'unica schermata attraverso un dashboard di Data factory basato sul cloud.


Per altre informazioni, vedere l'argomento relativo allo [spostamento di dati tra un ambiente locale e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

### Spostamento di dati affidabile e conveniente
L'attività di copia è progettata per spostare in modo affidabile volumi elevati di dati provenienti da una vasta gamma di origini dati, resistendo in maniera efficace agli errori temporanei. È possibile copiare i dati a un costo conveniente, con la possibilità di abilitare la compressione durante la connessione.

### Conversioni dei tipi tra diversi sistemi di tipi
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi di sink mediante il metodo seguente diviso in due fasi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Per trovare il mapping di un sistema di tipi nativo a .NET per uno specifico archivio dati, vedere gli argomenti relativi al connettore archivio dati corrispondente. È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.

### Uso di diversi formati di file
Per le origini basate su file l'attività di copia supporta una vasta gamma di formati di file, inclusi il formato binario, il formato testo e il formato Avro. È possibile utilizzare l'attività di copia per convertire da un formato a altro. Esempio: testo (CSV) ad Avro.

### Proprietà dell'attività di copia
Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via. Al contrario, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività.

Quando viene eseguita un'attività di copia, nella sezione **typeProperties** vengono visualizzate proprietà diverse a seconda dei tipi di origini e sink. Ciascuna pagina relativa agli archivi dati elencati sopra contiene informazioni su queste proprietà, che variano in base al tipo di archivio dati.


## Invia commenti e suggerimenti
I commenti e i suggerimenti su questo articolo possono essere molto utili. L’invio di commenti e suggerimenti tramite [posta elettronica](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-movement-activities.md) richiede solo alcuni minuti.

<!---HONumber=Oct15_HO3-->