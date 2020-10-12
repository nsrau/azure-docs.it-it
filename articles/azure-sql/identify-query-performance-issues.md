---
title: Tipi di problemi di prestazioni delle query nel database SQL di Azure
description: In questo articolo vengono fornite informazioni sui tipi di problemi di prestazioni delle query nel database SQL di Azure e viene inoltre illustrato come identificare e risolvere le query con questi problemi
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, sstein
ms.date: 03/10/2020
ms.openlocfilehash: afc142ec9de0e275d505276d959cfac3e652c55d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619764"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipi rilevabili di colli di bottiglia delle prestazioni delle query nel database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Quando si tenta di risolvere un collo di bottiglia delle prestazioni, iniziare determinando se il collo di bottiglia si verifica quando lo stato della query è in esecuzione o in attesa. Diverse risoluzioni si applicano in base a questa determinazione. Usare il diagramma seguente per comprendere i fattori che possono causare problemi correlati all'esecuzione o a un problema in attesa. In questo articolo vengono descritti i problemi e le risoluzioni relativi a ogni tipo di problema.

È possibile usare il database SQL di Azure [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) o SQL Server [DMV](database/monitoring-with-dmvs.md) per rilevare questi tipi di colli di bottiglia delle prestazioni.

![Stati del carico di lavoro](./media/identify-query-performance-issues/workload-states.png)

**Problemi relativi all'esecuzione**: i problemi correlati all'esecuzione sono in genere correlati a problemi di compilazione, causando un piano di query non ottimale o problemi di esecuzione correlati a risorse insufficienti o sovrautilizzate.
**Problemi relativi all'attesa**: i problemi correlati all'attesa sono in genere correlati a:

- Blocchi (blocco)
- I/O
- Contesa relativa all'utilizzo di TempDB
- Attese di concessione di memoria

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemi di compilazione che provocano un piano di query non ottimale

Un piano non ottimale generato da Query Optimizer di SQL può essere causato da un rallentamento delle prestazioni delle query. SQL Query Optimizer potrebbe produrre un piano non ottimale a causa di un indice mancante, di statistiche non aggiornate, di una stima non corretta del numero di righe da elaborare o di una stima non accurata della memoria necessaria. Se si sa che la query è stata eseguita più velocemente in passato o in un'altra istanza, confrontare i piani di esecuzione effettivi per verificare se sono diversi.

- Identificare gli eventuali indici mancanti utilizzando uno di questi metodi:

  - Usare [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Advisor per database](database/database-advisor-implement-performance-recommendations.md) per database singoli e in pool.
  - DMV. Questo esempio illustra l'effetto di un indice mancante, come rilevare gli [indici mancanti](database/performance-guidance.md#identifying-and-adding-missing-indexes) usando DMV e l'effetto dell'implementazione della raccomandazione per gli indici mancanti.
