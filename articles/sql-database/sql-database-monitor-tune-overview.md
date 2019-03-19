---
title: Monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure | Microsoft Docs
description: Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ac87ce2198296b82ef5655d7d75443a0bd49df3c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875139"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Database SQL di Azure è un servizio di dati flessibile e gestito automaticamente che consente di monitorare l'utilizzo, aggiungere o rimuovere facilmente risorse (CPU, memoria, I/O), trovare indicazioni per migliorare le prestazioni del database o lasciare che il database si adatti al carico di lavoro e ottimizzi automaticamente le prestazioni.

## <a name="monitoring-database-performance"></a>Monitoraggio delle prestazioni del database

Il monitoraggio delle prestazioni di un database SQL in Azure inizia con il monitoraggio dell'utilizzo delle risorse rispetto al livello di prestazioni scelto per il database. Database SQL di Azure consente di individuare le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando le [indicazioni per ottimizzare le prestazioni](sql-database-advisor.md). Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare queste indicazioni per migliorare le prestazioni del carico di lavoro. È anche possibile impostare il database SQL di Azure in modo che [ottimizzi automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le indicazioni identificate e verificando l'effettivo miglioramento delle prestazioni del database.

Per il monitoraggio e la risoluzione dei problemi di prestazioni del database sono disponibili le opzioni seguenti:

- Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
- Usare [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per identificare le query che consumano la maggior parte delle risorse.
- Usare [Advisor per database SQL ](sql-database-advisor-portal.md) per visualizzare le indicazioni per creare e rimuovere indici, parametrizzare le query e correggere i problemi di schema.
- Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per il monitoraggio automatico delle prestazioni del database. Dopo aver rilevato un problema di prestazioni, viene generato un log di diagnostica con i dettagli e l'analisi della causa radice del problema. Quando possibile viene fornita un'indicazione di miglioramento delle prestazioni.
- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) e consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni identificati.
- Usare le [viste a gestione dinamica (DMV)](sql-database-monitoring-with-dmvs.md), gli [eventi estesi](sql-database-xevent-db-diff-from-svr.md) e [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per la risoluzione più dettagliata dei problemi di prestazioni.

> [!TIP]
> Vedere le [indicazioni relative alle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche che è possibile usare per migliorare le prestazioni del database SQL di Azure dopo aver identificato il problema di prestazioni con uno o più metodi sopra elencati.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorare i database tramite il portale di Azure

Nel [portale di Azure](https://portal.azure.com/), è possibile monitorare un utilizzo di singoli database s selezionandolo e scegliendo il **monitoraggio** grafico. Verrà visualizzata una finestra della **metrica** in cui è possibile apportare modifiche facendo clic su l pulsante **Modifica grafico**. Aggiungere le metriche seguenti

- Percentuale CPU
- Percentuale di DTU
- Percentuale di I/O di dati
- Percentuale di dimensioni del database

Dopo aver aggiunto queste metriche, è possibile continuare a visualizzarle nel grafico **Monitoraggio** con altre informazioni nella finestra **Metrica**. Tutte le quattro metriche mostrano la percentuale media di utilizzo relativa alla **DTU** del database. Per altre informazioni sui livelli di servizio, vedere gli articoli [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).  

![Monitoraggio del livello di servizio delle prestazioni del database.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

È inoltre possibile configurare gli avvisi sulle metriche delle prestazioni. Scegliere il pulsante **Aggiungi avviso** nella finestra **Metrica**. Seguire la procedura guidata per configurare l'avviso. È possibile scegliere di ricevere un avviso se la metrica supera una determinata soglia o scende al di sotto di una determinata soglia.

Ad esempio, se si prevede un aumento del carico di lavoro sul database, è possibile scegliere di configurare un avviso di posta elettronica ogni volta che il database raggiunge l'80% per una qualsiasi delle metriche delle prestazioni. È possibile usarlo come un preavviso per capire quando potrebbe essere necessario passare alla dimensione di calcolo superiore.

Le metriche delle prestazioni consentono inoltre di determinare se è possibile effettuare il downgrade a una dimensione di calcolo inferiore. Presupporre di utilizzare un database Standard S2 e tutte le metriche delle prestazioni mostrano che il database in media non utilizza più del 10% in un dato momento. È probabile che il database funzioni bene in Standard S1. Tuttavia, prestare attenzione ai picchi o alle fluttuazioni dei carichi di lavoro prima di decidere di passare a una dimensione di calcolo inferiore.

## <a name="troubleshoot-performance-issues"></a>Risolvere i problemi di prestazioni

Per diagnosticare e risolvere i problemi di prestazioni, iniziare analizzando lo stato di ogni query attive e le condizioni che causano problemi di prestazioni pertinenti a ogni stato del carico di lavoro. Per migliorare le prestazioni del database SQL di Azure, verificare che ogni richiesta di query attiva dall'applicazione sia in stato di attesa o in esecuzione. Durante la risoluzione dei problemi di prestazioni nel database SQL di Azure, tenere presente il grafico seguente durante la lettura dell'articolo per diagnosticare e risolvere i problemi di prestazioni.

![Stati del carico di lavoro](./media/sql-database-monitor-tune-overview/workload-states.png)

Per un carico di lavoro con problemi di prestazioni, il problema di prestazioni potrebbe dipendere da una contesa di CPU (una condizione **correlata all'esecuzione**) o dalle singole query in attesa (condizione **correlata all'attesa**).

## <a name="running-related-performance-issues"></a>Problemi di prestazioni correlati all'esecuzione

Come regola generale, se l'utilizzo della CPU è costantemente pari o superiore all'80%, è presente un problema di prestazioni correlato all'esecuzione. Se si riscontra un problema correlato all'esecuzione, questo potrebbe essere causato da risorse di CPU insufficienti o può essere correlato a una delle condizioni seguenti:

- Numero eccessivo di query in esecuzione
- Numero eccessivo di query in fase di compilazione
- Una o più query in esecuzione usano un piano di query non ottimale

Se si determina che è presente un problema di prestazioni correlato all'esecuzione, l'obiettivo consiste nell'identificare il problema preciso usando uno o più metodi. I metodi più comuni per identificare i problemi di esecuzione sono:

- Usare il [portale di Azure](#monitor-databases-using-the-azure-portal) per monitorare l'utilizzo percentuale della CPU.
- Usare le seguenti [visualizzazioni a gestione dinamica](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce il consumo di CPU, I/O e memoria per un database SQL di Azure. È presente una riga ogni 15 secondi, anche se non c'è attività di database. I dati cronologici vengono conservati per un'ora.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce i dati di archiviazione e di uso della CPU per un database SQL di Azure. I dati vengono raccolti e aggregati per intervalli di cinque minuti.

> [!IMPORTANT]
> Per un set di una query T-SQL che usa queste DMV per risolvere i problemi relativi all'utilizzo della CPU, vedere [Identificare i problemi di prestazioni della CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>Risolvere i problemi relativi ai piani di esecuzione delle query sensibili ai parametri

Il problema del piano sensibile ai parametri si riferisce a uno scenario in cui Query Optimizer genera un piano di esecuzione di query ottimale solo per un valore di parametro specifico (un o set di valori) e il piano memorizzato nella cache non è quindi ottimale per i valori dei parametri usati in esecuzioni consecutive. I piani non ottimali possono quindi comportare problemi di prestazioni delle query e ridurre la velocità effettiva complessiva dei carichi di lavoro.

Esistono diverse soluzioni alternative per attenuare i problemi, ognuna delle quali comporta compromessi e svantaggi:

- Usare l'hint per la query [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) a ogni esecuzione di query. Questa soluzione alternativa offre tempi di compilazione ridotti e una maggiore quantità di CPU per una migliore qualità del piano. L'uso dell'opzione `RECOMPILE` spesso non è possibile per i carichi di lavoro che richiedono una velocità effettiva elevata.
- Usare l'hint per la query [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo con un valore di parametro tipico che genera un piano adeguato per la maggior parte dei valori di parametro possibili.   Questa opzione richiede una buona conoscenza dei valori di parametro ottimali e delle caratteristiche del piano associate.
- Usare l'hint per la query [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo invece di usare la media del vettore di densità. A questo scopo è anche possibile acquisire i valori dei parametri in entrata in variabili locali e quindi usare le variabili locali all'interno dei predicati invece di usare i parametri stessi. La densità media deve essere *adeguata* a questa particolare correzione.
- Disabilitare completamente l'analisi dei parametri usando l'hint per la query [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Usare l'hint per la query [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per evitare la ricompilazione nella cache. Questa soluzione alternativa presuppone che il piano comune *adeguato* sia quello già nella cache. È anche possibile disabilitare gli aggiornamenti automatici alle statistiche per ridurre le probabilità che il piano valido venga rimosso e che venga compilato un nuovo piano non valido.
- Forzare il piano usando in modo esplicito l'hint per la query [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (specificandolo in modo esplicito, impostando un piano specifico con Query Store o abilitando l'[ottimizzazione automatica](sql-database-automatic-tuning.md)).
- Sostituire la procedura singola con un set annidato di procedure, ognuna delle quali può essere usata in base alla logica condizionale e ai valori dei parametri associati.
- Creare alternative basate sull'esecuzione di stringhe dinamiche per una definizione di procedura statica.

Per altre informazioni sulla risoluzione di questi tipi di problemi, vedere:

- Post di blog sull'[analisi di un parametro](https://blogs.msdn.microsoft.com/queryoptteam/20../../i-smell-a-parameter/)
- Post di blog sul [problema di analisi dei parametri e soluzioni alternative](https://blogs.msdn.microsoft.com/turgays/20../../parameter-sniffing-problem-and-possible-workarounds/)
- Post di blog [sull'analisi dei parametri per il problema dell'elefante e del topo](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/)
- Post di blog su [SQL dinamico e la qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/20../../conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Risoluzione dei problemi relativi all'attività di compilazione a causa della parametrizzazione non corretta

Quando una query contiene valori letterali, il motore di database sceglie di impostare automaticamente i parametri dell'istruzione oppure un utente può impostarne i parametri in modo esplicito per ridurre il numero di compilazioni. Un numero elevato di compilazioni di una query con lo stesso modello, ma valori letterali diversi può causare un utilizzo elevato della CPU. Analogamente, se si impostano solo parzialmente i parametri di una query che continua a includere valori letterali, il motore di database non imposta gli altri parametri.  Di seguito è riportato un esempio di una query con solo alcuni parametri impostati:

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Nell'esempio precedente `t1.c1` usa `@p1`, ma `t2.c2` continua a usare il GUID come valore letterale. In questo caso, se si modifica il valore per `c2`, la query verrà considerata come una query diversa e verrà eseguita una nuova compilazione. Per ridurre le compilazioni nell'esempio precedente, la soluzione consiste nell'impostare i parametri anche del GUID.

La query seguente illustra il conteggio delle query per hash di query per determinare se i parametri di una query sono impostati correttamente o meno:

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

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Risolvere le query problematiche o fornire altre risorse

Dopo avere identificato il problema, è possibile ottimizzare le query problematiche oppure aggiornare le dimensioni di calcolo o il livello di servizio per aumentare la capacità del database SQL di Azure per assorbire i requisiti della CPU. Per informazioni sul ridimensionamento delle risorse per i singoli database, vedere [Ridimensionare le risorse di database singoli nel database SQL di Azure](sql-database-single-database-scale.md) e per il ridimensionamento delle risorse per i pool elastici, vedere [Ridimensionare le risorse dei pool elastici nel database SQL di Azure](sql-database-elastic-pool-scale.md). Per informazioni su come ridimensionare un'istanza gestita, vedere [Limiti delle risorse a livello di istanza](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Determinare se i problemi di esecuzione sono dovuti all'aumento del volume del carico di lavoro

Un aumento del carico di lavoro e del traffico dell'applicazione può causare un maggiore utilizzo della CPU, ma è necessario prestare attenzione e diagnosticare il problema in modo corretto. In uno scenario con utilizzo elevato della CPU, rispondere a queste domande per determinare se un aumento della CPU è effettivamente dovuto a modifiche apportate al volume del carico di lavoro:

1. Le query eseguite dall'applicazione sono la causa del problema di utilizzo elevato della CPU?
2. Per le query con utilizzo più elevato della CPU (che possono essere identificate):

   - Determinare se erano presenti più piani di esecuzione associati alla stessa query. In questo caso, determinarne il motivo.
   - Per le query con lo stesso piano di esecuzione, determinare se i tempi di esecuzione erano coerenti e se il numero di esecuzioni è aumentato. In caso affermativo, è probabile che siano presenti problemi di prestazioni a causa dell'aumento del carico di lavoro.

Per riepilogare, se il piano di esecuzione della query non è stato eseguito in modo diverso, ma l'utilizzo della CPU è aumentato con il numero di esecuzioni, è probabile che esista un problema di prestazioni correlato all'aumento del carico di lavoro.

Non è sempre facile concludere che è una modifica del volume del carico di lavoro a determinare un problema di CPU.   Fattori da valutare: 

- **Modifica dell'utilizzo delle risorse**

  Si consideri, ad esempio, uno scenario in cui l'utilizzo della CPU è aumentato fino all'80% per un lungo periodo di tempo.  L'utilizzo della CPU in sé non significa che il volume di carico di lavoro sia cambiato.  Anche le regressioni del piano di esecuzione di una query e le modifiche alla distribuzione dei dati possono contribuire al maggior utilizzo di risorse anche se l'applicazione esegue lo stesso identico carico di lavoro.

- **Esecuzione di una nuova query**

   Un'applicazione può gestire un nuovo set di query in momenti diversi.

- **Aumento o diminuzione del numero di richieste**

   Questo scenario è la misura più ovvia del carico di lavoro. Il numero di query non corrisponde sempre a un maggiore utilizzo delle risorse. Tuttavia, questa metrica rimane un segnale significativo, supponendo che gli altri fattori restino invariati.

## <a name="waiting-related-performance-issues"></a>Problemi di prestazioni relativi all'attesa

Una volta che si è certi di non essere in presenza di un problema di prestazioni correlato all'esecuzione per l'uso elevato della CPU, si dovrà considerare un problema di prestazioni correlato all'attesa. In particolare, le risorse della CPU non sono usate in modo efficiente perché la CPU è in attesa di un'altra risorsa. In questo caso, il passaggio successivo consiste nell'identificare di cosa sono in attesa le risorse della CPU. I metodi più comuni per la mostrare le tre prime categorie dei tipi di attesa sono:

- Il [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fornisce le statistiche di attesa per ogni query nel tempo. In Query Store, i pi di attesa vengono combinati in categorie di attesa. Il mapping delle categorie di attesa ai tipi di attesa è disponibile in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) restituisce informazioni su tutte le attese incontrate dai thread eseguiti durante l'operazione. È possibile utilizzare questa visualizzazione aggregata per diagnosticare i problemi di prestazioni con il database SQL di Azure e anche con query e batch specifici.
- [DM os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) restituisce informazioni relative alla coda di attesa dell'attività in attesa su alcune risorse.

Negli scenari con utilizzo elevato della CPU, Query Store e le statistiche di attesa non riflettono sempre l'utilizzo della CPU per questi due motivi:

- È possibile che siano ancora in esecuzione query con utilizzo elevato della CPU e che le query non siano state completate
- Le query con utilizzo elevato della CPU erano in esecuzione quando si è verificato un failover

Query Store e le viste DMV di rilevamento delle statistiche di attesa mostrano solo i risultati delle query completate e di cui si è verificato il timeout e non mostrano i dati per le istruzioni attualmente in esecuzione (finché non vengono completate).  La vista DMV [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) consente di tenere traccia delle query attualmente in esecuzione e della durata del ruolo di lavoro associato.

Come illustrato nel grafico precedente, le attese più comuni sono:

- Blocchi (blocco)
- I/O
- Contenuto correlato a `tempdb`
- Attese di concessione di memoria

> [!IMPORTANT]
> Per un set di query T-SQL che usa queste DMV per risolvere i problemi relativi all'attesa, vedere:
>
> - [Identificare i problemi di prestazioni di IO](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificare `tempdb` i problemi di prestazioni](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificare le attese di concessione di memoria](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

## <a name="improving-database-performance-with-more-resources"></a>Miglioramento delle prestazioni del database con più risorse

Se alla fine non esistono elementi eseguibili che possono migliorare le prestazioni del database, è possibile modificare la quantità di risorse disponibili nel database SQL di Azure. È possibile assegnare più risorse modificando il [livello di servizio DTU](sql-database-service-tiers-dtu.md) di un database singolo o aumentare il numero di eDTU di un pool elastico in qualsiasi momento. In alternativa, se si usa il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) è possibile modificare il livello di servizio o aumentare le risorse allocate al database.

1. Per i database singoli, è possibile [modificare i livelli di servizio](sql-database-single-database-scale.md) o le [risorse di calcolo](sql-database-single-database-scale.md) su richiesta per migliorare le prestazioni del database.
2. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

## <a name="tune-and-refactor-application-or-database-code"></a>Ottimizzare ed eseguire il refactoring del codice del database o dell'applicazione

È possibile modificare il codice dell'applicazione per usare il database in modo più ottimale, modificare gli indici, forzare i piani o usare i suggerimenti per adattare manualmente il database al carico di lavoro. Alcuni suggerimenti e indicazioni per l'ottimizzazione manuale e la riscrittura del codice sono disponibili nell'[argomento relativo alle indicazioni sulle prestazioni](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.
- Modificare le risorse disponibili nel database modificando i [livelli di servizio Database SQL di Azure](sql-database-performance-guidance.md)
