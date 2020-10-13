---
title: Migliorare le prestazioni dell'indice columnstore (anteprima aree di lavoro)
description: Ridurre i requisiti di memoria o aumentare la memoria disponibile per accrescere al massimo il numero di righe che un indice columnstore comprime in ogni gruppo di righe.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fecb78b240f5c983580d4bdb34535a879ffe3e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289277"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>Massimizza la qualità rowgroup per le prestazioni degli indici columnstore

La qualità di un gruppo di righe è determinata dal numero di righe nel gruppo. Aumentando la memoria disponibile è possibile massimizzare il numero di righe che un indice columnstore comprime in ogni rowgroup.  Usare questi metodi per migliorare il tasso di compressione e le prestazioni delle query per gli indici columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Perché sono importanti le dimensioni del gruppo di righe

Poiché un indice columnstore analizza una tabella eseguendo la scansione di segmenti di colonna di singoli gruppi di righe, accrescendo al massimo il numero di righe in ogni gruppo di righe le prestazioni delle query migliorano. Quando i gruppi di righe hanno un numero elevato di righe, la compressione dei dati migliora, il che significa meno dati da leggere dal disco.

Per altre informazioni sui gruppi di righe, vedere [Descrizione degli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="target-size-for-rowgroups"></a>Dimensioni di destinazione per i gruppi di righe

Per ottimizzare le prestazioni delle query, l'obiettivo è accrescere al massimo il numero di righe per ogni gruppo di righe in un indice columnstore. Un gruppo di righe può avere un massimo di 1.048.576 righe. È accettabile non avere il numero massimo di righe per gruppo di righe. Gli indici columnstore ottengono buone prestazioni quando i gruppi di righe hanno almeno 100.000 righe.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>I gruppi di righe possono essere tagliati durante la compressione

Durante un caricamento bulk o la ricompilazione di un indice columnstore, a volte non è disponibile memoria sufficiente per comprimere tutte le righe designate per ogni rowgroup. Quando la memoria disponibile è scarsa, gli indici columnstore riducono le dimensioni del gruppo di righe in modo da consentire la compressione nel columnstore.

Quando la memoria disponibile non è sufficiente per comprimere almeno 10.000 righe in ogni rowgroup, viene generato un errore.

Per altre informazioni sul caricamento bulk, vedere [Caricamento bulk in un indice columnstore cluster](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#Bulk&preserve-view=true ).

## <a name="how-to-monitor-rowgroup-quality"></a>Come monitorare la qualità di un gruppo di righe

Il sys.dm_pdw_nodes_db_column_store_row_group_physical_stats DMV ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) contiene la definizione della vista che corrisponde al database SQL) che espone informazioni utili, ad esempio il numero di righe in RowGroups e il motivo per cui è stato tagliato il taglio. Per effettuare una query su questa DMV allo scopo di ottenere informazioni sul trimming di un gruppo di righe, è possibile creare la vista seguente.

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

- BULKLOAD: questo motivo viene usato se il batch di righe in ingresso per il caricamento è inferiore a 1 milione di righe. Il motore creerà gruppi di righe compressi se devono essere inserite più di 100.000 righe (a differenza dell'inserimento nell'archivio differenziale), ma imposta il motivo per cui il gruppo è stato tagliato su BULKLOAD. In questo scenario, prendere in considerazione l'aumento del carico batch per includere più righe. Rivalutare inoltre lo schema di partizionamento per verificare che non sia troppo granulare e che i gruppi di righe non possano quindi estendersi oltre i limiti della partizione.
- MEMORY_LIMITATION: per creare gruppi di righe con 1 milione di righe, il motore richiede una certa quantità di memoria di lavoro. Se la memoria disponibile nella sessione di caricamento è inferiore alla memoria di lavoro necessaria, i gruppi di righe vengono tagliati in modo prematuro. Le sezioni seguenti illustrano come stimare la memoria necessaria e allocare memoria aggiuntiva.
- DICTIONARY_SIZE: questo motivo indica che il gruppo di righe è stato tagliato perché era presente almeno una colonna di stringhe con stringhe "wide" e/o a cardinalità elevata. Le dimensioni del dizionario sono limitate a 16 MB di memoria e, al raggiungimento di questo limite, il gruppo di righe viene compresso. Se si verifica questa situazione, valutare l'opportunità di isolare la colonna problematica in una tabella separata.

