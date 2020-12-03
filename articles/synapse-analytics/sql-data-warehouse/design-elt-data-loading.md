---
title: Progettare un processo ELT anziché ETL
description: Implementare strategie di caricamento dei dati flessibili per i pool SQL dedicati in Azure sinapsi Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 64ba24eb0eab581310122908fc05d1d671ac1d40
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531574"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Strategie di caricamento dei dati per il pool SQL dedicato in Azure sinapsi Analytics

I pool SQL dedicati a SMP tradizionali usano un processo di estrazione, trasformazione e caricamento (ETL) per il caricamento dei dati. Sinapsi SQL, all'interno di Azure sinapsi Analytics, usa l'architettura di elaborazione delle query distribuite che sfrutta la scalabilità e la flessibilità delle risorse di calcolo e di archiviazione.

L'uso di un processo di estrazione, caricamento e trasformazione (ELT) sfrutta le funzionalità di elaborazione delle query distribuite predefinite ed Elimina le risorse necessarie per la trasformazione dei dati prima del caricamento.

Sebbene i pool SQL dedicati supportino molti metodi di caricamento, tra cui le opzioni di SQL Server più diffuse, ad esempio [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e l' [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), il modo più rapido e scalabile per caricare i dati è tramite le tabelle esterne di base e l' [istruzione Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Con PolyBase e l'istruzione COPY, è possibile accedere ai dati archiviati esterni in Archiviazione BLOB di Azure o Azure Data Lake Store tramite il linguaggio T-SQL. Per la massima flessibilità durante il caricamento, è consigliabile usare l'istruzione COPY.


## <a name="what-is-elt"></a>Definizione di ELT

Estrazione, caricamento e trasformazione (ELT) è un processo mediante il quale i dati vengono estratti da un sistema di origine, caricati in un pool SQL dedicato e quindi trasformati.

I passaggi di base per l'implementazione del processo ELT sono:

1. Estrarre i dati di origine in file di testo.
2. Trasferire i dati nell'archivio BLOB di Azure o in Azure Data Lake Store.
3. Preparare i dati per il caricamento.
4. Caricare i dati nelle tabelle di staging con PolyBase o il comando COPY.
5. Trasformare i dati.
6. Inserire i dati in tabelle di produzione.

Per un'esercitazione sul caricamento, vedere [caricamento di dati da un archivio BLOB di Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Estrarre i dati di origine in file di testo

La modalità di recupero dei dati dal sistema di origine dipende dalla posizione di archiviazione. L'obiettivo è spostare i dati in file di testo o CSV delimitati supportati.

### <a name="supported-file-formats"></a>Formati di file supportati

Con PolyBase e l'istruzione COPY, è possibile caricare dati da file di testo o CSV delimitati con codifica UTF-8 e UTF-16. Oltre ai file di testo o CSV delimitati, è supportato il caricamento da formati di file Hadoop come ORC e Parquet. PolyBase e l'istruzione COPY possono anche caricare dati da file compressi Gzip e Snappy.

I formati ASCII esteso, a larghezza fissa e annidati, come WinZip o XML, non sono attualmente supportati. Se si esegue l'esportazione da SQL Server, è possibile usare lo [strumento da riga di comando bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per esportare i dati in file di testo delimitati.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Trasferire i dati in Archiviazione BLOB di Azure o in Azure Data Lake Store

Per trasferire i dati in Archiviazione di Azure, è possibile spostarli nell'[archivio BLOB di Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o in [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In entrambe le posizioni, i dati devono essere archiviati in file di testo. PolyBase e l'istruzione COPY supportano il caricamento da entrambe le posizioni.

Strumenti e servizi che è possibile usare per spostare i dati in Archiviazione di Azure:

- Il servizio [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) migliora la velocità effettiva della rete, le prestazioni e la prevedibilità. ExpressRoute è un servizio che instrada i dati tramite una connessione privata dedicata ad Azure. Le connessioni ExpressRoute non instradano i dati attraverso la rete Internet pubblica. Queste connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle tipiche connessioni tramite la rete Internet pubblica.
- L'[utilità AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sposta i dati in Archiviazione di Azure tramite la rete Internet pubblica. Si tratta di un'opzione appropriata se le dimensioni dei dati sono inferiori a 10 TB. Per eseguire regolarmente caricamenti con AZCopy, assicurasi che la velocità di rete sia accettabile.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) include un gateway che è possibile installare nel server locale. È quindi possibile creare una pipeline per spostare i dati dal server locale ad Archiviazione di Azure. Per usare Data Factory con i pool SQL dedicati, vedere [caricamento di dati per pool SQL dedicati](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparare i dati per il caricamento

Potrebbe essere necessario preparare e pulire i dati nell'account di archiviazione prima del caricamento. La preparazione dei dati può essere eseguita nella posizione di origine dei dati, mentre si esportano i dati in file di testo o quando i dati raggiungono Archiviazione di Azure.  È più facile lavorare con i dati il prima possibile nel processo.  

### <a name="define-the-tables"></a>Definire le tabelle

Quando si usa l'istruzione COPY, è necessario innanzitutto definire le tabelle in cui si esegue il caricamento nel pool SQL dedicato.

Se si usa la polibase, è necessario definire le tabelle esterne nel pool SQL dedicato prima del caricamento. PolyBase usa le tabelle esterne per definire i dati e accedervi in Archiviazione di Azure. Una tabella esterna è simile a una vista di database. La tabella esterna contiene lo schema della tabella e punta ai dati archiviati all'esterno del pool SQL dedicato.

La definizione di tabelle esterne include la specifica dell'origine dati, del formato dei file di testo e delle definizioni delle tabelle. Gli articoli di riferimento per la sintassi T-SQL che saranno necessari sono:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Usare il mapping dei tipi di dati SQL seguente durante il caricamento di file parquet:

|                         Tipo Parquet                         |   Tipo logico Parquet (annotazione)   |  Tipo di dati SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      float       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       INT        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      BINARY      |
|                            BINARY                            |                 UTF8                  |     NVARCHAR     |
|                            BINARY                            |                STRING                 |     NVARCHAR     |
|                            BINARY                            |                 ENUM                  |     NVARCHAR     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     SMALLINT     |
|                            INT32                             |            INT(16, true)            |     SMALLINT     |
|                            INT32                             |             INT(32, true)             |       INT        |
|                            INT32                             |            INT(8, false)            |     TINYINT      |
|                            INT32                             |            INT(16, false)             |       INT        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       Data       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false)            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       time       |
|                            INT64                             | TIMESTAMP (MILLIS)                  |    datetime2     |
| [Tipo complesso](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 INSERZIONE                  |   ntext   |
| [Tipo complesso](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   ntext   |

>[!IMPORTANT] 
>- I pool dedicati SQL attualmente non supportano i tipi di dati parquet con la precisione di micro e NANO. 
>- È possibile che si verifichi l'errore seguente se i tipi non corrispondono tra parquet e SQL o se sono presenti tipi di dati parquet non supportati: **"HdfsBridge:: recordReaderFillBuffer-errore imprevisto durante il caricamento del buffer del lettore di record: ClassCastException:..."**
>- Il caricamento di un valore non compreso nell'intervallo 0-127 in una colonna tinyint per il formato di file parquet e ORC non è supportato.

Per un esempio di creazione di oggetti esterni, vedere [creare tabelle esterne](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Formattare i file di testo

Se si usa PolyBase, per gli oggetti esterni definiti è necessario allineare le righe dei file di testo alla definizione della tabella esterna e del formato del file. I dati in ogni riga del file di testo devono essere allineati alla definizione della tabella.
Per formattare i file di testo:

- Se i dati provengono da un'origine non relazionale, è necessario trasformarli in righe e colonne. Sia che i dati provengano da un'origine relazionale o non relazionale, devono essere trasformati per allinearli alle definizioni di colonna per la tabella in cui si prevede di caricare i dati.
- Formattare i dati nel file di testo per allinearli alle colonne e ai tipi di dati nella tabella di destinazione. Il mancato allineamento tra i tipi di dati nei file di testo esterni e nella tabella del pool SQL dedicata comporta il rifiuto delle righe durante il caricamento.
- Separare i campi nel file di testo con un carattere di terminazione.  Assicurarsi di usare un carattere o una sequenza di caratteri non inclusi nei dati di origine. Usare il carattere di terminazione specificato con [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Caricare i dati usando PolyBase o l'istruzione COPY

È consigliabile caricare i dati in una tabella di staging. Le tabelle di staging consentono di gestire gli errori senza interferire con le tabelle di produzione. Una tabella di staging offre inoltre la possibilità di usare l'architettura di elaborazione parallela del pool SQL dedicata per le trasformazioni dei dati prima di inserire i dati nelle tabelle di produzione.

### <a name="options-for-loading"></a>Opzioni per il caricamento

Per caricare i dati, è possibile usare una delle opzioni di caricamento seguenti:

- L' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) è l'utilità di caricamento consigliata, in quanto consente di caricare facilmente e in modo flessibile i dati. L'istruzione dispone di molte funzionalità di caricamento aggiuntive non disponibili in polibase. 
- Per [la polibase con T-SQL](load-data-from-azure-blob-storage-using-polybase.md) è necessario definire oggetti dati esterni.
- [PolyBase e istruzione COPY con Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) è un altro strumento di orchestrazione,  che definisce una pipeline e pianifica i processi.
- La funzione [polibase con SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funziona bene quando i dati di origine sono in SQL Server. SSIS definisce i mapping delle tabelle di origine e di destinazione, oltre a orchestrare il caricamento. Se sono già disponibili pacchetti SSIS, è possibile modificarli per utilizzare la nuova destinazione di data warehouse.
- [PolyBase con Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) trasferisce i dati da una tabella in un dataframe di Databricks e/o scrive i dati da un dataframe di Databricks in una tabella usando PolyBase.

### <a name="other-loading-options"></a>Altre opzioni di caricamento

Oltre a PolyBase e all'istruzione COPY, è possibile usare [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) o l'[API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). bcp carica direttamente i dati nel database senza dover passare attraverso l'archivio BLOB di Azure ed è destinato esclusivamente a piccoli caricamenti.

> [!NOTE]
> Le prestazioni di caricamento di queste opzioni sono inferiori rispetto a PolyBase e all'istruzione COPY.

## <a name="5-transform-the-data"></a>5. Trasformare i dati

Mentre i dati sono nella tabella di staging, eseguire le trasformazioni richieste dal carico di lavoro, quindi spostare i dati in una tabella di produzione.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserire i dati in tabelle di produzione

L'istruzione INSERT INTO... SELECT sposta i dati dalla tabella di staging alla tabella permanente.

Quando si progetta un processo ETL, provare a eseguire il processo su un campione di test di piccole dimensioni. Provare a estrarre 1000 righe dalla tabella in un file, spostarlo in Azure e quindi provare a caricarlo in una tabella di staging.

## <a name="partner-loading-solutions"></a>Soluzioni di caricamento dei partner

Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere l'elenco dei [partner che offrono soluzioni](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Passaggi successivi

Per linee guida relative al caricamento, vedere [Linee guida per il caricamento di dati ](guidance-for-loading-data.md).
