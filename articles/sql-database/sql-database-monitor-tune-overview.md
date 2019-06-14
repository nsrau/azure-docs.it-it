---
title: Monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure | Microsoft Docs
description: Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416282"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Risolvere i problemi potenziali di Azure SQL Database consente di monitorare facilmente l'utilizzo, aggiungere o rimuovere risorse (CPU, memoria, i/o) e trovare raccomandazioni utili per migliorare le prestazioni del database. Database SQL di Azure offre numerose funzionalità che consente di correggere automaticamente i problemi dei database se si desidera che i database di adattarsi ai carichi di lavoro e ottimizzi automaticamente le prestazioni. Tuttavia, esistono alcuni problemi personalizzati che potrebbe essere necessario risolvere i problemi. Questo articolo illustra alcune procedure consigliate e strumenti che è possibile usare per risolvere i problemi di prestazioni.

Esistono due attività principali che è necessario eseguire per assicurarsi che tale database è in esecuzione senza problemi:
- [Monitoraggio delle prestazioni del database](#monitoring-database-performance) per assicurarsi che le risorse assegnate al database possono gestire il carico di lavoro. Se viene visualizzato che si raggiungono i limiti delle risorse, è necessario per identificare le query per consumo di risorse superiore e ottimizzarle o aggiungere altre risorse eseguendo l'aggiornamento a livello di servizio.
- [Risolvere i problemi di prestazioni](#troubleshoot-performance-issues) allo scopo di identificare il motivo per cui si è verificato un problema potenziale, identificare la causa radice del problema e l'azione che può risolvere il problema.

## <a name="monitoring-database-performance"></a>Monitoraggio delle prestazioni del database

Il monitoraggio delle prestazioni di un database SQL in Azure inizia con il monitoraggio dell'utilizzo delle risorse rispetto al livello di prestazioni scelto per il database. È necessario monitorare le risorse seguenti:
 - **Utilizzo della CPU** -è necessario controllare si raggiungono 100% di utilizzo della CPU in un periodo di tempo più lungo. Ciò potrebbe indicare che potrebbe essere necessario aggiornare l'istanza o database o identificare e ottimizzare le query che utilizzano la maggior parte della potenza di calcolo.
 - **Statistiche relative all'attesa** -è necessario controllare ciò che il motivo per cui le query sono in attesa di alcune risorse. Queriesmig attendere che i dati da recuperare o salvati in file di database, in attesa perché viene raggiunto un limite di risorse e così via.
 - **Utilizzo di IO** -è necessario controllare si raggiungono i limiti dei / o dell'archiviazione sottostante.
 - **Utilizzo della memoria** -la quantità di memoria disponibile per il database o l'istanza è proporzionale al numero di Vcore e da verificare è sufficiente per il carico di lavoro. Permanenza presunta della pagina è uno dei parametri che possono indicare sono le pagine rapidamente rimosso dalla memoria.

Database SQL di Azure **offre consigli utili per risolvere i problemi e correggere potenziali problemi di prestazioni**. È possibile identificare facilmente le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando [raccomandazioni sull'ottimizzazione delle prestazioni](sql-database-advisor.md). Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare queste indicazioni per migliorare le prestazioni del carico di lavoro. È anche possibile impostare il database SQL di Azure in modo che [ottimizzi automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le indicazioni identificate e verificando l'effettivo miglioramento delle prestazioni del database.

Per il monitoraggio e la risoluzione dei problemi di prestazioni del database sono disponibili le opzioni seguenti:

- Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
- Gli strumenti, ad esempio SQL Server Management Studio forniscono numerosi report utili, ad esempio un [Performance Dashboard](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) dove è possibile monitorare l'utilizzo delle risorse e identificare le query per consumo di risorse superiore o [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)in cui è possibile identificare le query con prestazioni peggiorate.
- Uso [informazioni dettagliate prestazioni Query](sql-database-query-performance.md) le [portale di Azure](https://portal.azure.com) per identificare le query che consumano la maggior parte delle risorse. Questa funzionalità è disponibile nel Database singolo e pool elastici solo.
- Usare [Advisor per database SQL ](sql-database-advisor-portal.md) per visualizzare le indicazioni per creare e rimuovere indici, parametrizzare le query e correggere i problemi di schema. Questa funzionalità è disponibile nel Database singolo e pool elastici solo.
- Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per il monitoraggio automatico delle prestazioni del database. Dopo aver rilevato un problema di prestazioni, viene generato un log di diagnostica con i dettagli e l'analisi della causa radice del problema. Quando possibile viene fornita un'indicazione di miglioramento delle prestazioni.
- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) e consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni identificati.
- Usare le [viste a gestione dinamica (DMV)](sql-database-monitoring-with-dmvs.md), gli [eventi estesi](sql-database-xevent-db-diff-from-svr.md) e [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per la risoluzione più dettagliata dei problemi di prestazioni.

> [!TIP]
> Vedere le [indicazioni relative alle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche che è possibile usare per migliorare le prestazioni del database SQL di Azure dopo aver identificato il problema di prestazioni con uno o più metodi sopra elencati.

## <a name="troubleshoot-performance-issues"></a>Risolvere i problemi di prestazioni

Per diagnosticare e risolvere i problemi di prestazioni, iniziare analizzando lo stato di ogni query attive e le condizioni che causano problemi di prestazioni pertinenti a ogni stato del carico di lavoro. Per migliorare le prestazioni del database SQL di Azure, verificare che ogni richiesta di query attiva dall'applicazione sia in stato di attesa o in esecuzione. Durante la risoluzione dei problemi di prestazioni nel database SQL di Azure, tenere presente il grafico seguente durante la lettura dell'articolo per diagnosticare e risolvere i problemi di prestazioni.

![Stati del carico di lavoro](./media/sql-database-monitor-tune-overview/workload-states.png)

Per un carico di lavoro con problemi di prestazioni, il problema di prestazioni potrebbe dipendere da una contesa di CPU (una condizione **correlata all'esecuzione**) o dalle singole query in attesa (condizione **correlata all'attesa**).

Le cause o **correlati esecuzione** problemi potrebbero essere:
- **Problemi di compilazione** -SQL Query Optimizer potrebbe produrre un piano non ottimale a causa di statistiche non aggiornate, la stima non corretta del numero di righe che verranno elaborati o la stima di memoria necessaria. Se si conosce che tale query è stata eseguita più velocemente in passato o in altra istanza (istanza gestita di SQL Server o istanza), disconnettere i piani di esecuzione effettivi e confrontare possano vedere sono diversi. Si tenta di applicare hint per la query o ricompilazioni statistiche o indici per ottenere il piano migliore. Attivare la correzione automatica del piano nel Database SQL di Azure per mitigare automaticamente questi problemi.
- **I problemi di esecuzione** : se il piano di query è ottimale quindi probabilmente sta impegnando alcuni limiti di risorse nel database, ad esempio velocità effettiva di scrittura log oppure potrebbe usare deframmentato gli indici che devono essere ricompilati. Un numero elevato di query simultanee che necessiti di risorse potrebbe anche essere la causa dei problemi di esecuzione. **In attesa correlati** problemi sono nella maggior parte dei casi relativi a problemi di esecuzione, perché le query che non sono in esecuzione in modo efficiente probabilmente sono in attesa di alcune risorse.

Le cause o **correlate in attesa** problemi potrebbero essere:
- **Il blocco** -un'unica query potrebbe essere utilizzato il blocco su alcuni oggetti nel database mentre altri utenti cercano di accedere agli stessi oggetti. È possibile identificare facilmente le query che il blocco tramite DMV o strumenti di monitoraggio.
- **Problemi dei / o** -potrebbero essere in attesa di query per le pagine in cui scrivere i file di dati o di log. In questo caso si noterà `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, o `PAGEIOLATCH_*` attendere le statistiche nella DMV.
- **Problemi di TempDB** : se si usa molte delle tabelle temporanee o se viene visualizzato una grande quantità di TempDB distribuisce nei piani di query potrebbe esistere un problema con una velocità effettiva di TempDB. 
- **Problemi correlati alla memoria** -non si dispone di memoria sufficiente per il carico di lavoro in modo che potrebbe eliminare la permanenza presunta della pagina o le query ottengono meno concessione di memoria quelle necessarie. In alcuni casi, funzionalità di intelligence integrata in Query Optimizer per correggere questi problemi.
 
Nelle sezioni seguenti verrà descritta come identificare e risolvere i problemi di alcuni di questi problemi.

## <a name="running-related-performance-issues"></a>Problemi di prestazioni correlati all'esecuzione

Come regola generale, se l'utilizzo della CPU è costantemente pari o superiore all'80%, è presente un problema di prestazioni correlato all'esecuzione. Se si riscontra un problema correlato all'esecuzione, questo potrebbe essere causato da risorse di CPU insufficienti o può essere correlato a una delle condizioni seguenti:

- Numero eccessivo di query in esecuzione
- Numero eccessivo di query in fase di compilazione
- Una o più query in esecuzione usano un piano di query non ottimale

Se si determina che è presente un problema di prestazioni correlato all'esecuzione, l'obiettivo consiste nell'identificare il problema preciso usando uno o più metodi. I metodi più comuni per identificare i problemi di esecuzione sono:

- Usare il [portale di Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) per monitorare l'utilizzo percentuale della CPU.
- Usare le seguenti [visualizzazioni a gestione dinamica](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce il consumo di CPU, I/O e memoria per un database SQL di Azure. È presente una riga ogni 15 secondi, anche se non c'è attività di database. I dati cronologici vengono conservati per un'ora.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce i dati di archiviazione e di uso della CPU per un database SQL di Azure. I dati vengono raccolti e aggregati per intervalli di cinque minuti.

> [!IMPORTANT]
> Per un set di una query T-SQL che usa queste DMV per risolvere i problemi relativi all'utilizzo della CPU, vedere [Identificare i problemi di prestazioni della CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Risolvere i problemi di query con problemi di piani di esecuzione di query sensibili ai parametri

Il problema del piano sensibile ai parametri si riferisce a uno scenario in cui Query Optimizer genera un piano di esecuzione di query ottimale solo per un valore di parametro specifico (un o set di valori) e il piano memorizzato nella cache non è quindi ottimale per i valori dei parametri usati in esecuzioni consecutive. I piani non ottimali possono quindi comportare problemi di prestazioni delle query e ridurre la velocità effettiva complessiva dei carichi di lavoro. Per altre informazioni su analisi dei parametri e l'elaborazione delle query, vedere la [Guida sull'architettura di elaborazione Query](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

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

- Ciò [olfatto parametro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) post di blog
- Post di blog su [SQL dinamico e la qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- Ciò [tecniche di ottimizzazione di Query SQL in SQL Server: Analisi dei parametri](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) post di blog

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Risoluzione dei problemi relativi all'attività di compilazione a causa della parametrizzazione non corretta

Quando una query contiene valori letterali, il motore di database sceglie di impostare automaticamente i parametri dell'istruzione oppure un utente può impostarne i parametri in modo esplicito per ridurre il numero di compilazioni. Un numero elevato di compilazioni di una query con lo stesso modello, ma valori letterali diversi può causare un utilizzo elevato della CPU. Analogamente, se si impostano solo parzialmente i parametri di una query che continua a includere valori letterali, il motore di database non imposta gli altri parametri.  Di seguito è riportato un esempio di una query con solo alcuni parametri impostati:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
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
### <a name="factors-influencing-query-plan-changes"></a>Fattori che influenzano le modifiche al piano di query

La ricompilazione di un piano di esecuzione query può comportare un piano di query generato è diverso da ciò che è stato originariamente memorizzati nella cache. Esistono vari motivi, motivo per cui un piano esistente originale potrebbe essere ricompilato automaticamente:
- Modifiche nello schema di cui fa riferimento la query
- Modifiche ai dati alle tabelle di cui fa riferimento la query 
- Modifiche alle opzioni del contesto di query 

Un piano compilato potrebbe essere espulso dalla cache per svariati motivi, ad esempio riavvii delle istanze, le modifiche alla configurazione, utilizzo della memoria e richieste esplicitare per cancellare la cache con ambito database. Utilizzando un hint RECOMPILE, inoltre, significa che non verrà memorizzate nella cache di un piano.

Una ricompilazione o nuova compilazione dopo la rimozione della cache, comunque può comportare la generazione di un piano di esecuzione di query identica rispetto a quello originariamente osservato.  Se, tuttavia, esistono modifiche al piano rispetto al piano originale o precedente, ecco le spiegazioni più comuni per il motivo per cui un piano di esecuzione di query modificato:

- **Modificare la progettazione fisica**. Ad esempio, nuovi indici creati che coprono che i requisiti di una query possono essere utilizzati con una nuova compilazione se query optimizer decide che è in modo più efficace più ottimale per sfruttare questo nuovo indice rispetto all'utilizzo la struttura dei dati selezionata originalmente per la prima versione di l'esecuzione della query.  Qualsiasi modifica fisica per gli oggetti di riferimento può comportare una scelta del piano di nuovo in fase di compilazione.

- **Differenze di risorse server**. In uno scenario in cui un unico piano differisce su "sistema" e "sistema B", la disponibilità delle risorse, ad esempio il numero di processori disponibili, possono influenzare il piano generato.  Ad esempio, se un sistema dispone di un numero maggiore di processori, può essere scelto un piano parallelo. 

- **Statistiche diverse**. Le statistiche associate agli oggetti di riferimento modificato o sono sostanzialmente diverse dalle statistiche del sistema originale.  Se le statistiche di modifica e si verifica una ricompilazione, query optimizer userà le statistiche a partire da quel punto specifico nel tempo. Le statistiche riviste possono avere distribuzioni dei dati diverse in modo significativo e frequenze che non sono presenti nella compilazione originale.  Queste modifiche vengono utilizzate per stimare stime della cardinalità (numero di righe previsto per fluire attraverso l'albero della query logica).  Le modifiche alle stime della cardinalità possono portare a scegliere diversi operatori fisici e ordine di operazioni associato.  Alcune modifiche minori anche alle statistiche possono comportare un piano di esecuzione di query modificata.

- **Versione Estimatore cardinalità o un livello di database è stato sostituito compatibilità**.  Le modifiche a livello di compatibilità del database è possono abilitare nuove strategie e le funzionalità che possono comportare un piano di esecuzione di query diversi.  Oltre a livello di compatibilità del database, la disabilitazione o abilitazione di flag di traccia 4199 o la modifica dello stato della configurazione con ambito database QUERY_OPTIMIZER_HOTFIXES possono influenzare anche query scelte di piani di esecuzione in fase di compilazione.  I flag di traccia 9481 (force versione CE legacy) e 2312 (forzare la versione CE predefinita) sono anche ai piani. 

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

Query Store e le viste DMV di rilevamento delle statistiche di attesa mostrano solo i risultati delle query completate e di cui si è verificato il timeout e non mostrano i dati per le istruzioni attualmente in esecuzione (finché non vengono completate). La vista DMV [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) consente di tenere traccia delle query attualmente in esecuzione e della durata del ruolo di lavoro associato.

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
> - [TigerToolbox - resta in attesa di latch](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Miglioramento delle prestazioni del database con più risorse

Se alla fine non esistono elementi eseguibili che possono migliorare le prestazioni del database, è possibile modificare la quantità di risorse disponibili nel database SQL di Azure. È possibile assegnare più risorse modificando il [livello di servizio DTU](sql-database-service-tiers-dtu.md) di un database singolo o aumentare il numero di eDTU di un pool elastico in qualsiasi momento. In alternativa, se si usa il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) è possibile modificare il livello di servizio o aumentare le risorse allocate al database.

1. Per i database singoli, è possibile [modificare i livelli di servizio](sql-database-single-database-scale.md) o le [risorse di calcolo](sql-database-single-database-scale.md) su richiesta per migliorare le prestazioni del database.
2. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

## <a name="tune-and-refactor-application-or-database-code"></a>Ottimizzare ed eseguire il refactoring del codice del database o dell'applicazione

È possibile modificare il codice dell'applicazione per usare il database in modo più ottimale, modificare gli indici, forzare i piani o usare i suggerimenti per adattare manualmente il database al carico di lavoro. Alcuni suggerimenti e indicazioni per l'ottimizzazione manuale e la riscrittura del codice sono disponibili nell'[argomento relativo alle indicazioni sulle prestazioni](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.
- Modificare le risorse disponibili nel database modificando i [livelli di servizio del database SQL di Azure](sql-database-performance-guidance.md)
