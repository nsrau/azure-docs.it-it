---
title: 'Esercitazione: Caricare dati da Azure Data Lake Storage'
description: Usare l'istruzione COPY per caricare i dati da Azure Data Lake Storage per i pool SQL dedicati.
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
ms.openlocfilehash: 4886cf6c24e7e96676b9ca5e74b431685cd5a8bb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452864"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Caricare i dati da Azure Data Lake Storage in pool SQL dedicati in Azure sinapsi Analytics

Questa guida illustra come usare l' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) per caricare i dati da Azure Data Lake storage. Per esempi rapidi sull'uso dell'istruzione COPY in tutti i metodi di autenticazione, vedere la documentazione seguente: caricare i dati in modo [sicuro usando pool SQL dedicati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

> [!NOTE]  
> Per fornire commenti e suggerimenti o segnalare problemi nell'istruzione COPY, inviare un messaggio di posta elettronica alla seguente lista di distribuzione: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Creare la tabella di destinazione per caricare i dati da Azure Data Lake Storage.
> * Creare l'istruzione COPY per caricare i dati nel data warehouse.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questa esercitazione, scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Per eseguire questa esercitazione è necessario:

* Un pool SQL dedicato. Vedere [creare un pool SQL dedicato ed eseguire query sui dati](create-data-warehouse-portal.md).
* Un account di Data Lake Storage. Vedere [Introduzione ad Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Per questo account di archiviazione, è necessario configurare o specificare una delle credenziali seguenti per il caricamento: una chiave dell'account di archiviazione, una chiave di firma di accesso condiviso, un utente dell'applicazione di directory di Azure o un utente di AAD con il ruolo di Azure appropriato per l'account di archiviazione.

## <a name="create-the-target-table"></a>Creare la tabella di destinazione

Connettersi al pool SQL dedicato e creare la tabella di destinazione in cui si desidera caricare. In questo esempio viene creata una tabella della dimensione Product.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Creare l'istruzione COPY

Connettersi al pool dedicato di SQL ed eseguire l'istruzione COPY. Per un elenco completo degli esempi, vedere la documentazione seguente: [caricare in modo sicuro i dati usando i pool SQL dedicati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Ottimizzare la compressione columnstore

Per impostazione predefinita, le tabelle sono definite come indice columnstore cluster. Al termine di un caricamento, alcune delle righe di dati potrebbero non essere compresse nel columnstore.  Esiste una serie di motivi per cui questo può verificarsi. Per altre informazioni, vedere l'articolo [Gestire gli indici columnstore](sql-data-warehouse-tables-index.md).

Per ottimizzare le prestazioni delle query e la compressione columnstore dopo un'operazione di caricamento, ricompilare la tabella per forzare l'indice columnstore per comprimere tutte le righe.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Ottimizzare le statistiche

È consigliabile creare statistiche a colonna singola subito dopo un caricamento. Sono disponibili alcune opzioni per le statistiche. Ad esempio, se si creano statistiche a colonna singola su ogni colonna, ricompilare tutte le statistiche potrebbe richiedere molto tempo. Se si sa che alcune colonne non si aggiungeranno ai predicati di query, è possibile ignorare la creazione delle statistiche su tali colonne.

Per creare statistiche a colonna singola su ogni colonna di ogni tabella, è possibile usare l'esempio di codice di stored procedure `prc_sqldw_create_stats` riportato nell'articolo relativo alle [statistiche](sql-data-warehouse-tables-statistics.md).

L'esempio seguente è un buon punto di partenza per la creazione delle statistiche. Qui vengono create statistiche a colonna singola su ogni colonna nella tabella della dimensione e su ogni colonna di join nelle tabelle dei fatti. È sempre possibile aggiungere in un secondo momento statistiche a colonna singola o a più colonne per altre colonne delle tabelle dei fatti.

## <a name="achievement-unlocked"></a>Obiettivo raggiunto

I dati sono stati caricati correttamente nel data warehouse. Ottimo lavoro.

## <a name="next-steps"></a>Passaggi successivi
Il caricamento dei dati è il primo passaggio per lo sviluppo di una soluzione di data warehouse con Azure Synapse Analytics. Vedere le risorse di sviluppo.

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo di tabelle per il data warehousing](sql-data-warehouse-tables-overview.md)

Per ulteriori informazioni sul caricamento di esempi e riferimenti, vedere la documentazione seguente:
- [Documentazione di riferimento dell'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [COPIA esempi per ogni metodo di autenticazione](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [COPIA avvio rapido per una singola tabella](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
