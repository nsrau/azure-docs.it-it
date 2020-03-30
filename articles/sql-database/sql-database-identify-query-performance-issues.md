---
title: Tipi di problemi di prestazioni delle query nel database SQL di AzureTypes of query performance issues in Azure SQL Database
description: In questo articolo vengono fornite informazioni sui tipi di problemi di prestazioni delle query nel database SQL di Azure e viene inoltre illustrato come identificare e risolvere le query con questi problemi
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256133"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipi rilevabili di colli di bottiglia delle prestazioni delle query nel database SQL di AzureDetectable types of query performance bottlenecks in Azure SQL Database

Quando si tenta di risolvere un collo di bottiglia delle prestazioni, iniziare determinando se il collo di bottiglia si verifica mentre la query è in esecuzione o in uno stato di attesa. A seconda di questa determinazione si applicano risoluzioni diverse. Utilizzare il diagramma seguente per comprendere meglio i fattori che possono causare un problema correlato all'esecuzione o in attesa. I problemi e le risoluzioni relative a ciascun tipo di problema sono discussi in questo articolo.

È possibile usare Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) o [le DMV](sql-database-monitoring-with-dmvs.md) di SQL Server per rilevare questi tipi di colli di bottiglia delle prestazioni.

![Stati del carico di lavoro](./media/sql-database-monitor-tune-overview/workload-states.png)

**Problemi relativi all'esecuzione:** i problemi relativi all'esecuzione sono in genere correlati a problemi di compilazione che causano un piano di query non ottimale o problemi di esecuzione correlati a risorse insufficienti o sovrautilizzate.
**Problemi legati all'attesa**: I problemi legati all'attesa sono generalmente correlati a:

- Blocchi (blocco)
- I/O
- Contesa correlata all'utilizzo di TempDB
- Attese di concessione di memoria

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemi di compilazione risultanti in un piano di query non ottimaleCompilation problems resulting in a suboptimal query plan

Un piano non ottimale generato da SQL Query Optimizer può essere la causa di un rallentamento delle prestazioni delle query. SQL Query Optimizer potrebbe produrre un piano non ottimale a causa di un indice mancante, statistiche non aggiornate, una stima non corretta del numero di righe da elaborare o una stima imprecisa della memoria necessaria. Se si sa che la query è stata eseguita più velocemente in passato o in un'altra istanza (un'istanza gestita o un'istanza di SQL Server), confrontare i piani di esecuzione effettivi per verificare se sono diversi.

- Identificare eventuali indici mancanti utilizzando uno dei metodi seguenti:Identify any missing indexes using one of these methods:

  - Utilizzare [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](sql-database-advisor.md) per database singoli e in pool.
  - Dmv. In questo esempio viene illustrato l'impatto di un indice [mancante,](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) come rilevare un indice mancante utilizzando DMV e l'impatto dell'implementazione della raccomandazione di indice mancante.