- Provare ad applicare gli hint per la [query](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query), [aggiornare le statistiche](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)o [ricompilare gli indici](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) per ottenere il piano migliore. Abilitare la [correzione automatica dei piani](../azure-sql/database/automatic-tuning-overview.md) nel database SQL di Azure per attenuare automaticamente questi problemi.

  Questo [esempio](database/performance-guidance.md#query-tuning-and-hinting) illustra l'effetto di un piano di query non ottimale a causa di una query con parametri, come rilevare questa condizione e come usare un hint per la query per la risoluzione.

- Provare a modificare il livello di compatibilità del database e a implementare l'elaborazione intelligente delle query. SQL Query Optimizer può generare un piano di query diverso a seconda del livello di compatibilità del database. I livelli di compatibilità più elevati offrono [funzionalità di elaborazione delle query più intelligenti](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing).

  - Per ulteriori informazioni sull'elaborazione di query, vedere [Guida all'architettura di elaborazione delle query](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide).
  - Per modificare i livelli di compatibilità del database e leggere altre informazioni sulle differenze tra i livelli di compatibilità, vedere [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Per altre informazioni sulla stima della cardinalità, vedere [stima della cardinalità](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server) .

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Risoluzione di query con piani di esecuzione di query non ottimali

Nelle sezioni seguenti viene illustrato come risolvere le query con un piano di esecuzione delle query non ottimale.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Query con problemi di piano con distinzione tra parametri (PSP)

Un problema relativo al piano con distinzione tra parametri (PSP) si verifica quando il Query Optimizer genera un piano di esecuzione della query ottimale solo per un valore di parametro specifico (o set di valori) e il piano memorizzato nella cache non è ottimale per i valori dei parametri utilizzati nelle esecuzioni consecutive. I piani che non sono ottimali possono causare problemi di prestazioni delle query e peggiorare la velocità effettiva complessiva del carico di lavoro.

Per ulteriori informazioni sull'analisi dei parametri e sull'elaborazione di query, vedere la [Guida sull'architettura di elaborazione delle query](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Diverse soluzioni alternative possono mitigare i problemi relativi a PSP. Per ogni soluzione alternativa sono stati associati compromessi e svantaggi:

- Usare l'hint per la query [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) a ogni esecuzione di query. Questa soluzione alternativa offre tempi di compilazione ridotti e una maggiore quantità di CPU per una migliore qualità del piano. L' `RECOMPILE` opzione non è spesso possibile per i carichi di lavoro che richiedono una velocità effettiva elevata.
- Utilizzare l'hint per la query [Option (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo con un valore di parametro tipico che produce un piano sufficientemente adatto per la maggior parte delle possibilità di valore del parametro. Questa opzione richiede una buona conoscenza dei valori di parametro ottimali e delle caratteristiche del piano associate.
- Utilizzare l'hint per la query [Option (Optimize for Unknown)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo e utilizzare invece la media del vettore di densità. È anche possibile eseguire questa operazione acquisendo i valori dei parametri in ingresso nelle variabili locali e quindi usando le variabili locali all'interno dei predicati invece di usare i parametri stessi. Per questa correzione, la densità media deve essere *sufficientemente adeguata*.
- Disabilitare interamente lo sniffing dei parametri utilizzando l'hint per la query [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Utilizzare l'hint per la query [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per impedire ricompilazioni nella cache. Questa soluzione si basa sul presupposto che il piano comune sufficientemente valido sia quello nella cache. È anche possibile disabilitare gli aggiornamenti automatici delle statistiche per ridurre le probabilità che venga eliminato il piano valido e che verrà compilato un nuovo piano non valido.
- Forzare il piano in modo esplicito utilizzando l'hint per la query [use Plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) riscrivendo la query e aggiungendo l'hint nel testo della query. In alternativa, impostare un piano specifico utilizzando Query Store o abilitando l' [ottimizzazione automatica](../azure-sql/database/automatic-tuning-overview.md).
- Sostituire la procedura singola con un set annidato di procedure, ognuna delle quali può essere usata in base alla logica condizionale e ai valori dei parametri associati.
- Creare alternative basate sull'esecuzione di stringhe dinamiche per una definizione di procedura statica.

Per ulteriori informazioni sulla risoluzione dei problemi relativi a PSP, vedere i post di Blog seguenti:

- [Odore di un parametro](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor rispetto a SQL dinamico e procedure rispetto alla qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Tecniche di ottimizzazione delle query SQL in SQL Server: sniffing dei parametri](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Attività di compilazione causata dalla parametrizzazione non corretta

Quando una query ha valori letterali, il motore di database parametrizza automaticamente l'istruzione o un utente parametrizza in modo esplicito l'istruzione per ridurre il numero di compilazioni. Un numero elevato di compilazioni per una query che usa lo stesso modello, ma valori letterali diversi può comportare un utilizzo elevato della CPU. Analogamente, se si imposta solo parzialmente un parametrizzamento di una query che continua a avere valori letterali, il motore di database non imposta ulteriormente i parametri della query.

Di seguito è riportato un esempio di una query con parametri parziale:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In questo esempio, `t1.c1` accetta `@p1` , ma `t2.c2` continua a assumere il GUID come valore letterale. In questo caso, se si modifica il valore di `c2` , la query viene considerata come una query diversa e verrà eseguita una nuova compilazione. Per ridurre le compilazioni in questo esempio, è necessario parametrizzare anche il GUID.

La query seguente mostra il numero di query in base all'hash della query per determinare se una query è parametrizzata correttamente:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Fattori che influiscono sulle modifiche al piano di query

Una ricompilazione del piano di esecuzione della query può comportare un piano di query generato che differisce dal piano originale memorizzato nella cache. Un piano originale esistente potrebbe essere ricompilato automaticamente per vari motivi:

- La query fa riferimento alle modifiche nello schema
- La query fa riferimento a modifiche ai dati delle tabelle
- Opzioni del contesto di query modificate

È possibile che un piano compilato venga espulso dalla cache per diversi motivi, ad esempio:

- Riavvii istanza
- Modifiche della configurazione con ambito database
- Utilizzo elevato della memoria
- Richieste esplicite di cancellazione della cache

Se si usa un hint RECOMPILE, un piano non verrà memorizzato nella cache.

Una ricompilazione (o una nuova compilazione dopo la rimozione della cache) può comunque comportare la generazione di un piano di esecuzione della query identico all'originale. Quando il piano viene modificato rispetto al piano precedente o originale, è probabile che queste spiegazioni siano:

- **Progettazione fisica modificata**: ad esempio, gli indici appena creati coprono in modo più efficace i requisiti di una query. I nuovi indici possono essere utilizzati in una nuova compilazione se il Query Optimizer decide che l'utilizzo di tale nuovo indice è più ottimale rispetto all'utilizzo della struttura di dati selezionata originariamente per la prima versione dell'esecuzione della query. Eventuali modifiche fisiche apportate agli oggetti a cui si fa riferimento possono comportare una nuova scelta del piano in fase di compilazione.

- **Differenze**tra le risorse del server: quando un piano in un sistema differisce dal piano in un altro sistema, la disponibilità delle risorse, ad esempio il numero di processori disponibili, può influenzare il piano generato. Ad esempio, se un sistema dispone di più processori, è possibile che venga scelto un piano parallelo.

- **Statistiche diverse**: le statistiche associate agli oggetti a cui si fa riferimento potrebbero essere state modificate o potrebbero essere materialmente diverse dalle statistiche del sistema originale. Se le statistiche cambiano e viene eseguita una ricompilazione, il Query Optimizer utilizza le statistiche a partire da quando sono state modificate. Le frequenze e le distribuzioni dei dati delle statistiche rivedute potrebbero essere diverse da quelle della compilazione originale. Queste modifiche vengono usate per creare stime della cardinalità. (Le*stime della cardinalità* sono il numero di righe che dovrebbero essere propagate attraverso l'albero delle query logiche). Le modifiche alle stime della cardinalità possono comportare la scelta di operatori fisici diversi e gli ordini di operazioni associati. Anche le modifiche minime alle statistiche possono generare un piano di esecuzione delle query modificato.

- Modifica del **livello di compatibilità del database o della versione di stima della cardinalità**: le modifiche apportate al livello di compatibilità del database possono consentire nuove strategie e funzionalità che potrebbero generare un piano di esecuzione di query diverso. Oltre al livello di compatibilità del database, un flag di traccia disabilitato o abilitato 4199 o uno stato modificato della configurazione con ambito database QUERY_OPTIMIZER_HOTFIXES possono anche influenzare le scelte del piano di esecuzione delle query in fase di compilazione. I flag di traccia 9481 (Force legacy CE) e 2312 (Force default CE) influiscono anche sul piano.

## <a name="resource-limits-issues"></a>Problemi relativi ai limiti delle risorse

Le prestazioni delle query lente non correlate ai piani di query non ottimali e agli indici mancanti sono in genere correlate a risorse insufficienti o sovrautilizzate. Se il piano di query è ottimale, è possibile che la query (e il database) raggiunga i limiti delle risorse per il database, il pool elastico o l'istanza gestita. Un esempio potrebbe essere una velocità effettiva di scrittura del log in eccesso per il livello di servizio.

- Rilevamento dei problemi relativi alle risorse tramite la portale di Azure: per verificare se il problema riguarda i limiti delle risorse, vedere [monitoraggio delle risorse del database SQL](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Per i database singoli e i pool elastici, vedere [Advisor per database raccomandazioni sulle prestazioni](database/database-advisor-implement-performance-recommendations.md) e [informazioni dettagliate sulle prestazioni delle query](database/query-performance-insight-use.md).
- Rilevamento dei limiti delle risorse tramite [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Rilevamento di problemi di risorse con [DMV](database/monitoring-with-dmvs.md):

  - La DMV [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) restituisce CPU, i/O e utilizzo di memoria per il database. È presente una riga per ogni intervallo di 15 secondi, anche se non è presente alcuna attività nel database. I dati cronologici vengono mantenuti per un'ora.
  - La DMV [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) restituisce i dati di archiviazione e di utilizzo della CPU per il database SQL di Azure. I dati vengono raccolti e aggregati in intervalli di cinque minuti.
  - [Numerose singole query hanno un utilizzo cumulativo elevato della CPU](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Se si identifica il problema come risorsa insufficiente, è possibile aggiornare le risorse per aumentare la capacità del database in modo da assorbire i requisiti della CPU. Per altre informazioni, vedere [ridimensionare le risorse di database singolo nel database SQL di Azure](database/single-database-scale.md) e [ridimensionare le risorse del pool elastico nel database SQL di Azure](database/elastic-pool-scale.md). Per informazioni sul ridimensionamento di un'istanza gestita, vedere [limiti delle risorse del livello di servizio](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemi di prestazioni causati dall'aumento del volume del carico di lavoro

Un aumento del traffico e del volume del carico di lavoro dell'applicazione può causare un maggiore utilizzo della CPU. Tuttavia, è necessario prestare attenzione a diagnosticare correttamente questo problema. Quando si verifica un problema di CPU elevato, rispondere a queste domande per determinare se l'aumento è causato da modifiche al volume del carico di lavoro:

- Le query dell'applicazione hanno causato il problema della CPU elevata?
- Per le [prime query che utilizzano la CPU che è possibile identificare](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Sono stati associati più piani di esecuzione alla stessa query? In caso affermativo, perché?
  - Per le query con lo stesso piano di esecuzione, i tempi di esecuzione sono coerenti? Il numero di esecuzioni è aumentato? In tal caso, l'aumento del carico di lavoro potrebbe causare problemi di prestazioni.

In breve, se il piano di esecuzione della query non viene eseguito in modo diverso, ma aumenta l'utilizzo della CPU insieme al numero di esecuzioni, il problema di prestazioni è probabilmente correlato all'aumento del carico di lavoro

Non è sempre facile identificare una modifica del volume del carico di lavoro che determina un problema di CPU. Tenere presente questi fattori:

- **Modifica dell'utilizzo delle risorse**: si consideri, ad esempio, uno scenario in cui l'utilizzo della CPU è aumentato fino al 80% per un lungo periodo di tempo. Solo utilizzo CPU non significa che il volume del carico di lavoro è stato modificato. Anche le regressioni nel piano di esecuzione della query e le modifiche nella distribuzione dei dati possono contribuire a un utilizzo più delle risorse anche se l'applicazione esegue lo stesso carico di lavoro.

- **L'aspetto di una nuova query**: un'applicazione può guidare un nuovo set di query in momenti diversi.

- **Aumento o riduzione del numero di richieste**: questo scenario è la misura più ovvia di un carico di lavoro. Il numero di query non corrisponde sempre a un maggiore utilizzo delle risorse. Tuttavia, questa metrica è ancora un segnale significativo, supponendo che altri fattori siano invariati.

Usare Intelligent Insights per rilevare gli [aumenti del carico di lavoro](database/intelligent-insights-troubleshoot-performance.md#workload-increase) e pianificare le [regressioni](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemi relativi all'attesa

Una volta eliminato un piano non ottimale e i problemi *correlati all'attesa* correlati ai problemi di esecuzione, il problema di prestazioni è in genere che le query sono in attesa di una risorsa. I problemi relativi alle attese potrebbero essere causati da:

- **Blocco**:

  Una query può mantenere il blocco sugli oggetti nel database mentre altri tentano di accedere agli stessi oggetti. È possibile identificare le query di blocco usando [DMV](database/monitoring-with-dmvs.md#monitoring-blocked-queries) o [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking).
- **Problemi di IO**

  È possibile che le query attendano la scrittura delle pagine nei file di dati o di log. In tal caso, controllare le `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` `PAGEIOLATCH_*` statistiche di attesa, o nella DMV. Vedere uso di DMV per [identificare i problemi di prestazioni](database/monitoring-with-dmvs.md#identify-io-performance-issues)di i/o.
- **Problemi di TempDB**

  Se il carico di lavoro utilizza tabelle temporanee o si verifica un problema di TempDB nei piani, è possibile che si verifichi un problema con la velocità effettiva di TempDB. Vedere uso di DMV per [identificare i problemi di tempdb](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemi relativi alla memoria**

  Se il carico di lavoro non dispone di memoria sufficiente, la permanenza presunta delle pagine potrebbe essere eliminata o le query potrebbero ottenere meno memoria del necessario. In alcuni casi, l'Intelligence incorporata in Query Optimizer risolverà i problemi correlati alla memoria. Vedere uso di DMV per [identificare i problemi di concessione di memoria](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metodi per visualizzare le categorie di attesa principali

Questi metodi vengono comunemente usati per mostrare le categorie principali dei tipi di attesa:

- Usare Intelligent Insights per identificare le query con un calo delle prestazioni a causa di [attese aumentate](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Usare [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per trovare le statistiche di attesa per ogni query nel tempo. In Query Store, i pi di attesa vengono combinati in categorie di attesa. È possibile trovare il mapping delle categorie di attesa ai tipi di attesa in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilizzare [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) per restituire informazioni su tutte le attese rilevate dai thread eseguiti durante un'operazione di query. È possibile usare questa visualizzazione aggregata per diagnosticare problemi di prestazioni con il database SQL di Azure e anche con query e batch specifici. Le query possono essere in attesa di risorse, attese di accodamento o attese esterne.
- Utilizzare [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) per restituire informazioni sulla coda di attività in attesa di una risorsa.

Negli scenari con CPU elevata, Query Store e le statistiche di attesa potrebbero non riflettere l'utilizzo della CPU se:

- Le query con utilizzo elevato di CPU sono ancora in esecuzione.
- Le query con utilizzo elevato della CPU venivano eseguite quando si verificava un failover.

DMV che tengono traccia delle statistiche di attesa e Query Store visualizzano i risultati solo per le query completate correttamente e scadute. Non visualizzano i dati per le istruzioni attualmente in esecuzione fino al completamento delle istruzioni. Utilizzare la vista a gestione dinamica [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per tenere traccia delle query attualmente in esecuzione e del tempo di lavoro associato.

> [!TIP]
> Strumenti aggiuntivi:
>
> - [TigerToolbox attese e latch](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [Usp_whatsup TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del monitoraggio e dell'ottimizzazione del database SQL](database/monitor-tune-overview.md)
