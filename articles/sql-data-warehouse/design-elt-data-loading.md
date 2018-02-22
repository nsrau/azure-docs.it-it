---
title: Progettazione di un processo ELT per Azure SQL Data Warehouse | Microsoft Docs
description: Combinare le tecnologie per lo spostamento di dati in Azure e il caricamento dei dati in SQL Data Warehouse per progettare un processo di estrazione, caricamento e trasformazione (ELT, Extract, Load, Transform) per Azure SQL Data Warehouse.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Progettazione ELT (Extract, Load, Transform) per Azure SQL Data Warehouse

Combinare le tecnologie per il trasferimento di dati in un archivio di Azure e il caricamento dei dati in SQL Data Warehouse per progettare un processo di estrazione, caricamento e trasformazione (ELT, Extract, Load, Transform) per Azure SQL Data Warehouse. Questo articolo presenta le tecnologie che supportano il caricamento con PolyBase e quindi si concentra sulla progettazione di un processo ELT che usa PolyBase con T-SQL per caricare dati in SQL Data Warehouse da Archiviazione di Azure.

## <a name="what-is-elt"></a>Definizione di ELT

ELT (Extract, Load, Transform) è un processo mediante il quale i dati vengono spostati da un sistema di origine a un data warehouse di destinazione. Questo processo viene eseguito a intervalli regolari, ad esempio ogni ora o quotidianamente, per trasferire i nuovi dati generati nel data warehouse. La soluzione ideale per trasferire i dati dall'origine al data warehouse è lo sviluppo di un processo ELT usato da PolyBase per caricare dati in SQL Data Warehouse.

ELT carica prima di tutto i dati e poi li trasforma, mentre un processo ETL (Extract, Transform, Load) trasforma i dati prima del caricamento. L'esecuzione di un processo ELT invece di ETL consente di risparmiare i costi per fornire risorse proprie per la trasformazione dei dati prima del caricamento. Quando si usa SQL Data Warehouse, ELT sfrutta il sistema MPP per eseguire le trasformazioni.

Anche se esistono molte varianti per l'implementazione del processo ELT per SQL Data Warehouse, questi sono i passaggi di base:  

1. Estrarre i dati di origine in file di testo.
2. Trasferire i dati nell'archivio BLOB di Azure o in Azure Data Lake Store.
3. Preparare i dati per il caricamento.
2. Caricare i dati in tabelle di staging di SQL Data Warehouse usando PolyBase.
3. Trasformare i dati.
4. Inserire i dati in tabelle di produzione.


Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Per altre informazioni, vedere il [blog sui modelli di caricamento](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opzioni per il caricamento con PolyBase

PolyBase è una tecnologia che accede a dati esterni al database tramite il linguaggio T-SQL e rappresenta il modo ottimale per caricare dati in SQL Data Warehouse. Con PolyBase, i dati vengono caricati in parallelo dall'origine dati direttamente nei nodi di calcolo. 

Per caricare i dati con PolyBase, è possibile usare una di queste opzioni di caricamento.

- [PolyBase con T-SQL](load-data-from-azure-blob-storage-using-polybase.md): ideale quando i dati sono nell'archivio BLOB di Azure o in Azure Data Lake Store. Questa opzione offre il massimo controllo sul processo di caricamento, ma richiede anche di definire oggetti dati esterni. Gli altri metodi definiscono questi oggetti dietro le quinte, man mano che si esegue il mapping di tabelle di origine e tabelle di destinazione.  Per orchestrare i caricamenti con T-SQL, è possibile usare Azure Data Factory, SSIS o funzioni di Azure. 
- [PolyBase con SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md): ideale quando i dati di origine sono in SQL Server, in locale o nel cloud. SSIS definisce i mapping delle tabelle di origine e di destinazione, oltre a orchestrare il caricamento. Se sono già disponibili pacchetti SSIS, è possibile modificarli per utilizzare la nuova destinazione di data warehouse. 
- [PolyBase con Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) è un altro strumento di orchestrazione,  che definisce una pipeline e pianifica i processi. 

### <a name="polybase-external-file-formats"></a>Formati di file esterni PolyBase

PolyBase carica i dati da file di testo delimitati con codifica UTF-8 e UTF-16. Oltre ai file di testo delimitati, supporta il caricamento da formati di file Hadoop, ovvero RC, ORC e Parquet. PolyBase può caricare dati da file compressi Gzip e Snappy. PolyBase non supporta attualmente i formati ASCII esteso, a larghezza fissa e annidati, come WinZip, JSON e XML.

### <a name="non-polybase-loading-options"></a>Opzioni di caricamento non PolyBase
Se i dati non sono compatibili con PolyBase, è possibile usare [bcp](sql-data-warehouse-load-with-bcp.md) o l'[API SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). bcp carica direttamente i dati in SQL Data Warehouse senza dover passare attraverso l'archivio BLOB di Azure ed è destinato esclusivamente a piccoli caricamenti. Si noti che le prestazioni di caricamento di queste opzioni sono notevolmente inferiori rispetto a PolyBase. 


## <a name="extract-source-data"></a>Estrarre i dati di origine

Come recuperare i dati dal sistema di origine dipende dall'origine.  L'obiettivo è spostare i dati in file di testo delimitati. Nel caso di SQL Server è possibile usare lo [strumento da riga di comando bcp](/sql/tools/bcp-utility) per esportare i dati.  

## <a name="land-data-to-azure-storage"></a>Trasferire i dati in Archiviazione di Azure

Per trasferire i dati in Archiviazione di Azure, è possibile spostarli nell'[archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md) o in [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). In entrambe le posizioni, i dati devono essere archiviati in file di testo. PolyBase può eseguire il caricamento da entrambe le posizioni.

Questi sono gli strumenti e i servizi che è possibile usare per spostare i dati in Archiviazione di Azure.

- Il servizio [Azure ExpressRoute](../expressroute/expressroute-introduction.md) migliora la velocità effettiva della rete, le prestazioni e la prevedibilità. ExpressRoute è un servizio che instrada i dati tramite una connessione privata dedicata ad Azure. Le connessioni ExpressRoute non instradano i dati attraverso la rete Internet pubblica. Queste connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle tipiche connessioni tramite la rete Internet pubblica.
- L'[utilità AZCopy](../storage/common/storage-use-azcopy.md) sposta i dati in Archiviazione di Azure tramite la rete Internet pubblica. Si tratta di un'opzione appropriata se le dimensioni dei dati sono inferiori a 10 TB. Per eseguire regolarmente caricamenti con AZCopy, assicurasi che la velocità di rete sia accettabile. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) include un gateway che è possibile installare nel server locale. È quindi possibile creare una pipeline per spostare i dati dal server locale ad Archiviazione di Azure.

