---
title: Procedure di sviluppo consigliate
description: Raccomandazioni e procedure consigliate da tenere presente durante lo sviluppo di soluzioni per il pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745375"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Procedure consigliate per lo sviluppo per il pool SQL sinapsi

Questo articolo descrive le linee guida e le procedure consigliate per lo sviluppo della soluzione del pool SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto

- [Ottimizzazione delle prestazioni con viste materializzate](performance-tuning-materialized-views.md)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](performance-tuning-ordered-cci.md)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite la sospensione e la scalabilità, vedere l'articolo gestire le risorse di [calcolo](sql-data-warehouse-manage-compute-overview.md) .

## <a name="maintain-statistics"></a>Gestire le statistiche

Il pool SQL può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  I piani di query creati dall'utilità di ottimizzazione sono validi solo per le statistiche disponibili.  

È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni carico per garantire che le statistiche sulle colonne utilizzate nelle query siano sempre aggiornate.

Se si ritiene che l'aggiornamento di tutte le statistiche richieda troppo tempo, è consigliabile provare a usare più selettivamente le colonne che necessitano di aggiornamenti frequenti delle statistiche. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno.

> [!TIP]
> Per ottenere il massimo vantaggio, è necessario avere aggiornato le statistiche sulle colonne incluse nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne presenti in GROUP BY.

Vedere anche [gestire le statistiche delle tabelle](sql-data-warehouse-tables-statistics.md), [creare statistiche](sql-data-warehouse-tables-statistics.md)e [aggiornare le statistiche](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  Questa progettazione consente agli utenti di iniziare a creare tabelle in modo semplice senza dover decidere come distribuire le tabelle.  

Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni notevolmente migliori.  L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.  

Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  Gli articoli che seguono forniscono ulteriori dettagli sul miglioramento delle prestazioni selezionando una colonna di distribuzione e come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE Tables.

Vedere anche [Panoramica delle tabelle](sql-data-warehouse-tables-overview.md), [distribuzione delle tabelle](sql-data-warehouse-tables-distribute.md), selezione della distribuzione delle [tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [Create Table](sql-data-warehouse-tables-overview.md)e [create table come SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Sebbene i dati di partizionamento possano essere efficaci per gestire i dati tramite il cambio della partizione o l'ottimizzazione delle analisi tramite l'eliminazione della partizione, la presenza di un numero eccessivo di partizioni può rallentare le query.  

Spesso, una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL.  Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe.  

Tenere presente che, dietro le quinte, il pool SQL esegue il partizionamento dei dati in un database di 60, pertanto se si crea una tabella con 100 partizioni, ciò comporta in realtà 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

> [!TIP]
> Valutare la possibilità di usare una granularità inferiore rispetto a quella che potrebbe avere funzionato in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [partizionamento delle tabelle](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Se, ad esempio, si dispone di un inserimento, che si prevede di eseguire 1 ora, se possibile, suddividere l'inserimento in quattro parti, ognuna delle quali verrà eseguita in 15 minuti.  Utilizzare casi di registrazione minimi speciali, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, per ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, è consigliabile usare un CTAS per scrivere i dati da tenere in una tabella anziché usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è un'operazione molto più sicura da eseguire perché ha una registrazione minima delle transazioni e può essere annullata rapidamente, se necessario.

Vedere anche [informazioni sulle transazioni](sql-data-warehouse-develop-transactions.md), [ottimizzazione delle transazioni](sql-data-warehouse-develop-best-practices-transactions.md), [partizionamento delle tabelle](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [create table As Select (CTAs)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce la DDL, l'utilizzo del tipo di dati più piccolo che supporterà i dati migliorerà le prestazioni di esecuzione delle query.  Questo approccio è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Cenni preliminari sulle tabelle](sql-data-warehouse-tables-overview.md), [tipi di dati della tabella](sql-data-warehouse-tables-data-types.md)e [Create Table](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come cluster columnstore.  

> [!NOTE]
> Per ottenere prestazioni ottimali per le query sulle tabelle columnstore, è importante avere una qualità di segmento adeguata.  

Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  

Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere l'articolo relativo alle [cause della qualità dell'indice columnstore scadente](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) nell'articolo sugli [indici di tabella](sql-data-warehouse-tables-index.md) .  

Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente presenti nella classe di risorse media o large per il caricamento dei dati. L'uso di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione righe per tabella e ogni tabella del pool SQL viene partizionata in 60 tabelle, in genere le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non contenga più di 60 milioni  

Per una tabella con meno di 60 milioni righe, potrebbe non essere opportuno avere un indice columnstore.  ma potrebbe anche non avere effetti negativi.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella contiene 100 partizioni, sarà necessario disporre di almeno 6 miliardi righe per trarre vantaggio da un archivio columnstore cluster (60 distribuzioni *100 partizioni* 1 milione righe).  

Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore.

> [!TIP]
> Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [indici di tabella](sql-data-warehouse-tables-index.md), [Guida sugli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [ricompilazione degli indici columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Passaggi successivi

Se non si trova ciò che si sta cercando in questo articolo, provare a usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti di Azure sinapsi.  

Il [Forum sulle sinapsi di Azure](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) è una posizione in cui è possibile inviare domande ad altri utenti e al gruppo di prodotti sinapsi di Azure.  Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  

Se si preferisce porre domande in Stack Overflow, è disponibile anche un [forum di Stack Overflow su Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Usare la pagina dei [commenti sulle sinapsi di Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) per eseguire richieste di funzionalità.  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.
