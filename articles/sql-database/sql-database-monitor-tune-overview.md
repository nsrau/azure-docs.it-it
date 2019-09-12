---
title: Monitoraggio e ottimizzazione delle prestazioni-database SQL di Azure | Microsoft Docs
description: Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 83ff39e9f3b7f95256466c74011e55ebdc22a7a9
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910520"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Il database SQL di Azure offre strumenti e metodi che è possibile usare per monitorare facilmente l'utilizzo, aggiungere o rimuovere risorse (ad esempio CPU, memoria o I/O), risolvere i problemi potenziali e apportare raccomandazioni per migliorare le prestazioni di un database. Le funzionalità del database SQL di Azure possono correggere automaticamente i problemi nei database. 

L'ottimizzazione automatica consente a un database di adattarsi al carico di lavoro e ottimizzare automaticamente le prestazioni. Alcuni problemi personalizzati potrebbero tuttavia richiedere la risoluzione dei problemi. Questo articolo illustra alcune procedure consigliate e alcuni strumenti che è possibile usare per risolvere i problemi relativi alle prestazioni.

Per assicurarsi che un database venga eseguito senza problemi, è necessario:
- [Monitorare le prestazioni del database](#monitor-database-performance) per assicurarsi che le risorse assegnate al database siano in grado di gestire il carico di lavoro. Se il database sta raggiungendo i limiti delle risorse, prendere in considerazione quanto segue:
   - Identificazione e ottimizzazione delle prime query che utilizzano risorse.
   - Aggiunta di altre risorse tramite [l'aggiornamento del livello di servizio](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Risolvere i problemi](#troubleshoot-performance-problems) relativi alle prestazioni per identificare il motivo per cui si è verificato un problema potenziale e identificare la causa principale del problema. Una volta identificata la causa principale, adottare le misure necessarie per risolvere il problema.

## <a name="monitor-database-performance"></a>Monitorare le prestazioni del database

Per monitorare le prestazioni di un database SQL in Azure, iniziare monitorando le risorse usate in relazione al livello di prestazioni del database scelto. Monitorare le risorse seguenti:
 - **Utilizzo CPU**: Verificare se il database sta raggiungendo il 100% dell'utilizzo della CPU per un lungo periodo di tempo. Un utilizzo elevato della CPU potrebbe indicare che è necessario identificare e ottimizzare le query che utilizzano la potenza di calcolo maggiore. Un utilizzo elevato della CPU potrebbe indicare anche che l'istanza o il database deve essere aggiornato a un livello di servizio superiore. 
 - **Statistiche attesa**: Utilizzare [sys. dm _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) per determinare il tempo di attesa delle query. Le query possono essere in attesa di risorse, attese di accodamento o attese esterne. 
 - **Utilizzo io**: Verificare se il database sta raggiungendo i limiti di i/o dell'archiviazione sottostante.
 - **Utilizzo memoria**: La quantità di memoria disponibile per il database o l'istanza è proporzionale al numero di vcore. Verificare che la memoria sia sufficiente per il carico di lavoro. La permanenza presunta della pagina è uno dei parametri che possono indicare la velocità con cui le pagine vengono rimosse dalla memoria.

Il servizio database SQL di Azure include strumenti e risorse che consentono di risolvere i problemi relativi alle prestazioni potenziali. È possibile identificare le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando le raccomandazioni per l' [ottimizzazione delle prestazioni](sql-database-advisor.md). 

Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare le indicazioni di ottimizzazione per migliorare le prestazioni del carico di lavoro. È anche possibile consentire al database SQL di Azure di [ottimizzare automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le raccomandazioni identificate. Quindi verificare che le raccomandazioni abbiano migliorato le prestazioni del database.

> [!NOTE]
> L'indicizzazione è disponibile solo nel database singolo e nei pool elastici. L'indicizzazione non è disponibile in un'istanza gestita.

Scegliere una delle opzioni seguenti per monitorare e risolvere i problemi relativi alle prestazioni del database:

- Nella [portale di Azure](https://portal.azure.com)selezionare **database SQL** e selezionare il database. Nel grafico di **monitoraggio** cercare le risorse che si avvicinano al relativo utilizzo massimo. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Selezionare **modifica** per modificare l'intervallo di tempo e i valori visualizzati.
- Strumenti come SQL Server Management Studio forniscono molti report utili, come il [Dashboard delle prestazioni](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Usare questi report per monitorare l'utilizzo delle risorse e identificare le prime query che utilizzano risorse. È possibile utilizzare [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) per identificare le query le cui prestazioni sono state regressioni.
- Nel [portale di Azure](https://portal.azure.com)usare [informazioni dettagliate prestazioni query](sql-database-query-performance.md) per identificare le query che usano la maggior parte delle risorse. Questa funzionalità è disponibile solo nel database singolo e nei pool elastici.
- Usare [SQL Advisor per database](sql-database-advisor-portal.md) per visualizzare le raccomandazioni che consentono di creare ed eliminare indici, parametrizzare query e correggere i problemi dello schema. Questa funzionalità è disponibile solo nel database singolo e nei pool elastici.
- Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per monitorare automaticamente le prestazioni del database. Quando viene rilevato un problema di prestazioni, viene generato un log di diagnostica. Il log fornisce i dettagli e un'analisi della causa radice (RCA) del problema. Quando possibile, viene fornita una raccomandazione per il miglioramento delle prestazioni.
- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) per consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni.
- Utilizzare le [viste a gestione dinamica (DMV)](sql-database-monitoring-with-dmvs.md), [gli eventi estesi](sql-database-xevent-db-diff-from-svr.md)e [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per informazioni più dettagliate sulla risoluzione dei problemi relativi alle prestazioni.

> [!TIP]
> Dopo aver identificato un problema di prestazioni, vedere le [indicazioni sulle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche per migliorare le prestazioni del database SQL di Azure.

## <a name="troubleshoot-performance-problems"></a>Risoluzione dei problemi di prestazioni

Per diagnosticare e risolvere i problemi relativi alle prestazioni, è necessario innanzitutto individuare lo stato di ogni query attiva e le condizioni che provocano problemi di prestazioni rilevanti per ogni stato del carico di lavoro. Per migliorare le prestazioni del database SQL di Azure, è necessario comprendere che ogni richiesta di query attiva dall'applicazione si trova nello stato in esecuzione o in attesa. Quando si risolve un problema di prestazioni nel database SQL di Azure, tenere presente il diagramma seguente.

![Stati del carico di lavoro](./media/sql-database-monitor-tune-overview/workload-states.png)

Un problema di prestazioni in un carico di lavoro può essere causato da una contesa di CPU (condizione *correlata all'esecuzione* ) o da singole query in attesa di qualcosa (condizione *relativa all'attesa* ).

I problemi correlati all'esecuzione potrebbero essere causati da:
- **Problemi di compilazione**: SQL Query Optimizer potrebbe produrre un piano non ottimale a causa di statistiche non aggiornate, una stima non corretta del numero di righe da elaborare o una stima non accurata della memoria necessaria. Se si sa che la query è stata eseguita più velocemente in passato o in un'altra istanza (un'istanza gestita o un'istanza di SQL Server), confrontare i piani di esecuzione effettivi per verificare se sono diversi. Provare ad applicare gli hint per la query o a ricompilare statistiche o indici per ottenere il piano migliore. Abilitare la correzione automatica dei piani nel database SQL di Azure per attenuare automaticamente questi problemi.
- **Problemi di esecuzione**: Se il piano di query è ottimale, è probabile che raggiunga i limiti delle risorse del database, ad esempio la velocità effettiva di scrittura del log. In alternativa, è possibile che vengano utilizzati indici frammentati che devono essere ricompilati. I problemi di esecuzione possono verificarsi anche quando un numero elevato di query simultanee necessita delle stesse risorse. I problemi *relativi alle attese* sono in genere correlati a problemi di esecuzione, perché le query che non vengono eseguite in modo efficiente sono probabilmente in attesa di alcune risorse.

I problemi relativi alle attese potrebbero essere causati da:
- **Blocco**: Una query può mantenere il blocco sugli oggetti nel database mentre altri tentano di accedere agli stessi oggetti. È possibile identificare le query di blocco usando DMV o gli strumenti di monitoraggio.
- **Problemi di io**: È possibile che le query attendano la scrittura delle pagine nei file di dati o di log. In tal caso, controllare le `INSTANCE_LOG_RATE_GOVERNOR`statistiche `WRITE_LOG`di attesa `PAGEIOLATCH_*` , o nella DMV.
- **Problemi di tempdb**: Se il carico di lavoro utilizza tabelle temporanee o si verifica un problema di TempDB nei piani, è possibile che si verifichi un problema con la velocità effettiva di TempDB. 
- **Problemi relativi alla memoria**: Se il carico di lavoro non dispone di memoria sufficiente, la permanenza presunta delle pagine potrebbe essere eliminata o le query potrebbero ottenere meno memoria del necessario. In alcuni casi, l'Intelligence incorporata in Query Optimizer risolverà i problemi correlati alla memoria.
 
Le sezioni seguenti illustrano come identificare e risolvere alcuni tipi di problemi.

## <a name="performance-problems-related-to-running"></a>Problemi di prestazioni correlati all'esecuzione

Come linea guida generale, se l'utilizzo della CPU è costantemente pari o superiore al 80%, il problema di prestazioni è correlato all'esecuzione. Un problema correlato all'esecuzione può essere causato da risorse CPU insufficienti. In alternativa, potrebbe essere correlato a una delle seguenti condizioni:

- Numero eccessivo di query in esecuzione
- Numero eccessivo di query in fase di compilazione
- Una o più query in esecuzione che utilizzano un piano di query non ottimale

Se si rileva un problema di prestazioni correlato all'esecuzione, l'obiettivo consiste nell'identificare il problema preciso utilizzando uno o più metodi. Questi metodi sono i modi più comuni per identificare i problemi relativi all'esecuzione:

- Usare il [portale di Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) per monitorare l'utilizzo della percentuale di CPU.
- Usare il [DMV](sql-database-monitoring-with-dmvs.md)seguente:

  - La DMV [sys. dm _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce CPU, i/O e utilizzo di memoria per un database SQL. È presente una riga per ogni intervallo di 15 secondi, anche se non è presente alcuna attività nel database. I dati cronologici vengono conservati per un'ora.
  - La DMV [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce i dati di archiviazione e di utilizzo della CPU per il database SQL di Azure. I dati vengono raccolti e aggregati in intervalli di cinque minuti.

> [!IMPORTANT]
> Per risolvere i problemi di utilizzo della CPU per le query T-SQL che usano sys. dm _db_resource_stats e sys. resource_stats DMV, vedere [identificare i problemi di prestazioni della CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Query con problemi di PSP

Un problema relativo al piano con distinzione tra parametri (PSP) si verifica quando il Query Optimizer genera un piano di esecuzione della query ottimale solo per un valore di parametro specifico (o set di valori) e il piano memorizzato nella cache non è ottimale per i valori dei parametri utilizzati in consecutivi. esecuzioni. I piani che non sono ottimali possono causare problemi di prestazioni delle query e peggiorare la velocità effettiva complessiva del carico di lavoro. 

Per ulteriori informazioni sull'analisi dei parametri e sull'elaborazione di query, vedere la [Guida sull'architettura di elaborazione delle query](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Diverse soluzioni alternative possono mitigare i problemi relativi a PSP. Per ogni soluzione alternativa sono stati associati compromessi e svantaggi:

- Usare l'hint per la query [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) a ogni esecuzione di query. Questa soluzione alternativa offre tempi di compilazione ridotti e una maggiore quantità di CPU per una migliore qualità del piano. L' `RECOMPILE` opzione non è spesso possibile per i carichi di lavoro che richiedono una velocità effettiva elevata.
- Utilizzare l'hint per la query [Option (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo con un valore di parametro tipico che produce un piano sufficientemente adatto per la maggior parte delle possibilità di valore del parametro. Questa opzione richiede una buona conoscenza dei valori di parametro ottimali e delle caratteristiche del piano associate.
- Utilizzare l'hint per la query [Option (Optimize for Unknown)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo e utilizzare invece la media del vettore di densità. È anche possibile eseguire questa operazione acquisendo i valori dei parametri in ingresso nelle variabili locali e quindi usando le variabili locali all'interno dei predicati invece di usare i parametri stessi. Per questa correzione, la densità media deve essere *sufficientemente adeguata*.
- Disabilitare completamente lo sniffing dei parametri usando l'hint per la query [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Utilizzare l'hint per la query [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per impedire ricompilazioni nella cache. Questa soluzione si basa sul presupposto che il piano comune sufficientemente valido sia quello nella cache. È anche possibile disabilitare gli aggiornamenti automatici delle statistiche per ridurre le probabilità che venga eliminato il piano valido e che verrà compilato un nuovo piano non valido.
- Forzare il piano in modo esplicito utilizzando l'hint per la query [use Plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) riscrivendo la query e aggiungendo l'hint nel testo della query. In alternativa, impostare un piano specifico utilizzando Query Store o abilitando l' [ottimizzazione automatica](sql-database-automatic-tuning.md).
- Sostituire la procedura singola con un set annidato di procedure, ognuna delle quali può essere usata in base alla logica condizionale e ai valori dei parametri associati.
- Creare alternative basate sull'esecuzione di stringhe dinamiche per una definizione di procedura statica.

Per ulteriori informazioni sulla risoluzione dei problemi relativi a PSP, vedere i post di Blog seguenti:

- [Odore di un parametro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor rispetto a SQL dinamico e procedure rispetto alla qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Tecniche di ottimizzazione delle query SQL in SQL Server: Sniffing dei parametri](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Attività di compilazione causata dalla parametrizzazione non corretta

Quando una query ha valori letterali, il motore di database parametrizza automaticamente l'istruzione o un utente parametrizza in modo esplicito l'istruzione per ridurre il numero di compilazioni. Un numero elevato di compilazioni per una query che usa lo stesso modello, ma valori letterali diversi può comportare un utilizzo elevato della CPU. Analogamente, se si imposta solo parzialmente un parametrizzamento di una query che continua a avere valori letterali, il motore di database non imposta ulteriormente i parametri della query.  

Di seguito è riportato un esempio di una query con parametri parziale:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In questo esempio, `t1.c1` accetta `@p1`, ma `t2.c2` continua a assumere il GUID come valore letterale. In questo caso, se si modifica il valore di `c2`, la query viene considerata come una query diversa e verrà eseguita una nuova compilazione. Per ridurre le compilazioni in questo esempio, è necessario parametrizzare anche il GUID.

La query seguente mostra il numero di query in base all'hash della query per determinare se una query è parametrizzata correttamente:

```sql
SELECT  TOP 10  
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
- La query fa riferimento alle modifiche nello schema.
- La query fa riferimento a modifiche ai dati delle tabelle. 
- Le opzioni del contesto di query sono state modificate.

È possibile che un piano compilato venga espulso dalla cache per diversi motivi, ad esempio:

- Riavvio dell'istanza.
- Modifiche della configurazione con ambito database.
- Pressione della memoria.
- Richieste esplicite di cancellazione della cache.

Se si usa un hint RECOMPILE, un piano non verrà memorizzato nella cache.

Una ricompilazione (o una nuova compilazione dopo la rimozione della cache) può comunque comportare la generazione di un piano di esecuzione della query identico all'originale. Quando il piano viene modificato rispetto al piano precedente o originale, è probabile che queste spiegazioni siano:

- **Progettazione fisica modificata**: Gli indici appena creati, ad esempio, coprono in modo più efficace i requisiti di una query. I nuovi indici possono essere utilizzati in una nuova compilazione se il Query Optimizer decide che l'utilizzo di tale nuovo indice è più ottimale rispetto all'utilizzo della struttura di dati selezionata originariamente per la prima versione dell'esecuzione della query.  Eventuali modifiche fisiche apportate agli oggetti a cui si fa riferimento possono comportare una nuova scelta del piano in fase di compilazione.

- **Differenze tra le risorse del server**: Quando un piano in un sistema differisce dal piano in un altro sistema, la disponibilità delle risorse, ad esempio il numero di processori disponibili, può influenzare il piano generato.  Ad esempio, se un sistema dispone di più processori, è possibile che venga scelto un piano parallelo. 

- **Statistiche diverse**: È possibile che le statistiche associate agli oggetti a cui si fa riferimento siano state modificate o siano materialmente diverse dalle statistiche del sistema originale.  Se le statistiche cambiano e viene eseguita una ricompilazione, il Query Optimizer utilizza le statistiche a partire da quando sono state modificate. Le frequenze e le distribuzioni dei dati delle statistiche rivedute potrebbero essere diverse da quelle della compilazione originale.  Queste modifiche vengono usate per creare stime della cardinalità. (Le*stime della cardinalità* sono il numero di righe che dovrebbero essere propagate attraverso l'albero delle query logiche). Le modifiche alle stime della cardinalità possono comportare la scelta di operatori fisici diversi e gli ordini di operazioni associati.  Anche le modifiche minime alle statistiche possono generare un piano di esecuzione delle query modificato.

- **Modifica del livello di compatibilità del database o della versione di stima della cardinalità**:  Le modifiche apportate al livello di compatibilità del database possono consentire nuove strategie e funzionalità che possono generare un piano di esecuzione di query diverso.  Oltre al livello di compatibilità del database, un flag di traccia disabilitato o abilitato 4199 o uno stato modificato della configurazione con ambito database QUERY_OPTIMIZER_HOTFIXES può anche influenzare le scelte del piano di esecuzione delle query in fase di compilazione.  I flag di traccia 9481 (Force legacy CE) e 2312 (Force default CE) influiscono anche sul piano. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Risolvere le query problematiche o fornire altre risorse

Dopo aver identificato il problema, è possibile ottimizzare le query sul problema oppure aggiornare le dimensioni di calcolo o il livello di servizio per aumentare la capacità del database SQL di assorbire i requisiti della CPU. 

Per altre informazioni, vedere [ridimensionare le risorse di database singolo nel database SQL di Azure](sql-database-single-database-scale.md) e [ridimensionare le risorse del pool elastico nel database SQL di Azure](sql-database-elastic-pool-scale.md). Per informazioni sul ridimensionamento di un'istanza gestita, vedere [limiti delle risorse a livello di istanza](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemi di prestazioni causati dall'aumento del volume del carico di lavoro

Un aumento del traffico e del volume del carico di lavoro dell'applicazione può causare un maggiore utilizzo della CPU. Tuttavia, è necessario prestare attenzione a diagnosticare correttamente questo problema. Quando si verifica un problema di CPU elevato, rispondere a queste domande per determinare se l'aumento è causato da modifiche al volume del carico di lavoro:

- Le query dell'applicazione hanno causato il problema della CPU elevata?
- Per le prime query che utilizzano la CPU che è possibile identificare:

   - Sono stati associati più piani di esecuzione alla stessa query? In tal caso, perché?
   - Per le query con lo stesso piano di esecuzione, i tempi di esecuzione sono coerenti? Il numero di esecuzioni è aumentato? In tal caso, l'aumento del carico di lavoro potrebbe causare problemi di prestazioni.

In breve, se il piano di esecuzione della query non viene eseguito in modo diverso, ma aumenta l'utilizzo della CPU insieme al numero di esecuzioni, il problema di prestazioni è probabilmente correlato all'aumento del carico di lavoro

Non è sempre facile identificare una modifica del volume del carico di lavoro che determina un problema di CPU. Tenere presente questi fattori: 

- **Modifica dell'utilizzo delle risorse**: Si consideri, ad esempio, uno scenario in cui l'utilizzo della CPU è aumentato fino al 80% per un lungo periodo di tempo.  Solo utilizzo CPU non significa che il volume del carico di lavoro è stato modificato. Anche le regressioni nel piano di esecuzione della query e le modifiche nella distribuzione dei dati possono contribuire a un utilizzo più delle risorse anche se l'applicazione esegue lo stesso carico di lavoro.

- **Aspetto di una nuova query**: Un'applicazione può guidare un nuovo set di query in momenti diversi.

- **Aumento o riduzione del numero di richieste**: Questo scenario è la misura più ovvia di un carico di lavoro. Il numero di query non corrisponde sempre a un maggiore utilizzo delle risorse. Tuttavia, questa metrica è ancora un segnale significativo, supponendo che altri fattori siano invariati.

## <a name="waiting-related-performance-problems"></a>Problemi di prestazioni relativi alle attese 

Se si è certi che il problema di prestazioni non è correlato all'utilizzo elevato della CPU o all'esecuzione, il problema è correlato all'attesa. In particolare, le risorse della CPU non vengono usate in modo efficiente perché la CPU è in attesa su un'altra risorsa. In questo caso, identificare le risorse della CPU in attesa. 

Questi metodi vengono comunemente usati per mostrare le categorie principali dei tipi di attesa:

- Usare [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per trovare le statistiche di attesa per ogni query nel tempo. In Query Store, i pi di attesa vengono combinati in categorie di attesa. È possibile trovare il mapping delle categorie di attesa ai tipi di attesa in [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilizzare [sys. dm _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) per restituire informazioni su tutte le attese rilevate dai thread eseguiti durante l'operazione. È possibile usare questa visualizzazione aggregata per diagnosticare problemi di prestazioni con il database SQL di Azure e anche con query e batch specifici.
- Utilizzare [sys. dm _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) per restituire informazioni sulla coda di attività in attesa di una risorsa.

Negli scenari con CPU elevata, Query Store e le statistiche di attesa potrebbero non riflettere l'utilizzo della CPU se:

- Le query con utilizzo elevato di CPU sono ancora in esecuzione.
- Le query con utilizzo elevato della CPU venivano eseguite quando si verificava un failover.

DMV che tengono traccia delle statistiche di attesa e Query Store visualizzano i risultati solo per le query completate correttamente e scadute. Non visualizzano i dati per le istruzioni attualmente in esecuzione fino al completamento delle istruzioni. Utilizzare la vista a gestione dinamica [sys. dm _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per tenere traccia delle query attualmente in esecuzione e del tempo di lavoro associato.

Il grafico vicino all'inizio di questo articolo mostra che le attese più comuni sono:

- Blocchi (blocco)
- I/O
- Contesa correlata a TempDB
- Attese di concessione di memoria

> [!IMPORTANT]
> Per un set di query T-SQL che usano DMV per risolvere i problemi relativi alle attese, vedere:
>
> - [Identificare i problemi di prestazioni di IO](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificare le attese di concessione di memoria](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox attese e latch](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Migliorare le prestazioni del database con più risorse

Se nessun elemento praticabile può migliorare le prestazioni del database, è possibile modificare la quantità di risorse disponibili nel database SQL di Azure. Per assegnare più risorse, modificare il [livello di servizio DTU](sql-database-service-tiers-dtu.md) di un database singolo. In alternativa, aumentare il edtu di un pool elastico in qualsiasi momento. In alternativa, se si usa il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md), modificare il livello di servizio o aumentare le risorse allocate al database.

Per i database singoli, è possibile [modificare i livelli di servizio o le risorse di calcolo](sql-database-single-database-scale.md) su richiesta per migliorare le prestazioni del database. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

## <a name="tune-and-refactor-application-or-database-code"></a>Ottimizzare ed eseguire il refactoring del codice del database o dell'applicazione

È possibile ottimizzare il codice dell'applicazione per il database, modificare gli indici, forzare i piani o usare hint per adattare manualmente il database al carico di lavoro. Per informazioni sull'ottimizzazione manuale e la riscrittura del codice, vedere [linee guida per l'ottimizzazione delle prestazioni](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e lasciare che la funzionalità di ottimizzazione automatica gestisca completamente il carico di lavoro, vedere [abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, rivedere [le indicazioni relative all'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md). Applicare manualmente le indicazioni per migliorare le prestazioni delle query.
- Modificare le risorse disponibili nel database modificando i livelli di [servizio del database SQL di Azure](sql-database-performance-guidance.md).