## <a name="how-to-estimate-memory-requirements"></a>Come stimare i requisiti di memoria

La quantità massima di memoria necessaria per comprimere un rowgroup è approssimativamente, come indicato di seguito:

- 72 MB +
- \#righe \* \# colonne \* 8 byte +
- \#righe \* \# stringa breve-colonne \* 32 byte +
- \#colonne stringa lunga \* 16 MB per il dizionario di compressione

> [!NOTE]
> Dove le colonne short string usano i tipi di dati stringa di <= 32 byte e le colonne con stringhe lunghe usano i tipi di dati stringa di > 32 byte.

Le stringhe lunghe vengono compresse con un metodo di compressione progettato per la compressione del testo. Questo metodo di compressione usa un *dizionario* per archiviare i modelli di testo. La dimensione massima di un oggetto dictionary è 16 MB. Esiste un solo dizionario per ogni colonna stringa lunga nel gruppo di righe.

Per una descrizione approfondita dei requisiti di memoria columnstore, vedere la pagina relativa alla [configurazione e alle linee guida per il video sinapsi SQL:](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)

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

Gli indici columnstore creano uno o più gruppi di righe per partizione. Per il data warehousing in Azure sinapsi Analytics, il numero di partizioni cresce rapidamente, perché i dati vengono distribuiti e ogni distribuzione è partizionata. Se la tabella ha troppe partizioni, potrebbero esserci abbastanza righe per riempire i gruppi di righe. La mancanza di righe non crea richiesta di memoria durante la compressione, ma alcuni gruppi di righe soffriranno di scarse prestazioni delle query columnstore.

Un altro motivo per evitare l'eccessivo partizionamento è che il caricamento di righe in un indice columnstore in una tabella partizionata comporta un sovraccarico della memoria. Durante il caricamento molte partizioni potrebbero ricevere le righe in ingresso, che vengono mantenute in memoria finché ogni partizione dispone di un numero di righe sufficiente da comprimere. Con un numero eccessivo di partizioni vengono create richieste di memoria aggiuntive.

### <a name="simplify-the-load-query"></a>Semplificare la query di caricamento

Il database condivide la concessione di memoria per una query tra tutti gli operatori della query. Quando una query di caricamento contiene ordinamenti complessi e join, la memoria disponibile per la compressione è ridotta.

Progettare la query di caricamento concentrandosi solo sul caricamento. Se è necessario eseguire trasformazioni sui dati, eseguirle separatamente dalla query di caricamento. Ad esempio, collocare temporaneamente i dati in una tabella heap, eseguire le trasformazioni e quindi caricare la tabella di gestione temporanea nell'indice columnstore. 

### <a name="adjust-maxdop"></a>Regolare MAXDOP

Ogni distribuzione comprime RowGroups nel columnstore in parallelo quando è disponibile più di un core CPU per ogni distribuzione. Il parallelismo richiede risorse di memoria aggiuntive che possono portare a richieste di memoria pesanti e al taglio del gruppo di righe.

Per ridurre le richieste di memoria, è possibile usare l'hint di query MAXDOP per forzare l'esecuzione seriale dell'operazione di caricamento in ogni distribuzione.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Modi per allocare altra memoria

La dimensione delle DWU e la classe della risorsa utente insieme determinano la quantità di memoria disponibile per una query dell'utente. Per aumentare la concessione di memoria per una query di caricamento, è possibile aumentare il numero di DWU o aumentare la classe risorsa.

- Per aumentare le DWU, vedere [Ridimensionare le prestazioni](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Per cambiare la classe risorsa per una query, vedere [Esempio di modifica della classe di risorse di un utente](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class).

## <a name="next-steps"></a>Passaggi successivi

Per altri modi per migliorare le prestazioni in sinapsi SQL, vedere [Panoramica delle prestazioni](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

 
