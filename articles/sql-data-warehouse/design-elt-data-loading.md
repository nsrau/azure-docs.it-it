---
title: Progettare ELT per Azure SQL Data Warehouse | Documenti Microsoft
description: Combinare le tecnologie per lo spostamento di dati in Azure e il caricamento dei dati in SQL Data Warehouse per la progettazione di un processo di estrazione, carico e trasformare (ELT) per Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/12/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: e94dca69c77c46034e318205279be5188e1371f5
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Progettazione di estrazione, trasformazione (ELT) per Azure SQL Data Warehouse e carico

Combinare le tecnologie per i dati di destinazione in archiviazione di Azure e il caricamento dei dati in SQL Data Warehouse per la progettazione di un processo di estrazione, carico e trasformare (ELT) per Azure SQL Data Warehouse. Questo articolo descrive le tecnologie che supportano il caricamento con Polybase e quindi si concentra sulla progettazione di un processo ELT usato da PolyBase con T-SQL per caricare dati in SQL Data Warehouse da archiviazione di Azure.

## <a name="what-is-elt"></a>Che cos'è ELT?

Estrarre, il carico, e trasformare (ELT) è un processo mediante il quale lo spostamento dei dati da un sistema di origine in un data warehouse di destinazione. Questo processo viene eseguito, ad esempio a intervalli regolari, ogni ora o ogni giorno, per ottenere appena generato dati nel data warehouse. La soluzione ideale per ottenere i dati dall'origine al data warehouse è per lo sviluppo di un processo ELT usato da PolyBase per caricare dati in SQL Data Warehouse.

ELT carica prima e quindi lo trasforma i dati, mentre l'estrazione, trasformazione e caricamento (ETL) Trasforma i dati prima del caricamento. Esecuzione ELT anziché ETL Salva il costo della fornitura di risorse per trasformare i dati prima del caricamento. Quando si utilizza SQL Data Warehouse, ELT sfrutta il sistema MPP per eseguire le trasformazioni.

Anche se esistono molte varianti per l'implementazione ELT per SQL Data Warehouse, ecco i passaggi di base:  

1. Estrarre i dati di origine in file di testo.
2. Inserite i dati in archiviazione Blob di Azure o archivio Azure Data Lake.
3. Preparare i dati per il caricamento.
2. Caricare i dati nelle tabelle di gestione temporanea tramite PolyBase di SQL Data Warehouse.
3. Trasformare i dati.
4. Inserire i dati nelle tabelle di produzione.


Per un'esercitazione di caricamento, vedere [usare PolyBase per caricare i dati dall'archiviazione blob di Azure per Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Per ulteriori informazioni, vedere [durante il caricamento di modelli blog](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opzioni per il caricamento con PolyBase

PolyBase è una tecnologia che accede ai dati all'esterno del database tramite il linguaggio T-SQL. È il modo migliore per caricare i dati in SQL Data Warehouse. Con PolyBase, i dati vengono caricati in parallelo dall'origine dati direttamente ai nodi di calcolo. 

Per caricare i dati con PolyBase, è possibile utilizzare una di queste opzioni di caricamento.

- [PolyBase con T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funziona bene quando i dati sono in archiviazione Blob di Azure o archivio Azure Data Lake. Offre il massimo controllo sul processo di caricamento, ma richiede anche di definire gli oggetti dati esterni. Gli altri metodi definiscono questi oggetti dietro le quinte, come si esegue il mapping di tabelle di origine alle tabelle di destinazione.  Per la gestione di carichi di T-SQL, è possibile utilizzare funzioni di Azure Data Factory di Azure o SSIS. 
- [PolyBase con SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md) funziona bene quando i dati di origine sono in SQL Server, SQL Server in locale o nel cloud. SSIS definisce l'origine per il mapping di tabelle di destinazione e gestisce inoltre il carico. Se si dispongono già di pacchetti SSIS, è possibile modificare i pacchetti da utilizzare con la nuova destinazione di data warehouse. 
- [PolyBase con Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) è un altro strumento di orchestrazione.  Definisce una pipeline e pianifica i processi. 

### <a name="polybase-external-file-formats"></a>Formati di file esterni di PolyBase

PolyBase consente di caricare dati da UTF-8 e file di testo delimitati con codificata UTF-16. Oltre ai file di testo delimitato, carica uno dei formati di file Hadoop File RC, ORC e Parquet. PolyBase può caricare i dati da Gzip e reattiva file compressi. PolyBase non supporta attualmente ASCII estesi, il formato a larghezza fissa e formati annidati, ad esempio WinZip, JSON e XML.

### <a name="non-polybase-loading-options"></a>Opzioni di caricamento non PolyBase
Se i dati non sono compatibili con PolyBase, è possibile utilizzare [bcp](sql-data-warehouse-load-with-bcp.md) o [API SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). bcp carica direttamente a SQL Data Warehouse senza dover passare attraverso l'archiviazione Blob di Azure e destinata solo a carichi piccoli. Si noti che le prestazioni del carico di queste opzioni sono molto più lenta rispetto a PolyBase. 


## <a name="extract-source-data"></a>Estrarre i dati di origine

Recupero di dati dal sistema di origine dipende dall'origine.  L'obiettivo consiste nello spostare i dati in file di testo delimitato da virgole. Se si utilizza SQL Server, è possibile utilizzare [strumento da riga di comando bcp](/sql/tools/bcp-utility) per esportare i dati.  

## <a name="land-data-to-azure-storage"></a>Terreni dati nell'archiviazione di Azure

Per inserite i dati in archiviazione di Azure, è possibile spostare [archiviazione Blob di Azure](../storage/blobs/storage-blobs-introduction.md) o [archivio Azure Data Lake](../data-lake-store/data-lake-store-overview.md). In questa posizione, i dati devono essere archiviati in file di testo. Polybase è possibile caricare da un'altra posizione.

Si tratta di strumenti e servizi, che è possibile utilizzare per spostare i dati in archiviazione di Azure.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) servizio migliora la velocità effettiva della rete, prestazioni e prevedibilità. ExpressRoute è un servizio che indirizza i dati tramite una connessione privata dedicata a Azure. Le connessioni ExpressRoute non inviare dati tramite la rete internet pubblica. Le connessioni offrono ulteriori affidabilità, velocità più elevate, minori latenze e una maggiore sicurezza rispetto alle connessioni sulla rete internet pubblica.
- [Utilità AZCopy](../storage/common/storage-use-azcopy.md) Sposta i dati in archiviazione di Azure sulla rete internet pubblica. Questo procedimento funziona se le dimensioni dei dati sono meno di 10 TB. Per eseguire caricamenti a intervalli regolari con AZCopy, verificare la velocità di rete per vedere se è accettabile. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) ha un gateway che è possibile installare nel server locale. È quindi possibile creare una pipeline per spostare i dati dal server locale a un massimo di archiviazione di Azure.

