---
title: Anziché ETL, progettare ELT
description: Implementare strategie flessibili di caricamento dei dati per il pool SQL sinapsi in Azure sinapsi Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e99fd898956e11a4827d023691111a47e5a790c0
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744965"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie di caricamento dei dati per il pool SQL sinapsi

I pool SQL SMP tradizionali usano un processo di estrazione, trasformazione e caricamento (ETL) per il caricamento dei dati. Il pool SQL sinapsi, all'interno di Azure sinapsi Analytics, dispone di un'architettura MPP (Massive Parallel Processing) che sfrutta la scalabilità e la flessibilità delle risorse di calcolo e di archiviazione.

L'uso di un processo di estrazione, caricamento e trasformazione (ELT) sfrutta MPP ed Elimina le risorse necessarie per la trasformazione dei dati prima del caricamento.

Sebbene il pool SQL supporti molti metodi di caricamento, tra cui le opzioni di SQL Server più diffuse, ad esempio [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e l' [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), il modo più rapido e scalabile per caricare i dati è tramite le tabelle esterne di base e l' [istruzione Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (anteprima).

Con la polibase e l'istruzione COPY è possibile accedere ai dati esterni archiviati nell'archivio BLOB di Azure o Azure Data Lake Store tramite il linguaggio T-SQL. Per maggiore flessibilità durante il caricamento, è consigliabile usare l'istruzione COPY.

> [!NOTE]  
> L'istruzione COPY è attualmente disponibile in versione di anteprima pubblica. Per fornire commenti e suggerimenti, inviare un messaggio di posta elettronica sqldwcopypreview@service.microsoft.comalla seguente lista di distribuzione:.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Definizione di ELT

Estrazione, caricamento e trasformazione (ELT) è un processo mediante il quale i dati vengono estratti da un sistema di origine, caricati in un pool SQL e quindi trasformati.

I passaggi di base per l'implementazione di ELT sono:

1. Estrarre i dati di origine in file di testo.
2. Trasferire i dati nell'archivio BLOB di Azure o in Azure Data Lake Store.
3. Preparare i dati per il caricamento.
4. Caricare i dati nelle tabelle di staging con il comando di copia o di base.
5. Trasformare i dati.
6. Inserire i dati in tabelle di produzione.

Per un'esercitazione sul caricamento di base, vedere [usare la polibase per caricare dati dall'archivio BLOB di Azure](load-data-from-azure-blob-storage-using-polybase.md).

Per altre informazioni, vedere il [blog sui modelli di caricamento](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Estrarre i dati di origine in file di testo

La modalità di recupero dei dati dal sistema di origine dipende dalla posizione di archiviazione.  L'obiettivo consiste nello spostare i dati in polibase e nei file di testo o CSV delimitati da copia supportati.

### <a name="polybase-and-copy-external-file-formats"></a>Database e copia formati di file esterni

Con la polibase e l'istruzione COPY è possibile caricare i dati da file di testo o CSV con codifica UTF-8 e UTF-16. Oltre ai file di testo o CSV delimitati, viene caricato dai formati di file Hadoop, ad esempio ORC e parquet. La polibase e l'istruzione COPY possono inoltre caricare dati da file compressi gzip e snapy.

Non sono supportati formati ASCII estesi, formato a larghezza fissa e annidati, ad esempio WinZip o XML. Se si sta esportando da SQL Server, è possibile usare lo [strumento da riga di comando bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per esportare i dati in file di testo delimitati.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Inserire i dati nell'archivio BLOB di Azure o Azure Data Lake Store

Per inserire i dati in archiviazione di Azure, è possibile spostarli nell' [Archivio BLOB di Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o in [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In entrambe le posizioni, i dati devono essere archiviati in file di testo. La polibase e l'istruzione COPY possono essere caricate da entrambe le posizioni.

Strumenti e servizi che è possibile usare per spostare i dati in Archiviazione di Azure:

- Il servizio [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) migliora la velocità effettiva della rete, le prestazioni e la prevedibilità. ExpressRoute è un servizio che instrada i dati tramite una connessione privata dedicata ad Azure. Le connessioni ExpressRoute non instradano i dati attraverso la rete Internet pubblica. Queste connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle tipiche connessioni tramite la rete Internet pubblica.
- L'[utilità AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sposta i dati in Archiviazione di Azure tramite la rete Internet pubblica. Si tratta di un'opzione appropriata se le dimensioni dei dati sono inferiori a 10 TB. Per eseguire regolarmente caricamenti con AZCopy, assicurasi che la velocità di rete sia accettabile.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) include un gateway che è possibile installare nel server locale. È quindi possibile creare una pipeline per spostare i dati dal server locale ad Archiviazione di Azure. Per usare Data Factory con analisi SQL, vedere [caricamento di dati per l'analisi SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. preparare i dati per il caricamento

Potrebbe essere necessario preparare e pulire i dati nell'account di archiviazione prima del caricamento. La preparazione dei dati può essere eseguita nella posizione di origine dei dati, mentre si esportano i dati in file di testo o quando i dati raggiungono Archiviazione di Azure.  È più facile lavorare con i dati il prima possibile nel processo.  

### <a name="define-external-tables"></a>Definire tabelle esterne

Se si usa la polibase, è necessario definire le tabelle esterne nel pool SQL prima del caricamento. Le tabelle esterne non sono richieste dall'istruzione COPY. PolyBase usa le tabelle esterne per definire i dati e accedervi in Archiviazione di Azure.

Una tabella esterna è simile a una vista di database. La tabella esterna contiene lo schema della tabella e punta ai dati archiviati all'esterno del pool SQL.

La definizione di tabelle esterne include la specifica dell'origine dati, del formato dei file di testo e delle definizioni delle tabelle. Gli articoli di riferimento per la sintassi T-SQL che saranno necessari sono:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Quando si carica parquet, il mapping dei tipi di dati SQL è:

| **Tipo di dati parquet** | **Tipo di dati SQL** |
| :-------------------: | :---------------: |
|        TINYINT        |      TINYINT      |
|       SMALLINT        |     SMALLINT      |
|          INT          |        INT        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       money       |
|        double         |    SMALLMONEY     |
|        string         |       NCHAR       |
|        string         |     NVARCHAR      |
|        string         |       char        |
|        string         |      varchar      |
|        BINARY         |      BINARY       |
|        BINARY         |     varbinary     |
|       timestamp       |       Data        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     Datetime      |
|       timestamp       |       time        |
|         Data          |       Data        |
|        decimal        |      decimal      |

Per un esempio di creazione di oggetti esterni, vedere il passaggio [Creare una tabella esterna](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) nell'esercitazione per il caricamento.

### <a name="format-text-files"></a>Formattare i file di testo

Se si usa la polibase, gli oggetti esterni definiti devono allineare le righe dei file di testo con la definizione della tabella esterna e del formato di file. I dati in ogni riga del file di testo devono essere allineati alla definizione della tabella.
Per formattare i file di testo:

- Se i dati provengono da un'origine non relazionale, è necessario trasformarli in righe e colonne. Sia che i dati provengano da un'origine relazionale o non relazionale, devono essere trasformati per allinearli alle definizioni di colonna per la tabella in cui si prevede di caricare i dati.
- Formattare i dati nel file di testo per allinearli con le colonne e i tipi di dati nella tabella di destinazione. Il mancato allineamento tra i tipi di dati nei file di testo esterni e nella tabella del pool SQL causa il rifiuto delle righe durante il caricamento.
- Separare i campi nel file di testo con un carattere di terminazione.  Assicurarsi di usare un carattere o una sequenza di caratteri non trovata nei dati di origine. Usare il carattere di terminazione specificato con [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. caricare i dati utilizzando l'istruzione di copia o di base

È consigliabile caricare i dati in una tabella di staging. Le tabelle di staging consentono di gestire gli errori senza interferire con le tabelle di produzione. Una tabella di staging offre inoltre la possibilità di usare l'MPP del pool SQL per le trasformazioni dei dati prima di inserire i dati nelle tabelle di produzione.

La tabella deve essere creata in precedenza durante il caricamento in una tabella di staging con COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opzioni per il caricamento con l'istruzione di copia e di base

Per caricare i dati con PolyBase, è possibile usare una di queste opzioni di caricamento:

- [PolyBase con T-SQL](load-data-from-azure-blob-storage-using-polybase.md): ideale quando i dati sono nell'archivio BLOB di Azure o in Azure Data Lake Store. Questa opzione offre il massimo controllo sul processo di caricamento, ma richiede anche di definire oggetti dati esterni. Gli altri metodi definiscono questi oggetti dietro le quinte, man mano che si esegue il mapping di tabelle di origine e tabelle di destinazione.  Per orchestrare i caricamenti con T-SQL, è possibile usare Azure Data Factory, SSIS o funzioni di Azure.
- [PolyBase con SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest): ideale quando i dati di origine sono in SQL Server, in locale o nel cloud. SSIS definisce i mapping delle tabelle di origine e di destinazione, oltre a orchestrare il caricamento. Se sono già disponibili pacchetti SSIS, è possibile modificarli per utilizzare la nuova destinazione di data warehouse.
- L' [istruzione di base e di copia con Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) è un altro strumento di orchestrazione.  che definisce una pipeline e pianifica i processi.
- La [polibase con Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) trasferisce i dati da una tabella a un frame di dati databricks e/o scrive i dati da un dataframe di databricks in una tabella usando la polibase.

### <a name="other-loading-options"></a>Altre opzioni di caricamento

Oltre a polibase e all'istruzione COPY, è possibile usare [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) o l' [API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). BCP viene caricato direttamente nel database senza passare attraverso l'archiviazione BLOB di Azure ed è destinato solo a piccoli carichi.

> [!NOTE]
> Le prestazioni di caricamento di queste opzioni sono più lente rispetto a polibase e all'istruzione COPY.

## <a name="5-transform-the-data"></a>5. trasformare i dati

Mentre i dati sono nella tabella di staging, eseguire le trasformazioni richieste dal carico di lavoro, quindi spostare i dati in una tabella di produzione.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserire i dati nelle tabelle di produzione

INSERT INTO... L'istruzione SELECT consente di spostare i dati dalla tabella di staging alla tabella permanente.

Quando si progetta un processo ETL, provare a eseguire il processo su un campione di test di piccole dimensioni. Provare a estrarre 1000 righe dalla tabella in un file, spostarlo in Azure e quindi provare a caricarlo in una tabella di staging.

## <a name="partner-loading-solutions"></a>Soluzioni di caricamento dei partner

Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere l'elenco dei [partner che offrono soluzioni](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Passaggi successivi

Per linee guida relative al caricamento, vedere [Linee guida per il caricamento di dati ](guidance-for-loading-data.md).
