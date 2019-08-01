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
ms.date: 01/25/2019
ms.openlocfilehash: c52b41c4e6d0618b4df9b2aed985bbd22d89f419
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567192"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Il database SQL di Azure offre strumenti e metodi per monitorare facilmente l'utilizzo, aggiungere o rimuovere risorse (CPU, memoria, I/O), risolvere i problemi potenziali e individuare raccomandazioni che possono migliorare le prestazioni di un database. Il database SQL di Azure dispone di numerose funzionalità che consentono di risolvere automaticamente i problemi nei database, in modo da consentire a un database di adattarsi al carico di lavoro e ottimizzare automaticamente le prestazioni. Tuttavia, esistono alcuni problemi personalizzati che potrebbero richiedere la risoluzione dei problemi. Questo articolo illustra alcune procedure consigliate e gli strumenti che possono essere usati per risolvere i problemi relativi alle prestazioni.

Per assicurarsi che un database venga eseguito senza problemi, è necessario eseguire due attività principali:
- [Monitoraggio delle prestazioni del database](#monitoring-database-performance) per assicurarsi che le risorse assegnate al database siano in grado di gestire il carico di lavoro. Se si nota che un database sta raggiungendo i limiti delle risorse, è necessario identificare e ottimizzare le prime query per consumo di risorse, oppure è necessario aggiungere più risorse aggiornando il livello di servizio.
- [Risolvere i problemi relativi alle prestazioni](#troubleshoot-performance-issues) per identificare il motivo per cui si è verificato un problema potenziale, identificare la causa radice del problema ed eseguire azioni che risolveranno il problema.

## <a name="monitoring-database-performance"></a>Monitoraggio delle prestazioni del database

Il monitoraggio delle prestazioni di un database SQL in Azure inizia con il monitoraggio dell'utilizzo delle risorse rispetto al livello di prestazioni scelto per il database. Le risorse seguenti devono essere monitorate per la stessa:
 - **Utilizzo CPU** : controllare se il database sta raggiungendo il 100% dell'utilizzo della CPU per un lungo periodo di tempo. Questo potrebbe indicare che è necessario aggiornare il database o l'istanza a un livello di servizio superiore oppure è necessario identificare e ottimizzare le query che utilizzano la maggior parte della potenza di calcolo.
 - **Statistiche attesa** : verificare perché le query sono in attesa di alcune risorse. Le query attendono che i dati vengano recuperati o salvati nei file di database, in attesa che venga raggiunto un limite di risorse e così via.
 - **Utilizzo** i/o: verificare se il database sta raggiungendo i limiti di i/o dell'archiviazione sottostante.
 - **Utilizzo memoria** : la quantità di memoria disponibile per il database o l'istanza è proporzionale al numero di Vcore e controlla se è sufficiente per il carico di lavoro. La permanenza presunta della pagina è uno dei parametri che possono indicare la velocità con cui le pagine vengono rimosse dalla memoria.

Il servizio database SQL **di Azure include gli strumenti e le risorse che consentono di risolvere i problemi relativi alle prestazioni potenziali**. È possibile identificare facilmente le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando le raccomandazioni per l' [ottimizzazione delle prestazioni](sql-database-advisor.md). Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare queste indicazioni di ottimizzazione per migliorare le prestazioni del carico di lavoro. È anche possibile consentire al database SQL di Azure di [ottimizzare automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le raccomandazioni identificate e verificando questo miglioramento delle prestazioni del database.

Per il monitoraggio e la risoluzione dei problemi relativi alle prestazioni del database sono disponibili le opzioni seguenti:

- Nella [portale di Azure](https://portal.azure.com)fare clic su **database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per individuare le risorse che si avvicinano al loro utilizzo massimo. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
- Strumenti come SQL Server Management Studio forniscono molti report utili come un [Dashboard delle prestazioni](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) per monitorare l'utilizzo delle risorse e identificare le prime query per consumo di risorse o [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) per identificare le query con regressione prestazioni.
- Usare [informazioni dettagliate prestazioni query](sql-database-query-performance.md) nel [portale di Azure](https://portal.azure.com) per identificare le query che dedicano la maggior parte delle risorse. Questa funzionalità è disponibile solo in Database singolo e nei pool elastici.
- Usare [Advisor per database SQL ](sql-database-advisor-portal.md) per visualizzare le indicazioni per creare e rimuovere indici, parametrizzare le query e correggere i problemi di schema. Questa funzionalità è disponibile solo in Database singolo e nei pool elastici.
- Usare [Intelligent Insights SQL di Azure](sql-database-intelligent-insights.md) per il monitoraggio automatico delle prestazioni del database. Dopo aver rilevato un problema di prestazioni, viene generato un log di diagnostica con i dettagli e l'analisi della causa radice del problema. Quando possibile viene fornita un'indicazione di miglioramento delle prestazioni.
- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) e consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni identificati.
- Usare le [viste a gestione dinamica (DMV)](sql-database-monitoring-with-dmvs.md), gli [eventi estesi](sql-database-xevent-db-diff-from-svr.md) e [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) per la risoluzione più dettagliata dei problemi di prestazioni.

> [!TIP]
> Vedere le [indicazioni relative alle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche che è possibile usare per migliorare le prestazioni del database SQL di Azure dopo aver identificato il problema di prestazioni con uno o più metodi sopra elencati.

## <a name="troubleshoot-performance-issues"></a>Risolvere i problemi di prestazioni

Per diagnosticare e risolvere i problemi di prestazioni, iniziare analizzando lo stato di ogni query attive e le condizioni che causano problemi di prestazioni pertinenti a ogni stato del carico di lavoro. Per migliorare le prestazioni del database SQL di Azure, tenere presente che ogni richiesta di query attiva dall'applicazione è in uno stato in esecuzione o in attesa. Quando si esegue la risoluzione dei problemi relativi alle prestazioni nel database SQL di Azure, tenere presente quanto segue in questo articolo per diagnosticare e risolvere i problemi relativi alle prestazioni.

![Stati del carico di lavoro](./media/sql-database-monitor-tune-overview/workload-states.png)

Per un carico di lavoro con problemi di prestazioni, il problema di prestazioni potrebbe dipendere da una contesa di CPU (una condizione **correlata all'esecuzione**) o dalle singole query in attesa (condizione **correlata all'attesa**).

Le cause dei problemi **relativi all'esecuzione** potrebbero essere:
- **Problemi di compilazione** -SQL Query Optimizer potrebbe produrre un piano non ottimale a causa di statistiche obsolete, una stima errata del numero di righe che verranno elaborate o la stima della memoria necessaria. Se si sa che la query è stata eseguita più velocemente in passato o in un'altra istanza (Istanza gestita o SQL Server istanza), eseguire i piani di esecuzione effettivi e confrontarli per verificare se sono diversi. Provare ad applicare gli hint per la query o ricompilare le statistiche o ricompilare gli indici per ottenere il piano migliore. Abilitare la correzione automatica dei piani nel database SQL di Azure per attenuare automaticamente questi problemi.
- **Problemi di esecuzione** : se il piano di query è ottimale, è probabile che si verifichino alcuni limiti di risorse nel database, ad esempio la velocità effettiva di scrittura del log o l'uso di indici deframmentati che devono essere ricompilati. Un numero elevato di query simultanee che spendono le risorse potrebbe essere causato anche da problemi di esecuzione. I problemi **relativi all'attesa** nella maggior parte dei casi sono correlati ai problemi di esecuzione, perché le query che non sono in esecuzione in modo efficiente sono probabilmente in attesa di alcune risorse.

Le cause dei problemi **relativi all'attesa** potrebbero essere:
- **Blocco** : una query potrebbe mantenere il blocco su alcuni oggetti nel database mentre altri tentano di accedere agli stessi oggetti. È possibile identificare facilmente le query di blocco usando la DMV o gli strumenti di monitoraggio.
- **Problemi di io** : le query potrebbero essere in attesa di scrivere le pagine nei file di dati o di log. In questo caso, `INSTANCE_LOG_RATE_GOVERNOR`vedere `WRITE_LOG`, o `PAGEIOLATCH_*` le statistiche di attesa nella DMV.
- **Problemi relativi a tempdb** : se il carico di lavoro utilizza una grande quantità di tabelle temporanee o se nei piani si verificano numerose distribuzioni di tempdb, le query potrebbero avere un problema con la velocità effettiva di tempdb. 
- **Problemi relativi alla memoria** : potrebbe non essere disponibile memoria sufficiente per il carico di lavoro, in modo che la permanenza presunta delle pagine venga eliminata oppure le query ricevono meno memoria del necessario. In alcuni casi, l'Intelligence incorporata in Query Optimizer risolve questi problemi.
 
 Nelle sezioni seguenti viene illustrato come identificare e risolvere alcuni di questi problemi.

## <a name="running-related-performance-issues"></a>Problemi di prestazioni correlati all'esecuzione

Come linea guida generale, se l'utilizzo della CPU è costantemente pari o superiore al 80%, si verifica un problema di prestazioni relativo all'esecuzione. Se si verifica un problema relativo all'esecuzione, potrebbe essere causato da risorse di CPU insufficienti oppure può essere correlato a una delle condizioni seguenti:

- Numero eccessivo di query in esecuzione
- Numero eccessivo di query in fase di compilazione
- Una o più query in esecuzione usano un piano di query non ottimale

Se si verifica un problema di prestazioni relativo all'esecuzione, l'obiettivo consiste nell'identificare il problema preciso mediante uno o più metodi. I metodi più comuni per identificare i problemi di esecuzione sono:

- Usare il [portale di Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) per monitorare l'utilizzo percentuale della CPU.
- Usare le seguenti [visualizzazioni a gestione dinamica](sql-database-monitoring-with-dmvs.md):

  - [sys. dm _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce CPU, I/O e utilizzo di memoria per un database SQL di Azure. Esiste una riga ogni 15 secondi di intervallo, anche se non è presente alcuna attività nel database. I dati cronologici vengono conservati per un'ora.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) restituisce i dati di archiviazione e di uso della CPU per un database SQL di Azure. I dati vengono raccolti e aggregati per intervalli di cinque minuti.

> [!IMPORTANT]
> Per un set di una query T-SQL che usa queste DMV per risolvere i problemi relativi all'utilizzo della CPU, vedere [Identificare i problemi di prestazioni della CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Risolvere i problemi relativi alle query con il piano di esecuzione di query sensibile ai parametri

Il problema del piano sensibile ai parametri si riferisce a uno scenario in cui Query Optimizer genera un piano di esecuzione di query ottimale solo per un valore di parametro specifico (un o set di valori) e il piano memorizzato nella cache non è quindi ottimale per i valori dei parametri usati in esecuzioni consecutive. I piani non ottimali possono quindi comportare problemi di prestazioni delle query e ridurre la velocità effettiva complessiva dei carichi di lavoro. Per ulteriori informazioni sull'analisi dei parametri e sull'elaborazione di query, vedere la [Guida sull'architettura di elaborazione delle query](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Sono disponibili diverse soluzioni alternative per attenuare questi problemi, ognuno con compromessi e svantaggi associati:

- Usare l'hint per la query [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) a ogni esecuzione di query. Questa soluzione alternativa offre tempi di compilazione ridotti e una maggiore quantità di CPU per una migliore qualità del piano. L'uso dell'opzione `RECOMPILE` spesso non è possibile per i carichi di lavoro che richiedono una velocità effettiva elevata.
- Usare l'hint per la query [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo con un valore di parametro tipico che genera un piano adeguato per la maggior parte dei valori di parametro possibili.   Questa opzione richiede una buona conoscenza dei valori di parametro ottimali e delle caratteristiche del piano associate.
- Usare l'hint per la query [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per eseguire l'override del valore del parametro effettivo invece di usare la media del vettore di densità. A questo scopo è anche possibile acquisire i valori dei parametri in entrata in variabili locali e quindi usare le variabili locali all'interno dei predicati invece di usare i parametri stessi. La densità media deve essere *adeguata* a questa particolare correzione.
- Disabilitare completamente l'analisi dei parametri usando l'hint per la query [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Usare l'hint per la query [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) per evitare la ricompilazione nella cache. Questa soluzione alternativa presuppone che il piano comune *adeguato* sia quello già nella cache. È anche possibile disabilitare gli aggiornamenti automatici alle statistiche per ridurre le probabilità che il piano valido venga rimosso e che venga compilato un nuovo piano non valido.
- Forzare il piano usando in modo esplicito l'hint per la query [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (specificandolo in modo esplicito, impostando un piano specifico con Query Store o abilitando l'[ottimizzazione automatica](sql-database-automatic-tuning.md)).
- Sostituire la procedura singola con un set annidato di procedure, ognuna delle quali può essere usata in base alla logica condizionale e ai valori dei parametri associati.
- Creare alternative basate sull'esecuzione di stringhe dinamiche per una definizione di procedura statica.

Per altre informazioni sulla risoluzione di questi tipi di problemi, vedere:

- Questo è un post di Blog relativo [all'odore di un parametro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- Post di blog su [SQL dinamico e la qualità del piano per le query con parametri](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- Queste [tecniche di ottimizzazione delle query SQL in SQL Server: Post di](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) Blog sullo sniffing dei parametri

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Risoluzione dei problemi relativi all'attività di compilazione a causa della parametrizzazione non corretta

Quando una query contiene valori letterali, il motore di database sceglie di impostare automaticamente i parametri dell'istruzione oppure un utente può impostarne i parametri in modo esplicito per ridurre il numero di compilazioni. Un numero elevato di compilazioni di una query con lo stesso modello, ma valori letterali diversi può causare un utilizzo elevato della CPU. Analogamente, se si impostano solo parzialmente i parametri di una query che continua a includere valori letterali, il motore di database non imposta gli altri parametri.  Di seguito è riportato un esempio di una query con solo alcuni parametri impostati:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Nell'esempio precedente, `t1.c1` accetta `@p1` ma `t2.c2` continua ad assumere il GUID come valore letterale. In questo caso, se si modifica il valore per `c2`, la query verrà considerata come una query diversa e verrà eseguita una nuova compilazione. Per ridurre le compilazioni nell'esempio precedente, la soluzione consiste nell'impostare i parametri anche del GUID.

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
### <a name="factors-influencing-query-plan-changes"></a>Fattori che influenzano le modifiche del piano di query

Una ricompilazione del piano di esecuzione della query può comportare un piano di query generato che differisce da quello originariamente memorizzato nella cache. Esistono diversi motivi per cui un piano originale esistente potrebbe essere ricompilato automaticamente:
- Modifiche nello schema a cui fa riferimento la query
- Modifiche ai dati nelle tabelle a cui fa riferimento la query 
- Modifiche alle opzioni del contesto di query 

Un piano compilato può essere espulso dalla cache per diversi motivi, tra cui riavvii dell'istanza, modifiche della configurazione con ambito database, utilizzo della memoria e richieste esplicite per cancellare la cache. Inoltre, l'utilizzo di un hint RECOMPILE indica che un piano non verrà memorizzato nella cache.

Una ricompilazione (o una nuova compilazione dopo la rimozione della cache) può comunque comportare la generazione di un piano di esecuzione di query identico a quello originariamente osservato.  Se tuttavia sono state apportate modifiche al piano rispetto al piano precedente o originale, di seguito sono riportate le spiegazioni più comuni per la modifica di un piano di esecuzione della query:

- **Modifica della progettazione fisica**. Ad esempio, sono stati creati nuovi indici che coprono in modo più efficace i requisiti di una query. Questi possono essere usati in una nuova compilazione se il Query Optimizer decide che è più ottimale sfruttare il nuovo indice rispetto alla struttura dei dati selezionata originariamente per la prima versione dell'esecuzione della query.  Eventuali modifiche fisiche apportate agli oggetti a cui si fa riferimento possono generare una nuova scelta del piano in fase di compilazione.

- **Differenze tra le risorse del server**. In uno scenario in cui un piano differisce da "sistema A" rispetto a "sistema B": la disponibilità delle risorse, ad esempio il numero di processori disponibili, può influenzare il piano generato.  Ad esempio, se un sistema dispone di un numero maggiore di processori, è possibile scegliere un piano parallelo. 

- **Statistiche diverse**. Le statistiche associate agli oggetti a cui si fa riferimento sono state modificate o sono materialmente diverse dalle statistiche del sistema originale.  Se le statistiche cambiano e viene eseguita una ricompilazione, il Query Optimizer utilizzerà le statistiche a partire da quel momento specifico. Le statistiche rivedute possono avere distribuzioni e frequenze di dati significativamente diverse che non sono state eseguite nella compilazione originale.  Queste modifiche vengono usate per stimare le stime della cardinalità (numero di righe previste per il flusso attraverso l'albero delle query logiche).  Le modifiche alle stime della cardinalità possono comportare la scelta di operatori fisici diversi e l'ordine delle operazioni associato.  Anche le modifiche minime alle statistiche possono generare un piano di esecuzione delle query modificato.

- **Modifica del livello di compatibilità del database o della versione di stima**della cardinalità.  Le modifiche apportate al livello di compatibilità del database possono consentire nuove strategie e funzionalità che possono generare un piano di esecuzione di query diverso.  Oltre al livello di compatibilità del database, la disabilitazione o l'abilitazione del flag di traccia 4199 o la modifica dello stato della configurazione con ambito database QUERY_OPTIMIZER_HOTFIXES possono anche influenzare le scelte del piano di esecuzione delle query in fase di compilazione.  I flag di traccia 9481 (Force legacy CE) e 2312 (Force default CE) sono anche piani che interessano. 

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
> - [TigerToolbox-attese e latch](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
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