Per ulteriori informazioni, vedere [lo spostamento dei dati da e verso l'archiviazione di Azure](../storage/common/storage-moving-data.md)


## <a name="prepare-data"></a>Preparazione dei dati

Potrebbe essere necessario preparare e pulire i dati nell'account di archiviazione prima di caricarli in SQL Data Warehouse. Preparazione dei dati può essere eseguita mentre i dati nell'origine, come esportare i dati in file di testo o dopo i dati in archiviazione di Azure.  È più facile lavorare con i dati di quanto prima durante il processo di possibili.  

### <a name="define-external-tables"></a>Definire le tabelle esterne
Prima di caricare i dati, è necessario definire le tabelle esterne nel data warehouse. PolyBase utilizza le tabelle esterne per definire e accedere ai dati in archiviazione di Azure. Nella tabella esterna è simile a una normale tabella. La differenza principale è la tabella esterna e i dati archiviati all'esterno del data warehouse. 

Definizione di tabelle esterne, è necessario specificare l'origine dati, il formato del file di testo e le definizioni di tabella. Questi sono gli argomenti della sintassi T-SQL che è necessario:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Per un esempio di creazione di oggetti esterni, vedere il [creare tabelle esterne](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) passaggio dell'esercitazione di caricamento.

### <a name="format-text-files"></a>File in formato testo

Una volta definiti gli oggetti esterni, è necessario allineare le righe dei file di testo con la tabella esterna e la definizione del formato del file. I dati in ogni riga del file di testo devono essere allineati con la definizione della tabella.

Per formattare i file di testo:

- Se i dati provenienti da un'origine non relazionali, è necessario trasformare in righe e colonne. Se i dati provengono da un'origine relazionale o non relazionali, i dati devono essere trasformati per allineamento con le definizioni di colonna per la tabella in cui si prevede di caricare i dati. 
- Formattare i dati nel file di testo per la compatibilità con i tipi di dati e di colonne nella tabella di destinazione di SQL Data Warehouse. Problemi di allineamento tra i tipi di dati nei file di testo esterno e la tabella di data warehouse fa sì che le righe vengano rifiutate durante il caricamento.
- Campi separati nel file di testo con un carattere di terminazione.  Assicurarsi di utilizzare un carattere o una sequenza di caratteri che non viene trovata nei dati di origine. Utilizzare il carattere di terminazione è specificato con [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Eseguire il caricamento in una tabella di staging
Per ottenere i dati nel data warehouse, funziona anche al primo caricamento dei dati in una tabella di gestione temporanea. Utilizzando una tabella di gestione temporanea, è possibile gestire gli errori senza interferire con le tabelle di produzione e non eseguire operazioni di rollback per la tabella di produzione. Tabella di gestione temporanea consente inoltre l'opportunità di utilizzare SQL Data Warehouse per eseguire trasformazioni prima di inserire i dati nelle tabelle di produzione.

Per caricare con T-SQL, eseguire il [Crea tabella AS selezionare (un'istruzione CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) istruzione T-SQL. Questo comando consente di inserire i risultati di un'istruzione select in una nuova tabella. Quando l'istruzione seleziona da una tabella esterna, l'importazione dei dati esterni. 

Nell'esempio seguente, ext. Data è una tabella esterna. Tutte le righe vengono importate in una nuova tabella denominata dbo. Data.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Trasformare i dati
Mentre i dati sono nella tabella di gestione temporanea, eseguire le trasformazioni che richiede il carico di lavoro. Spostare quindi i dati in una tabella di produzione.

## <a name="insert-data-into-production-table"></a>Inserire dati in una tabella di produzione

L'istruzione INSERT INTO... Istruzione SELECT Sposta i dati dalla tabella di gestione temporanea per la tabella permanente. 

Quando si progetta un processo ETL, provare a eseguire il processo su un campione di test di piccole dimensioni. Provare l'estrazione di 1000 righe dalla tabella in un file, spostarlo in Azure e ripetere l'operazione di caricamento in una tabella di gestione temporanea. 

## <a name="partner-loading-solutions"></a>Soluzioni di caricamento dei partner
Molti partner Microsoft dispongono di soluzioni di caricamento. Per ulteriori dettagli, vedere un elenco dei nostri [partner delle soluzioni](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Passaggi successivi
Per il caricamento di informazioni aggiuntive, vedere [linee guida per caricare i dati](guidance-for-loading-data.md).


