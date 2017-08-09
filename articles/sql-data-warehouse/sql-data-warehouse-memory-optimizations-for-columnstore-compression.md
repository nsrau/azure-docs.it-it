---
title: Migliorare le prestazioni dell'indice columnstore in Azure SQL | Documentazione Microsoft
description: Ridurre i requisiti di memoria o aumentare la memoria disponibile per accrescere al massimo il numero di righe che un indice columnstore comprime in ogni gruppo di righe.
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 6/2/2017
ms.author: shigu;barbkess
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: a0452c4dedc218dff17404c4ecee70d788e49644
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Ottimizzazione della qualità di un gruppo di righe per columnstore

La qualità di un gruppo di righe è determinata dal numero di righe nel gruppo. Ridurre i requisiti di memoria o aumentare la memoria disponibile per accrescere al massimo il numero di righe che un indice columnstore comprime in ogni gruppo di righe.  Usare questi metodi per migliorare il tasso di compressione e le prestazioni delle query per gli indici columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Perché sono importanti le dimensioni del gruppo di righe
Poiché un indice columnstore analizza una tabella eseguendo la scansione di segmenti di colonna di singoli gruppi di righe, accrescendo al massimo il numero di righe in ogni gruppo di righe le prestazioni delle query migliorano. Quando i gruppi di righe hanno un numero elevato di righe, la compressione dei dati migliora, il che significa meno dati da leggere dal disco.