- Provare ad applicare hint di [query,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [aggiornare le statistiche](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)o [ricostruire gli indici](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) per ottenere il piano migliore. Abilitare la [correzione automatica](sql-database-automatic-tuning.md) del piano nel database SQL di Azure per ridurre automaticamente questi problemi.

  In [questo esempio](sql-database-performance-guidance.md#query-tuning-and-hinting) viene illustrato l'impatto di un piano di query non ottimale a causa di una query con parametri, come rilevare questa condizione e come utilizzare un hint di query per risolvere.

- Provare a modificare il livello di compatibilità del database e a implementare l'elaborazione intelligente delle query. SQL Query Optimizer può generare un piano di query diverso a seconda del livello di compatibilità per il database. Livelli di compatibilità più elevati forniscono funzionalità di elaborazione delle query più [intelligenti.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - Per ulteriori informazioni sull'elaborazione delle query, vedere [Guida all'architettura di elaborazione](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)delle query .
  - Per modificare i livelli di compatibilità del database e leggere ulteriori informazioni sulle differenze tra i livelli di compatibilità, vedere [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Per ulteriori informazioni sulla stima della cardinalità, vedere [Stima della cardinalità](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Risoluzione delle query con piani di esecuzione delle query non ottimali

Nelle sezioni seguenti viene illustrato come risolvere le query con un piano di esecuzione delle query non ottimale.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Query con problemi di piano sensibile ai parametri (PSP)

Un problema del piano sensibile ai parametri (PSP) si verifica quando Query Optimizer genera un piano di esecuzione delle query ottimale solo per un valore di parametro specifico (o set di valori) e il piano memorizzato nella cache non è quindi ottimale per i valori di parametro utilizzati in consecutivi Esecuzioni. I piani non ottimali possono quindi causare problemi di prestazioni delle query e ridurre la velocità effettiva complessiva del carico di lavoro.

Per ulteriori informazioni sullo sniffing dei parametri e sull'elaborazione delle query, vedere la [Guida all'architettura](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)di elaborazione delle query .

Diverse soluzioni alternative possono mitigare i problemi di PSP. A ogni soluzione alternativa sono associati compromessi e svantaggi:

- Usare l'hint per la query [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) a ogni esecuzione di query. Questa soluzione alternativa offre tempi di compilazione ridotti e una maggiore quantità di CPU per una migliore qualità del piano. L'opzione `RECOMPILE` spesso non è possibile per i carichi di lavoro che richiedono una velocità effettiva elevata.
- Utilizzare l'hint di query [OPTION (OPTIMI'E')](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per sostituire il valore effettivo del parametro con un valore di parametro tipico che produce un piano sufficiente per la maggior parte delle possibilità di valore dei parametri. Questa opzione richiede una buona conoscenza dei valori di parametro ottimali e delle caratteristiche del piano associate.
- Utilizzare l'hint di query [OPTION (OPTIMIE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per sostituire il valore effettivo del parametro e utilizzare invece la media vettoriale di densità. È anche possibile farlo catturando i valori dei parametri in ingresso nelle variabili locali e quindi utilizzando le variabili locali all'interno dei predicati anziché utilizzare i parametri stessi. Per questa correzione, la densità media deve essere *sufficientemente buona*.
- Disabilitare completamente lo sniffing dei parametri utilizzando l'hint per la query [DISABLE_PARAMETER_SNIFFING.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)
- Utilizzare l'hint per la query [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per impedire le ricompilazioni nella cache. Questa soluzione presuppone che il piano comune sufficientemente valido sia quello già nella cache. È inoltre possibile disabilitare gli aggiornamenti statistici automatici per ridurre le probabilità che il buon piano verrà rimosso e verrà compilato un nuovo piano non valido.
- Forzare il piano utilizzando in modo esplicito l'hint per la query [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) riscrivendo la query e aggiungendo l'hint nel testo della query. In alternativa, impostare un piano specifico utilizzando Query Store o abilitando [l'ottimizzazione automatica.](sql-database-automatic-tuning.md)
- Sostituire la procedura singola con un set annidato di procedure, ognuna delle quali può essere usata in base alla logica condizionale e ai valori dei parametri associati.
- Creare alternative basate sull'esecuzione di stringhe dinamiche per una definizione di procedura statica.

Per ulteriori informazioni sulla risoluzione dei problemi PSP, vedere questi post di blog:

- [Sento l'odore di un parametro](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Confronto tra Conor e SQL dinamico e procedure per la qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Tecniche di ottimizzazione delle query SQL in SQL Server: sniffing dei parametri](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Attività di compilazione causata da parametrizzazione non correttaCompile activity caused by improper parameterization

Quando una query dispone di valori letterali, il motore di database parametrizza automaticamente l'istruzione o un utente parametrizza in modo esplicito l'istruzione per ridurre il numero di compilazioni. Un numero elevato di compilazioni per una query che usa lo stesso modello ma valori letterali diversi possono comportare un utilizzo elevato della CPU. Analogamente, se si parametrizza solo parzialmente una query che continua ad avere valori letterali, il motore di database non parametrizza ulteriormente la query.

Di seguito è riportato un esempio di query parzialmente con parametri:Here's an example of a partially parameterized query:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In questo `t1.c1` esempio, `@p1` `t2.c2` accetta , ma continua a accettare GUID come valore letterale. In questo caso, se si `c2`modifica il valore di , la query viene considerata come una query diversa e verrà eseguita una nuova compilazione. Per ridurre le compilazioni in questo esempio, è necessario anche parametrizzare il GUID.

La query seguente mostra il conteggio delle query in base all'hash della query per determinare se una query è parametrizzata correttamente:

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

### <a name="factors-that-affect-query-plan-changes"></a>Fattori che influiscono sulle modifiche del piano di queryFactors that affect query plan changes

La ricompilazione di un piano di esecuzione delle query potrebbe comportare un piano di query generato diverso dal piano memorizzato nella cache originale. Un piano originale esistente potrebbe essere ricompilato automaticamente per vari motivi:An existing original plan might be automatically compiled for various reasons:

- Le modifiche nello schema fanno riferimento alla query
- La query fa riferimento alle modifiche apportate ai dati delle tabelle
- Le opzioni del contesto della query sono state modificate

Un piano compilato potrebbe essere espulso dalla cache per vari motivi, ad esempio:A compiled plan might be ejected from the cache for various reasons, such as:

- Riavvio dell'istanza
- Modifiche alla configurazione con ambito databaseDatabase-scoped configuration changes
- Utilizzo elevato della memoria
- Richieste esplicite di cancellazione della cacheExplicit requests to clear the cache

Se si utilizza un hint RECOMPILE, un piano non verrà memorizzato nella cache.

Una ricompilazione (o nuova compilazione dopo lo sfratto della cache) può comunque comportare la generazione di un piano di esecuzione della query identico all'originale. Quando il piano cambia rispetto al piano precedente o originale, è probabile che queste spiegazioni:

- **Progettazione fisica modificata:** Ad esempio, gli indici appena creati coprono in modo più efficace i requisiti di una query. I nuovi indici potrebbero essere utilizzati in una nuova compilazione se Query Optimizer decide che l'utilizzo di tale nuovo indice è più ottimale rispetto all'utilizzo della struttura di dati originariamente selezionata per la prima versione dell'esecuzione della query. Eventuali modifiche fisiche agli oggetti di riferimento potrebbero comportare una nuova scelta del piano in fase di compilazione.

- **Differenze**di risorse del server: quando un piano in un sistema è diverso dal piano di un altro sistema, la disponibilità delle risorse, ad esempio il numero di processori disponibili, può influire sul piano generato. Ad esempio, se un sistema dispone di più processori, è possibile scegliere un piano parallelo.

- **Statistiche diverse**: Le statistiche associate agli oggetti di riferimento potrebbero essere cambiate o essere sostanzialmente diverse dalle statistiche del sistema originale. Se le statistiche cambiano e si verifica una ricompilazione, Query Optimizer utilizza le statistiche a partire da quando sono state modificate. Le distribuzioni e le frequenze dei dati delle statistiche rivedute potrebbero differire da quelle della compilazione originale. Queste modifiche vengono utilizzate per creare stime di cardinalità. Le stime di*cardinalità* sono il numero di righe che si prevede scorrano attraverso l'albero delle query logiche. Le modifiche alle stime di cardinalità potrebbero portare a scegliere operatori fisici diversi e ordini di operazioni associati. Anche modifiche minori alle statistiche possono comportare un piano di esecuzione delle query modificato.

- Versione del livello di compatibilità del database o della versione del **stimatore di cardinalità modificata:** le modifiche al livello di compatibilità del database possono abilitare nuove strategie e funzionalità che potrebbero generare un piano di esecuzione delle query diverso. Oltre al livello di compatibilità del database, un flag di traccia 4199 disabilitato o abilitato o uno stato modificato della configurazione con ambito database QUERY_OPTIMIZER_HOTFIXES possono influire anche sulle scelte del piano di esecuzione delle query in fase di compilazione. I flag di traccia 9481 (forza legacy CE) e 2312 (impostazione predefinita della forza) influiscono anche sul piano.

## <a name="resource-limits-issues"></a>Problemi relativi ai limiti delle risorse

Rallentamento delle prestazioni delle query non correlate a piani di query non ottimali e indici mancanti sono in genere correlati a risorse insufficienti o sovrautilizzate. Se il piano di query è ottimale, la query (e il database) potrebbero raggiungere i limiti di risorse per il database, il pool elastico o l'istanza gestita. Un esempio potrebbe essere l'eccesso di velocità effettiva di scrittura del log per il livello di servizio.

- Rilevamento dei problemi delle risorse tramite il portale di Azure: per verificare se il problema è dovuto ai limiti delle risorse, vedere [Monitoraggio delle risorse del database SQL.](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring) Per singoli database e pool elastici, vedere Consigli sulle prestazioni di [Database Advisor](sql-database-advisor.md) e Query Performance [Insights](sql-database-query-performance.md).
- Rilevamento dei limiti delle risorse tramite [Intelligent InsightsDetecting resource](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) limits using Intelligent Insights
- Rilevamento dei problemi di risorse tramite [DMV:](sql-database-monitoring-with-dmvs.md)

  - La DMV [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce CPU, I/O e consumo di memoria per un database SQL. Esiste una riga per ogni intervallo di 15 secondi, anche se non è presente alcuna attività nel database. I dati cronologici vengono mantenuti per un'ora.
  - La [DMV sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce l'utilizzo della CPU e i dati di archiviazione per il database SQL di Azure.The sys.resource_stats DMV returns CPU usage and storage data for Azure SQL Database. I dati vengono raccolti e aggregati a intervalli di cinque minuti.
  - [Numerose singole query hanno un utilizzo cumulativo elevato della CPU](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Se si identifica il problema come risorsa insufficiente, è possibile aggiornare le risorse per aumentare la capacità del database SQL per assorbire i requisiti della CPU. Per altre informazioni, vedere [Scalare singole risorse di database nel database SQL](sql-database-single-database-scale.md) di Azure e Scalare le risorse del pool elastico nel database SQL di Azure.For more information, see Scale single database resources in Azure SQL Database and [Scale elastic pool resources in Azure SQL Database.](sql-database-elastic-pool-scale.md) Per informazioni sulla scalabilità di un'istanza gestita, vedere Limiti delle risorse a livello di [servizio.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemi di prestazioni causati da un aumento del volume del carico di lavoroPerformance problems caused by a increased workload volume

Un aumento del traffico delle applicazioni e del volume del carico di lavoro può causare un aumento dell'utilizzo della CPU. Ma è necessario fare attenzione a diagnosticare correttamente questo problema. Quando viene visualizzato un problema di CPU elevata, rispondere a queste domande per determinare se l'aumento è causato dalle modifiche al volume del carico di lavoro:When you see a high-CPU problem, answer these questions to determine whether the increase is caused by changes to the workload volume:

- Le query dell'applicazione sono la causa del problema di alta CPU?
- Per le principali query che utilizzano la [CPU che è possibile identificare:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Più piani di esecuzione sono stati associati alla stessa query? Se sì, perché?
  - Per le query con lo stesso piano di esecuzione, i tempi di esecuzione sono stati coerenti? Il numero di esecuzioni è aumentato? In tal caso, l'aumento del carico di lavoro causa probabilmente problemi di prestazioni.

In sintesi, se il piano di esecuzione delle query non è stato eseguito in modo diverso ma l'utilizzo della CPU è aumentato insieme al conteggio dell'esecuzione, il problema di prestazioni è probabilmente correlato a un aumento del carico di lavoro.

Non è sempre facile identificare una modifica del volume del carico di lavoro che determina un problema della CPU. Tenere presente questi fattori:

- **Utilizzo delle risorse modificato:** si consideri ad esempio uno scenario in cui l'utilizzo della CPU è aumentato all'80% per un periodo di tempo prolungato. L'utilizzo della CPU da solo non significa che il volume del carico di lavoro sia cambiato. Le regressioni nel piano di esecuzione delle query e le modifiche nella distribuzione dei dati possono anche contribuire a un maggiore utilizzo delle risorse anche se l'applicazione esegue lo stesso carico di lavoro.

- **L'aspetto di una nuova query**: un'applicazione potrebbe guidare un nuovo set di query in momenti diversi.

- **Un aumento o una diminuzione del numero di richieste**: Questo scenario è la misura più ovvia di un carico di lavoro. Il numero di query non corrisponde sempre a un maggiore utilizzo delle risorse. Tuttavia, questa metrica è ancora un segnale significativo, supponendo che altri fattori siano invariati.

Usare Intelligent Insights per rilevare [gli aumenti del carico](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) di lavoro e [pianificare le regressioni.](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>Problemi legati all'attesa

Dopo aver eliminato un piano non ottimale e problemi *correlati all'attesa* correlati a problemi di esecuzione, il problema di prestazioni è in genere in genere le query sono probabilmente in attesa di alcune risorse. I problemi legati all'attesa possono essere causati da:

- **Blocco**:

  Una query potrebbe bloccare gli oggetti nel database mentre altri tentano di accedere agli stessi oggetti. È possibile identificare le query di blocco utilizzando [DMV](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) o [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#locking).
- **Problemi di I/O**

  Le query potrebbero essere in attesa che le pagine vengano scritte nei file di dati o di log. In questo caso, `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG`controllare `PAGEIOLATCH_*` le statistiche , o attendere nella DMV. Vedere Utilizzo delle DMV per identificare i problemi di [prestazioni di I/O](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemi di TempDB**

  Se il carico di lavoro utilizza tabelle temporanee o si verificano perdite di TempDB nei piani, le query potrebbero avere un problema con la velocità effettiva di TempDB. Vedere Utilizzo delle DMV per [identificare i problemi di TempDB](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemi relativi alla memoria**

  Se il carico di lavoro non dispone di memoria sufficiente, l'aspettativa di vita della pagina potrebbe diminuire o le query potrebbero ottenere meno memoria del necessario. In alcuni casi, l'intelligenza incorporata in Query Optimizer risolverà i problemi relativi alla memoria. Vedere Utilizzo delle DMV per identificare i problemi relativi alla concessione di [memoria.](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

### <a name="methods-to-show-top-wait-categories"></a>Metodi per mostrare le principali categorie di attesa

Questi metodi sono comunemente utilizzati per mostrare le principali categorie di tipi di attesa:

- Usare Intelligent Insights per identificare le query con riduzione delle prestazioni a causa di un aumento delle [attese](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Usare [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per trovare le statistiche di attesa per ogni query nel tempo. In Query Store, i pi di attesa vengono combinati in categorie di attesa. È possibile trovare il mapping delle categorie di attesa per attendere i tipi in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilizzare [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) per restituire informazioni su tutte le attese rilevate dai thread eseguiti durante un'operazione di query. È possibile usare questa vista aggregata per diagnosticare problemi di prestazioni con il database SQL di Azure e anche con query e batch specifici. Le query possono essere in attesa di risorse, attese in coda o attese esterne.
- Utilizzare [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) per restituire informazioni sulla coda di attività in attesa di una risorsa.

Negli scenari con CPU elevata, le statistiche dell'archivio query e dell'attesa potrebbero non riflettere l'utilizzo della CPU se:In high-CPU scenarios, Query Store and wait statistics might not reflect CPU usage if:

- Le query che consumano CPU elevata sono ancora in esecuzione.
- Le query che utilizzano una CPU elevata erano in esecuzione quando si è verificato un failover.

Le dMV che tengono traccia delle statistiche di Query Store e di attesa mostrano i risultati solo per le query completate e stime completate correttamente. Non mostrano i dati per le istruzioni attualmente in esecuzione fino al completamento delle istruzioni. Utilizzare la visualizzazione a gestione dinamica [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per tenere traccia delle query attualmente in esecuzione e del tempo di lavoro associato.

> [!TIP]
> Strumenti aggiuntivi:
>
> - [TigerToolbox aspetta e si ferma](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [usp_whatsup di TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del monitoraggio e dell'ottimizzazione del database SQLSQL Database monitoring and tuning overview](sql-database-monitor-tune-overview.md)