Per altre informazioni, vedere [Spostamento dei dati da e verso Archiviazione di Azure](../storage/common/storage-moving-data.md).


## <a name="prepare-data"></a>Preparazione dei dati

Potrebbe essere necessario preparare e pulire i dati nell'account di archiviazione prima di caricarli in SQL Data Warehouse. La preparazione dei dati può essere eseguita nella posizione di origine dei dati, mentre si esportano i dati in file di testo o quando i dati raggiungono Archiviazione di Azure.  È più facile lavorare con i dati il prima possibile nel processo.  

### <a name="define-external-tables"></a>Definire tabelle esterne
Prima di caricare i dati, è necessario definire le tabelle esterne nel data warehouse. PolyBase usa le tabelle esterne per definire i dati e accedervi in Archiviazione di Azure. La tabella esterna è simile a una normale tabella. La differenza principale è che la tabella esterna punta a dati archiviati all'esterno del data warehouse. 

La definizione di tabelle esterne include la specifica dell'origine dati, del formato dei file di testo e delle definizioni delle tabelle. Questi sono gli argomenti della sintassi T-SQL a cui fare riferimento:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Per un esempio di creazione di oggetti esterni, vedere il passaggio [Creare una tabella esterna](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) nell'esercitazione per il caricamento.

### <a name="format-text-files"></a>Formattare i file di testo

Dopo aver definito gli oggetti esterni, è necessario allineare le righe dei file di testo alla definizione della tabella esterna e del formato del file. I dati in ogni riga del file di testo devono essere allineati alla definizione della tabella.

Per formattare i file di testo:

- Se i dati provengono da un'origine non relazionale, è necessario trasformarli in righe e colonne. Sia che i dati provengano da un'origine relazionale o non relazionale, devono essere trasformati per allinearli alle definizioni di colonna per la tabella in cui si prevede di caricare i dati. 
- Formattare i dati nel file di testo per allinearli alle colonne e ai tipi di dati nella tabella di destinazione di SQL Data Warehouse. In caso di non allineamento dei tipi di dati nei file di testo esterni e nella tabella del data warehouse, le righe verranno rifiutate durante il caricamento.
- Separare i campi nel file di testo con un carattere di terminazione.  Assicurarsi di usare un carattere o una sequenza di caratteri non inclusi nei dati di origine. Usare il carattere di terminazione specificato con [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Eseguire il caricamento in una tabella di staging
Per trasferire i dati nel data warehouse, è anche possibile caricare prima di tutto i dati in una tabella d staging. L'uso di una tabella di staging consente di gestire gli errori senza interferire con le tabelle di produzione, evitando di eseguire operazioni di rollback sulla tabella di produzione. Una tabella di staging offre anche l'opportunità di usare SQL Data Warehouse per eseguire trasformazioni prima di inserire i dati nelle tabelle di produzione.

Per eseguire il caricamento con T-SQL, eseguire l'istruzione T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md). Questo comando inserisce i risultati di un'istruzione di selezione in una nuova tabella. Quando l'istruzione effettua la selezione da una tabella esterna, importa i dati esterni. 

Nell'esempio seguente, ext.Date è una tabella esterna. Tutte le righe vengono importate in una nuova tabella denominata dbo.Date.

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
Mentre i dati sono nella tabella di staging, eseguire le trasformazioni richieste dal carico di lavoro, quindi spostare i dati in una tabella di produzione.

## <a name="insert-data-into-production-table"></a>Inserire dati in una tabella di produzione

L'istruzione INSERT INTO... SELECT sposta i dati dalla tabella di staging alla tabella permanente. 

Quando si progetta un processo ETL, provare a eseguire il processo su un campione di test di piccole dimensioni. Provare a estrarre 1000 righe dalla tabella in un file, spostarlo in Azure e quindi provare a caricarlo in una tabella di staging. 

## <a name="partner-loading-solutions"></a>Soluzioni di caricamento dei partner
Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere l'elenco dei [partner che offrono soluzioni](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Passaggi successivi
Per linee guida relative al caricamento, vedere [Linee guida per il caricamento di dati](guidance-for-loading-data.md).