Per altre informazioni sui gruppi di righe, vedere [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Dimensioni di destinazione per i gruppi di righe
Per ottimizzare le prestazioni delle query, l'obiettivo è accrescere al massimo il numero di righe per ogni gruppo di righe in un indice columnstore. Un gruppo di righe può avere un massimo di 1.048.576 righe. È accettabile non avere il numero massimo di righe per gruppo di righe. Gli indici columnstore ottengono buone prestazioni quando i gruppi di righe hanno almeno 100.000 righe.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>I gruppi di righe possono essere tagliati durante la compressione

Durante un caricamento bulk o la ricompilazione di un indice columnstore, talvolta non è disponibile memoria sufficiente per comprimere tutte le righe per ogni gruppo di righe. Quando la memoria disponibile è scarsa, gli indici columnstore riducono le dimensioni del gruppo di righe in modo da consentire la compressione nel columnstore. 

Quando la memoria è insufficiente per la compressione di almeno 10.000 righe in ogni gruppo di righe, SQL Data Warehouse genera un errore.

Per altre informazioni sul caricamento bulk, vedere [Caricamento bulk in un indice columnstore cluster](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Come monitorare la qualità di un gruppo di righe

È presente una DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats) che espone informazioni utili come il numero di righe presenti nei gruppi ed eventualmente il motivo per cui un gruppo di righe è stato tagliato. Per effettuare una query su questa DMV allo scopo di ottenere informazioni sul trimming di un gruppo di righe, è possibile creare la vista seguente.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

trim_reason_desc specifica se il gruppo di righe è stato tagliato (trim_reason_desc = NO_TRIM indica che il gruppo di righe è di qualità ottimale e non è stato tagliato). I motivi seguenti indicano che il gruppo di righe è stato tagliato prematuramente:
- BULKLOAD: questo motivo viene usato se il batch di righe in ingresso per il caricamento è inferiore a 1 milione di righe. Il motore creerà gruppi di righe compressi se devono essere inserite più di 100.000 righe (a differenza dell'inserimento nell'archivio differenziale), ma imposta il motivo per cui il gruppo è stato tagliato su BULKLOAD. In questo scenario, valutare l'opportunità di ampliare la finestra di caricamento in batch in modo da accumulare più righe. Rivalutare inoltre lo schema di partizionamento per verificare che non sia troppo granulare e che i gruppi di righe non possano quindi estendersi oltre i limiti della partizione.
- MEMORY_LIMITATION: per creare gruppi di righe con 1 milione di righe, il motore richiede una certa quantità di memoria di lavoro. Se la memoria disponibile nella sessione di caricamento è inferiore alla memoria di lavoro necessaria, i gruppi di righe vengono tagliati in modo prematuro. Le sezioni seguenti illustrano come stimare la memoria necessaria e allocare memoria aggiuntiva.
- DICTIONARY_SIZE: questo motivo indica che il gruppo di righe è stato tagliato perché era presente almeno una colonna di stringhe con stringhe "wide" e/o a cardinalità elevata. Le dimensioni del dizionario sono limitate a 16 MB di memoria e, al raggiungimento di questo limite, il gruppo di righe viene compresso. Se si verifica questa situazione, valutare l'opportunità di isolare la colonna problematica in una tabella separata.

## <a name="how-to-estimate-memory-requirements"></a>Come stimare i requisiti di memoria

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

La memoria massima necessaria per comprimere un gruppo di righe è circa

- 72 MB +
- \#righe \* \#colonne \* 8 byte +
- \#righe \* \#colonne stringa breve \* 32 byte +
- \#colonne stringa lunga \* 16 MB per il dizionario di compressione

dove le colonne stringa breve usano tipi di dati stringa < = 32 byte e le colonne stringa lunga usano tipi di dati stringa > 32 byte.

Le stringhe lunghe vengono compresse con un metodo di compressione progettato per la compressione del testo. Questo metodo di compressione usa un *dizionario* per archiviare i modelli di testo. La dimensione massima di un oggetto dictionary è 16 MB. Esiste un solo dizionario per ogni colonna stringa lunga nel gruppo di righe.

Per un'analisi approfondita dei requisiti di memoria columnstore, vedere il video [Azure SQL Data Warehouse scaling: configuration and guidance](https://myignite.microsoft.com/videos/14822) (Scalabilità di Azure SQL Data Warehouse: configurazione e linee guida).

## <a name="ways-to-reduce-memory-requirements"></a>Modi per ridurre i requisiti di memoria

Usare le tecniche seguenti per ridurre i requisiti di memoria per la compressione dei gruppi di righe in indici columnstore.

### <a name="use-fewer-columns"></a>Usare meno colonne
Se possibile, progettare la tabella con meno colonne. Quando un gruppo di righe viene compresso nel columnstore, l'indice columnstore comprime ogni segmento di colonna separatamente. Pertanto i requisiti di memoria per comprimere un gruppo di righe aumentano con l'aumentare del numero di colonne.


### <a name="use-fewer-string-columns"></a>Usare meno colonne di stringhe
Le colonne di dati di tipo stringa richiedono una quantità di memoria maggiore rispetto ai tipi di dati numerici. Per ridurre i requisiti di memoria, prendere in considerazione la rimozione delle colonne di tipo stringa dalle tabelle di dati e il loro inserimento in tabelle di dimensioni minori.

Requisiti di memoria aggiuntivi per la compressione di stringhe:

- I tipi di dati stringa fino a 32 caratteri possono richiedere 32 byte aggiuntivi per valore.
- I tipi di dati stringa con più di 32 caratteri vengono compressi mediante metodi di dizionario.  Ogni colonna del gruppo di righe può richiedere fino a 16 MB aggiuntivi per creare il dizionario.

### <a name="avoid-over-partitioning"></a>Evitare il partizionamento eccessivo

Gli indici columnstore creano uno o più gruppi di righe per partizione. In SQL Data Warehouse il numero di partizioni aumenta rapidamente perché i dati vengono distribuiti e ogni distribuzione è partizionata. Se la tabella ha troppe partizioni, potrebbero esserci abbastanza righe per riempire i gruppi di righe. La mancanza di righe non crea richiesta di memoria durante la compressione, ma alcuni gruppi di righe soffriranno di scarse prestazioni delle query columnstore.

Un altro motivo per evitare l'eccessivo partizionamento è che il caricamento di righe in un indice columnstore in una tabella partizionata comporta un sovraccarico della memoria. Durante il caricamento molte partizioni potrebbero ricevere le righe in ingresso, che vengono mantenute in memoria finché ogni partizione dispone di un numero di righe sufficiente da comprimere. Con un numero eccessivo di partizioni vengono create richieste di memoria aggiuntive.

### <a name="simplify-the-load-query"></a>Semplificare la query di caricamento

Il database condivide la concessione di memoria per una query tra tutti gli operatori della query. Quando una query di caricamento contiene ordinamenti complessi e join, la memoria disponibile per la compressione è ridotta.

Progettare la query di caricamento concentrandosi solo sul caricamento. Se è necessario eseguire trasformazioni sui dati, eseguirle separatamente dalla query di caricamento. Ad esempio, collocare temporaneamente i dati in una tabella heap, eseguire le trasformazioni e quindi caricare la tabella di gestione temporanea nell'indice columnstore. È possibile anche caricare prima i dati e poi usare il sistema MPP per trasformarli.

### <a name="adjust-maxdop"></a>Regolare MAXDOP

Ogni distribuzione comprime i gruppi di righe nel columnstore in parallelo quando c'è più di un core CPU disponibile per distribuzione. Il parallelismo richiede risorse di memoria aggiuntive che possono portare a richieste di memoria pesanti e al taglio del gruppo di righe.

Per ridurre le richieste di memoria, è possibile usare l'hint di query MAXDOP per forzare l'esecuzione seriale dell'operazione di caricamento in ogni distribuzione.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Modi per allocare altra memoria

La dimensione delle DWU e la classe della risorsa utente insieme determinano la quantità di memoria disponibile per una query dell'utente. Per aumentare la concessione di memoria per una query di caricamento, è possibile aumentare il numero di DWU o aumentare la classe risorsa.

- Per aumentare le DWU, vedere [Ridimensionare le prestazioni](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- Per cambiare la classe risorsa per una query, vedere [Esempio di modifica della classe di risorse di un utente](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

Ad esempio, sulla DWU 100 un utente nella classe risorsa smallrc può usare 100 MB di memoria per ogni distribuzione. Per informazioni dettagliate, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md).

Si supponga di determinare la necessità di 700 MB di memoria per ottenere le dimensioni di un gruppo di righe di alta qualità. Questi esempi illustrano come eseguire la query di caricamento con memoria sufficiente.

- Usando la DWU 1000 e mediumrc, la concessione di memoria è di 800 MB
- Usando la DWU 600 e largerc, la concessione di memoria è di 800 MB.


## <a name="next-steps"></a>Passaggi successivi

Per trovare altri modi con cui migliorare le prestazioni in SQL Data Warehouse, vedere la sezione [Panoramica](sql-data-warehouse-overview-manage-user-queries.md) relativa alle prestazioni.

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

