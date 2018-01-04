---
title: Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights | Microsoft Docs
description: Intelligent Insights consente di risolvere i problemi di prestazioni del database SQL di Azure.
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: cce112929ff2f4fb48c2c6e2ddc2d4eee743b790
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights

Questa pagina contiene informazioni sui problemi di prestazioni del database SQL di Azure rilevati tramite il log di diagnostica delle prestazioni del database di [Intelligent Insights](sql-database-intelligent-insights.md). Il log di diagnostica può essere inviato ad [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Hub eventi di Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Archiviazione di Azure](sql-database-metrics-diag-logging.md#stream-into-storage) oppure a una soluzione di terze parti per funzionalità di avvisi e report di DevOps personalizzate.

> [!NOTE]
> Per una rapida guida alla risoluzione dei problemi di prestazioni del database SQL, vedere il diagramma [Flusso di risoluzione dei problemi consigliato](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) in questo documento.
>

## <a name="detectable-database-performance-patterns"></a>Modelli di prestazioni del database rilevabili

Intelligent Insights rileva automaticamente i problemi di prestazioni con il database di SQL in base ai tempi di attesa, agli errori o ai timeout di esecuzione delle query. Registra quindi i modelli di prestazioni rilevati nel log di diagnostica. La tabella seguente riepiloga i modelli di prestazioni rilevabili.

| Modelli di prestazioni rilevabili | Dettagli restituiti |
| :------------------- | ------------------- |
| [Raggiungimento dei limiti delle risorse](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Il consumo di risorse disponibili (DTU), i thread di lavoro del database o le sessioni di accesso al database disponibili nella sottoscrizione monitorata hanno raggiunto i limiti, con conseguenti problemi di prestazioni del database SQL. |
| [Aumento del carico di lavoro](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | È stato rilevato un aumento o un accumulo continuo di carico di lavoro nel database, con conseguenti problemi di prestazioni del database SQL. |
| [Utilizzo elevato della memoria](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | I thread di lavoro che hanno richiesto concessioni di memoria devono attendere le allocazioni di memoria per una quantità di tempo memoria statisticamente significativa. Oppure è presente un maggiore accumulo di thread di lavoro che hanno richiesto concessioni di memoria, con conseguenti effetti sulle prestazioni del database SQL. |
| [Blocco](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | È stato rilevato un blocco eccessivo del database con effetti sulle prestazioni del database SQL. |
| [Valore di MAXDOP aumentato](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | L'opzione relativa al massimo grado di parallelismo (MAXDOP) è stata modificata, con effetti sull'efficienza di esecuzione delle query. |
| [Contesa di latch di pagina](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | È stata rilevata una contesa di latch di pagina, con effetti sulle prestazioni del database SQL. Più thread tentano di accedere contemporaneamente alle stesse pagine del buffer dei dati in memoria. I tempi di attesa risultano pertanto maggiori, con effetti sulle prestazioni del database SQL. |
| [Indice mancante](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | È stato rilevato un problema di indice mancante, con effetti sulle prestazioni del database SQL. |
| [Nuova query](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | È stata rilevata una nuova query, con effetti sulle prestazioni complessive del database SQL. |
| [Statistiche di attesa non comune](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Sono stati rilevati tempi di attesa del database non comuni, con effetti sulle prestazioni del database SQL. |
| [Contesa di TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Più thread tentano di accedere alle stesse risorse tempDB, causando un collo di bottiglia che influisce sulle prestazioni del database SQL. |
| [Carenza di DTU nel pool elastico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Una carenza di eDTU disponibili nel pool elastico influisce sulle prestazioni del database SQL. |
| [Regressione di piani](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | È stato rilevato un nuovo piano o una modifica nel carico di lavoro di un piano esistente con effetti sulle prestazioni del database SQL. |
| [Modifica del valore di configurazione in ambito database](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Una modifica di configurazione nel database influisce sulle prestazioni del database SQL. |
| [Client lento](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | È stato rilevato un client applicazione lento che non è in grado di usare in modo abbastanza rapido l'output del database SQL, con effetti sulle prestazioni del database stesso. |
| [Downgrade del piano tariffario](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Un'azione di downgrade del piano tariffario ha ridotto le risorse disponibili, con effetti sulle prestazioni del database SQL. |

> [!TIP]
> Per un'ottimizzazione continua delle prestazioni del database SQL, abilitare l'[ottimizzazione automatica del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). Questa funzionalità univoca di intelligence incorporata nel database SQL consente di monitorare in modo continuo il database SQL, ottimizzare automaticamente gli indici e applicare correzioni ai piani di esecuzione delle query.
>

La sezione seguente descrive in modo più dettagliato i modelli di prestazioni rilevabili elencati in precedenza.

## <a name="reaching-resource-limits"></a>Raggiungimento dei limiti delle risorse

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili combina i problemi di prestazioni correlati al raggiungimento dei limiti delle risorse disponibili, dei thread di lavoro e delle sessioni. Dopo che il problema è stato rilevato, un campo di descrizione del log di diagnostica indica se il problema di prestazioni è correlato ai limiti delle risorse, dei thread di lavoro o delle sessioni.

Le risorse nel database SQL vengono in genere definite [risorse DTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu). Sono costituite da una misura combinata di risorse di CPU e I/O (I/O di dati e del log delle transazioni). Il modello del raggiungimento dei limiti delle risorse viene riconosciuto quando la riduzione delle prestazioni delle query rilevata è provocata dal raggiungimento di uno qualsiasi dei limiti delle risorse misurate.

La risorsa con limiti di sessione indica il numero di accessi simultanei disponibili al database SQL. Questo modello di prestazioni viene riconosciuto nel caso in cui le applicazioni che si connettono ai database SQL abbiano raggiunto il numero di accessi simultanei disponibili al database. Se le applicazioni tentano di usare più sessioni rispetto a quelle disponibili in un database, le prestazioni delle query ne sono influenzate.

Il raggiungimento dei limiti dei thread di lavoro rappresenta un caso di raggiungimento dei limiti delle risorse specifico poiché i thread di lavoro disponibili non vengono conteggiati nell'uso delle DTU. Il raggiungimento dei limiti dei thread di lavoro in un database può causare l'aumento dei tempi di attesa specifici di una risorsa, con conseguente riduzione delle prestazioni delle query.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica genera hash di query per le query con effetti sulle prestazioni e sulle percentuali di consumo delle risorse. È possibile usare queste informazioni come punto iniziale per ottimizzare il carico di lavoro del database. In particolare, è possibile ottimizzare le query con effetti sulla riduzione delle prestazioni tramite l'aggiunta di indici. Oppure è possibile ottimizzare le applicazioni con una distribuzione più uniforme dei carichi di lavoro. Se non è possibile ridurre i carichi di lavoro o eseguire ottimizzazioni, valutare il passaggio a un piano tariffario di livello superiore per la sottoscrizione del database SQL per incrementare la quantità di risorse disponibili.

Se sono stati raggiunti i limiti delle sessioni disponibili, è possibile ottimizzare le applicazioni riducendo il numero di accessi al database. Se non si riesce a ridurre il numero di accessi dalle applicazioni al database, valutare il passaggio a un piano tariffario di livello superiore per il database. In alternativa, è possibile suddividere e spostare il database in più database per una distribuzione più equilibrata del carico di lavoro.

Per altri suggerimenti sulla risoluzione dei problemi per i limiti delle sessioni, vedere [How to deal with the limits of Azure SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/).(Come gestire i limiti al numero massimo di accessi al database SQL di Azure). Per conoscere i limiti delle risorse disponibili per il proprio livello di sottoscrizione, vedere [Limiti delle risorse del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Aumento del carico di lavoro

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni identifica i problemi causati da un aumento o, in forma peggiore, da un accumulo del carico di lavoro.

Il rilevamento viene eseguito tramite una combinazione di diverse metriche. La metrica di base misurata è la rilevazione di un aumento del carico di lavoro rispetto alla baseline del carico di lavoro precedente. L'altra forma di rilevamento si basa sulla misurazione di un notevole aumento di thread di lavoro attivi, sufficiente per influire sulle prestazioni delle query.

Nella sua forma più grave, il carico di lavoro potrebbe accumularsi continuamente fino a rendere impossibile la gestione del carico di lavoro da parte del database SQL. La conseguenza è un continuo aumento delle dimensioni del carico di lavoro, ovvero la condizione che determina l'accumulo. A causa di questa condizione, aumenta il tempo di attesa dell'esecuzione per il carico di lavoro. Questa condizione rappresenta uno dei più gravi problemi di prestazioni di database. Questo problema viene rilevato monitorando l'aumento del numero di thread di lavoro interrotti. 

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica riporta il numero di query la cui esecuzione è aumentata e l'hash della query che contribuisce maggiormente all'aumento del carico di lavoro. È possibile usare queste informazioni come punto di partenza per l'ottimizzazione del carico di lavoro. La query identificata come quella che contribuisce maggiormente all'aumento del carico di lavoro è particolarmente utile come punto di partenza.

Si potrebbe valutare una distribuzione più uniforme dei carichi di lavoro nel database. Prendere in considerazione l'ottimizzazione della query che influisce sulle prestazioni tramite l'aggiunta di indici. Si può anche distribuire il carico di lavoro tra più database. Se queste soluzioni non sono praticabili, valutare il passaggio a un piano tariffario di livello superiore per la sottoscrizione del database SQL per incrementare la quantità di risorse disponibili.

## <a name="memory-pressure"></a>Utilizzo elevato della memoria

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database causata da un utilizzo elevato della memoria o, nella forma più grave, da una condizione di accumulo d richieste alla memoria rispetto alla baseline delle prestazioni dei sette giorni precedenti.

L'uso elevato della memoria indica una condizione delle prestazioni in cui è presente un numero elevato di thread di lavoro che richiedono concessioni di memoria nel database SQL. Questo volume elevato causa una condizione di uso elevato della memoria in cui il database SQL non è in grado di allocare in modo efficiente la memoria a tutti i thread di lavoro che la richiedono. Uno dei motivi più comuni per questo problema è da un lato correlato alla quantità di memoria disponibile per il database SQL. Dall'altro, un aumento del carico di lavoro causa l'aumento dei thread di lavoro e l'utilizzo elevato della memoria.

La forma più grave di utilizzo elevato della memoria è la condizione di accumulo di richieste di memoria. Questa condizione indica la presenza di un numero di thread di lavoro che richiede concessioni di memoria maggiore rispetto alle query che rilasciano la memoria. Il numero di thread di lavoro che richiedono concessioni di memoria potrebbe anche crescere di continuo (accumulo) perché il motore del database SQL non è in grado di allocare memoria in modo sufficientemente efficiente per soddisfare la domanda. La condizione di accumulo di richieste alla memoria rappresenta uno dei più gravi problemi di prestazioni di database.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica restituisce i dettagli dell'archivio di oggetti di memoria con il clerk di memoria, ovvero thread di lavoro, contrassegnato come il motivo principale per l'uso elevato della memoria e i timestamp pertinenti. È possibile usare queste informazioni come base per la risoluzione dei problemi. 

È possibile ottimizzare o rimuovere query correlate ai clerk con l'utilizzo della memoria più elevato. È anche possibile assicurarsi di evitare l'esecuzione di query su dati che non si prevede di usare. Una buona norma consiste nell'usare sempre una clausola WHERE nelle query. Inoltre, è consigliabile creare indici non cluster per la ricerca dei dati, anziché eseguirne l'analisi.

È anche possibile ridurre il carico di lavoro ottimizzandolo o distribuendolo su più database. Oppure è possibile distribuire il carico di lavoro tra più database. Se queste soluzioni non sono praticabili, valutare il passaggio a un piano tariffario di livello superiore per la sottoscrizione del database SQL per incrementare la quantità di risorse di memoria disponibili per il database.

Per altre informazioni sulla risoluzione dei problemi, vedere [Memory grants meditation: The mysterious SQL Server memory consumer with many names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/) (Riflessione sulle concessioni di memoria: il misterioso consumer di memoria di SQL Server con molti nomi).

## <a name="locking"></a>Blocco

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database in cui viene rilevato un blocco eccessivo del database rispetto alla baseline delle prestazioni dei sette giorni precedenti. 

Nei sistemi RDBMS moderni il blocco è essenziale per l'implementazione multithreading in cui le prestazioni sono massimizzate tramite l'esecuzione di più thread di lavoro simultanei e di transazioni di database parallele ove possibile. Il termine blocco in questo contesto si riferisce al meccanismo di accesso predefinito in cui solo un'unica transazione può accedere in modo esclusivo a righe, pagine, tabelle e file necessari e non entra in conflitto con un'altra transazione per accedere alle risorse. Quando la transazione che ha bloccato le risorse per l'uso è completata, il blocco su tali risorse viene rilasciato consentendo ad altre transazioni di accedere alle risorse necessarie. Per altre informazioni sul blocco, vedere [Utilizzo dei blocchi in Motore di database](https://msdn.microsoft.com/library/ms190615.aspx).

Se le transazioni eseguite dal motore SQL rimangono in attesa per periodi di tempo prolungati prima di accedere alle risorse bloccate per l'uso, il tempo di attesa causa il rallentamento delle prestazioni di esecuzione del carico di lavoro. 

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica restituisce dettagli di blocchi che è possibile usare come base per la risoluzione dei problemi. È possibile analizzare le query che causano il blocco segnalate, ovvero quelle che introducono la riduzione delle prestazioni correlate al blocco, e quindi rimuoverle. In alcuni casi potrebbe essere utile ottimizzare le query che causano il blocco.

Il modo più semplice e sicuro per attenuare il problema consiste nel mantenere brevi le transazioni e nel ridurre l'impatto del blocco causato dalle query più dispendiose. È possibile suddividere un batch di grandi dimensioni di operazioni in operazioni più piccole. È buona norma ridurre l'impatto del blocco aumentando il più possibile l'efficienza della query. Ridurre le analisi di grandi dimensioni perché aumentano le possibilità di deadlock e influiscono negativamente sulle prestazioni generali del database. Per le query identificate che causano il blocco, è possibile creare nuovi indici o aggiungere colonne all'indice esistente per evitare scansioni di tabella. 

Per altre informazioni, vedere [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (Come risolvere i problemi di blocco causati dall'escalation blocchi in SQL Server).

## <a name="increased-maxdop"></a>Valore di MAXDOP aumentato

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui un piano di esecuzione di query scelto è stato eseguito in parallelo in misura maggiore del previsto. Query Optimizer nel database SQL può migliorare le prestazioni del carico di lavoro eseguendo query in parallelo per velocizzare le operazioni ove possibile. In alcuni casi, i thread di lavoro che elaborano in parallelo una query trascorrono più tempo in attesa della sincronizzazione e dell'unione dei risultati reciproche, rispetto all'esecuzione della stessa query con meno thread di lavoro paralleli o, in alcuni casi, con un unico thread.

Un sistema esperto analizza le prestazioni del database correnti confrontandole con il periodo della baseline e determina se una query eseguita in precedenza è più lenta rispetto a prima perché il piano di esecuzione di query viene eseguito in parallelo più di quanto necessario.

L'opzione di configurazione del server MAXDOP nel database SQL viene usata per controllare il numero di core CPU che possono essere usati per eseguire la stessa query in parallelo. 

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica restituisce gli hash di query correlati alle query per cui la durata di esecuzione è aumentata a causa di una parallelizzazione maggiore rispetto al necessario. Il log restituisce anche i tempi di attesa CXP. Tale tempo rappresenta il tempo che un singolo thread organizzatore/coordinatore (thread 0) rimane in attesa della terminazione di tutti gli altri thread prima di unire i risultati e procedere. Il log di diagnostica restituisce anche i tempi di attesa complessivi per l'esecuzione delle query con prestazioni ridotte. È possibile usare queste informazioni come base per la risoluzione dei problemi.

Ottimizzare o semplificare prima di tutto le query più complesse. ad esempio suddividendo processi batch di grandi dimensioni in processi di dimensioni minori. Assicurarsi inoltre che siano stati creati indici a supporto delle query. È anche possibile applicare manualmente l'opzione di massimo grado di parallelismo (MAXDOP) per una query contrassegnata con prestazioni insufficienti. Per configurare questa operazione tramite T-SQL, vedere [Configurare l'opzione di configurazione del server max degree of parallelism](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

L'impostazione dell'opzione di configurazione server MAXDOP su zero (0) come valore predefinito indica che il database SQL può usare tutti i core CPU logici disponibili per parallelizzare i thread per l'esecuzione di una singola query. L'impostazione di MAXDOP su uno (1) indica che è possibile usare un solo core per l'esecuzione di una singola query. In pratica, ciò significa che il parallelismo è disattivato. A seconda del caso, dei core disponibili per il database e delle informazioni del log di diagnostica, è possibile impostare l'opzione MAXDOP sul numero di core per l'esecuzione di query parallele che consente di risolvere il problema nel caso specifico.

## <a name="pagelatch-contention"></a>Contesa di latch di pagina

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa della contesa di latch di pagina rispetto alla baseline del carico di lavoro dei sette giorni precedenti.

I latch sono meccanismi di sincronizzazione leggeri usati dal database SQL per abilitare il multithreading per garantire la coerenza delle strutture in memoria che includono indici, pagine di dati e altre strutture interne.

Nel database SQL sono disponibili molti tipi di latch. Per semplificare, i latch di buffer vengono usati per proteggere le pagine in memoria nel pool di buffer. I latch di I/O vengono usati per proteggere le pagine non ancora caricate nel pool di buffer. Ogni volta che i dati vengono scritti o letti in una pagina nel pool di buffer, un thread di lavoro deve prima acquisire un latch di buffer per la pagina. Ogni volta che un thread di lavoro tenta di accedere a una pagina che non è già disponibile nel pool di buffer in memoria, viene eseguita una richiesta di I/O per caricare le informazioni necessarie dall'archiviazione. Questa sequenza di eventi indica una forma più grave di riduzione delle prestazioni.

La contesa di latch di pagina si verifica quando più thread tentano di acquisire contemporaneamente latch sulla stessa struttura in memoria con il conseguente aumento del tempo di attesa per l'esecuzione delle query. In caso di contesa I/O di latch di pagina quando occorre accedere ai dati dall'archiviazione, il tempo di attesa è anche maggiore. Ciò può influire notevolmente sulle prestazioni del carico di lavoro. La contesa di latch di pagina è lo scenario più comune di thread in attesa tra loro e in conflitto per l'accesso alle risorse su più CPU.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include informazioni dettagliate sulle contese di latch di pagina. È possibile usare queste informazioni come base per la risoluzione dei problemi.

Dato che il latch di pagina è un meccanismo di controllo interno del database SQL, l'uso di questo meccanismo viene determinato automaticamente. Le decisioni correlate alle applicazioni, ad esempio la progettazione dello schema, possono influire sul funzionamento del latch di pagina a causa del comportamento deterministico dei latch.

Un metodo per gestire una contesa di latch consiste nel sostituire una chiave di indice sequenziale con una chiave non sequenziale per distribuire uniformemente gli inserimenti su un intervallo di indici. Una colonna iniziale nell'indice distribuisce in genere il carico di lavoro in modo proporzionale. Un altro metodo da considerare consiste nel partizionamento delle tabelle. La creazione di uno schema di partizionamento hash con una colonna calcolata in una tabella partizionata è un approccio comune per attenuare la contesa di latch eccessiva. Nel caso di contesa I/O di latch di pagina l'introduzione degli indici è utile per ridurre questo problema di prestazioni. 

Per altre informazioni, vedere il documento in formato PDF [Diagnose and resolve latch contention on SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (Diagnosi e risoluzione delle contese di latch in SQL Server).

## <a name="missing-index"></a>Indice mancante

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa di un indice mancante rispetto alla baseline dei sette giorni precedenti.

Per velocizzare le prestazioni delle query, viene usato un indice che consente di accedere rapidamente ai dati della tabella grazie alla riduzione del numero di pagine dei set di dati che devono essere visitate o analizzate.

Le query specifiche che hanno causato una riduzione delle prestazioni vengono identificate tramite il rilevamento, per cui la creazione degli indici influirebbe favorevolmente sulle prestazioni.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include gli hash delle query identificate con effetti sulle prestazioni del carico di lavoro. È possibile creare indici per queste query. È anche possibile ottimizzare o rimuovere queste query se non sono necessarie. È buona norma evitare di eseguire query sui dati che non vengono usati.

> [!TIP]
> L'intelligence incorporata nel database SQL può gestire automaticamente gli indici con le prestazioni migliori per i database.
>
> Per un'ottimizzazione continua delle prestazioni del database SQL, è consigliabile abilitare l'[ottimizzazione automatica del database SQL](sql-database-automatic-tuning.md). Questa funzionalità univoca di intelligence incorporata nel database SQL consente di monitorare in modo continuo il database SQL, nonché di ottimizzare e creare automaticamente gli indici per i database.
>

## <a name="new-query"></a>Nuova query

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica che è stata rilevata una nuova query con prestazioni insufficienti e con impatto sulle prestazioni del carico di lavoro rispetto alla baseline delle prestazioni dei sette giorni precedenti.

La scrittura di una query con buone prestazioni può essere un'attività complessa. Per altre informazioni sulla scrittura di query, vedere [Writing SQL queries](https://msdn.microsoft.com/library/bb264565.aspx) (Scrittura di query SQL). Per ottimizzare le prestazioni delle query esistenti, vedere [Ottimizzazione delle query](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include informazioni sulle due nuove query che usano maggiormente la CPU, includendo gli hash corrispondenti. Dato che la query rilevata influisce sulle prestazioni del carico di lavoro, è possibile ottimizzarla. È buona norma recuperare solo i dati necessari. È anche consigliabile usare query con una clausola WHERE. Si consiglia inoltre di semplificare le query più complesse e suddividerle in query di dimensioni inferiori. Un'altra buona norma consiste nel suddividere query in batch di grandi dimensioni in query in batch più piccole. L'introduzione di indici per le nuove query è in genere consigliabile per ridurre questo problema di prestazioni.

Valutare la possibilità di usare [Informazioni dettagliate prestazioni query del database SQL di Azure](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Statistiche di attesa non comune

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una riduzione delle prestazioni del carico di lavoro in cui vengono identificate le query con prestazioni ridotte rispetto alla baseline del carico di lavoro dei sette giorni precedenti.

In questo caso, il sistema non può classificare le query con prestazioni scarse in nessun'altra categoria di prestazioni rilevabili standard, ma è in grado di rilevare la statistica di attesa responsabile della regressione. Queste query vengono quindi considerate con *statistiche di attesa non comune* e viene anche esposta la statistica di attesa non comune responsabile della regressione. 

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include informazioni sui dettagli del tempo di attesa non comune, sugli hash di query delle query interessate e sui tempi di attesa.

Dato che in questo caso il sistema non è stato in grado di identificare la causa radice delle query con prestazioni ridotte, le informazioni di diagnostica rappresentano un buon punto di partenza per una risoluzione dei problemi manuale. È possibile ottimizzare le prestazioni di queste query. È buona norma recuperare solo i dati che devono essere usati e semplificare e suddividere le query complesse in query di dimensioni minori. 

Per altre informazioni sull'ottimizzazione delle prestazioni delle query, vedere [Ottimizzazione delle query](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contesa di TempDB

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione delle prestazioni del database in cui è presente un collo di bottiglia di thread che tentano di accedere a risorse di tempDB. (Questa condizione non è correlata all'I/O.) Lo scenario tipico per questo problema di prestazioni è la presenza di centinaia di query simultanee che creano, usano e rilasciano tutte le tabelle tempDB di piccole dimensioni. Il sistema ha rilevato che il numero di query simultanee che usano le stesse tabelle tempDB è aumentato in modo statisticamente significativo sufficiente da influire sulle prestazioni del database rispetto alla baseline delle prestazioni degli ultimi sette giorni.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include informazioni dettagliate sulle contese di tempDB. È possibile usare queste informazioni come punto di partenza per la risoluzione dei problemi. Esistono due operazioni che è possibile eseguire per risolvere questo tipo di contesa e aumentare la velocità effettiva del carico di lavoro globale, ovvero è possibile interrompere l'uso di tabelle temporanee e usare tabelle ottimizzate per la memoria. 

Per altre informazioni, vedere [Introduzione alle tabelle ottimizzate per la memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Carenza di DTU nel pool elastico

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database rispetto alla baseline dei sette giorni precedenti. La causa è la carenza di DTU disponibili nel pool elastico della sottoscrizione. 

Le risorse nel database SQL vengono spesso denominate [risorse DTU](sql-database-what-is-a-dtu.md) e sono costituite da una misura combinata di risorse di CPU e I/O (I/O del log delle transazioni e dei dati). Le [risorse del pool elastico di Azure](sql-database-elastic-pool.md) vengono usate come un pool di risorse eDTU disponibili condivise tra più database a scopo di ridimensionamento. Quando le risorse eDTU disponibili nel pool elastico non sono in numero sufficiente per supportare tutti i database nel pool, il sistema rileva un problema di prestazioni per carenza di eDTU nel pool elastico.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include informazioni sul pool elastico, elenca i database che usano più DTU e restituisce la percentuale delle DTU del pool usate dal database più dispendioso.

Dato che questa condizione delle prestazioni è correlata a più database che usano lo stesso pool di eDTU del pool elastico, la procedura di risoluzione dei problemi è focalizzata sui database che usano più DTU. È possibile ridurre il carico di lavoro nei database più dispendiosi, ad esempio ottimizzando le query più dispendiose su tali database. È anche possibile assicurarsi di evitare l'esecuzione di query su dati che non si prevede di usare. Un altro approccio consiste nell'ottimizzare le applicazioni coinvolte nei database che usano più DTU e nel ridistribuire il carico di lavoro tra più database.

Se la riduzione e l'ottimizzazione del carico di lavoro corrente nei database che usano più DTU non sono possibili, valutare la possibilità di aumentare il piano tariffario del pool elastico. Tale aumento determina l'incremento delle DTU disponibili nel pool elastico.

## <a name="plan-regression"></a>Regressione di piani

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in base alla quale il database SQL inizia a usare un piano di esecuzione delle query non ottimale. Tale piano causa in genere un aumento del tempo di esecuzione di query che comporta a sua volta tempi di attesa superiori per la query corrente e le altre.

Il database SQL determina il piano di esecuzione delle query con il minor costo di esecuzione. Dato che il tipo delle query e dei carichi di lavoro può variare, talvolta i piani esistenti non sono più efficienti o è possibile che il database SQL non esegua una valutazione corretta. In termini di correzione i piani di esecuzione delle query possono essere forzati manualmente.

Questo modello di prestazioni rilevabili combina tre casi diversi di regressione di piani, ovvero regressione di nuovi piani, regressione di piani precedenti e carico di lavoro modificato di piani esistenti. Il tipo specifico di regressione del piano che si è verificato è indicato nella proprietà *details* nel log di diagnostica.

La condizione di regressione di nuovi piani fa riferimento a uno stato in cui il database SQL inizia l'esecuzione di un nuovo piano non efficiente come quello precedente. La condizione di regressione di piani precedenti fa riferimento allo stato in cui il database SQL passa dall'uso di un nuovo piano più efficiente all'uso del piano precedente non efficiente come quello nuovo. La regressione correlata al carico di lavoro modificato di piani esistenti fa riferimento allo stato in cui il piano precedente e quello nuovo vengono continuamente alternati, con una preferenza verso il piano con prestazioni inferiori.

Per altre informazioni sulle regressioni di piani, vedere [What is plan regression in SQL server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/) (Informazioni sulla regressione di piani in SQL Server). 

### <a name="troubleshooting"></a>risoluzione dei problemi

Il logo di diagnostica include gli hash delle query, l'ID del piano appropriato, l'ID del piano non valido e gli ID delle query. È possibile usare queste informazioni come base per la risoluzione dei problemi.

È possibile analizzare i dati per individuare il piano con prestazioni migliori per le query specifiche che è possibile identificare in base agli hash forniti. Dopo aver determinato il piano più adatto alle query, è possibile applicarlo manualmente. 

Per altre informazioni, vedere [How SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/) (Come SQL Server impedisce le regressioni di piani).

> [!TIP]
> L'intelligence incorporata nel database SQL può gestire automaticamente i piani di esecuzione delle query con le prestazioni migliori per i database in uso.
>
> Per un'ottimizzazione continua delle prestazioni del database SQL, è consigliabile abilitare l'[ottimizzazione automatica del database SQL](sql-database-automatic-tuning.md). Questa funzionalità univoca di intelligence incorporata nel database SQL consente di monitorare in modo continuo il database SQL, nonché di ottimizzare e creare automaticamente i piani di esecuzione delle query con prestazioni migliori per i database.
>

## <a name="database-scoped-configuration-value-change"></a>Modifica del valore di configurazione in ambito database

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui viene rilevata una modifica nella configurazione in ambito database che causa una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei sette giorni precedenti. Ciò indica che le modifiche recenti apportate alla configurazione in ambito database non sembrano essere utili per le prestazioni del database in uso.

La modifica della configurazione in ambito database può essere impostata per ogni singolo database. Questa configurazione viene usata caso per caso per ottimizzare le singole prestazioni del database. Le opzioni seguenti possono essere configurate per ogni singolo database: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>risoluzione dei problemi

Il log di diagnostica include le modifiche della configurazione in ambito database apportate di recente che hanno causato la riduzione delle prestazioni rispetto al comportamento del carico di lavoro dei sette giorni precedenti. È possibile ripristinare le modifiche di configurazione reimpostando i valori precedenti. Si possono anche ottimizzare i singoli valori fino a ottenere il livello di prestazioni desiderato. È possibile copiare i valori di configurazione in ambito database da un database simile con prestazioni soddisfacenti. Se non si riesce a risolvere i problemi di prestazioni, ripristinare i valori predefiniti del database SQL e tentare di eseguire un'ottimizzazione partendo da questa baseline.

Per altre informazioni sull'ottimizzazione della configurazione in ambito database e sulla sintassi T-SQL per la modifica della configurazione, vedere [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Client lento

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui il client che usa il database SQL non è in grado di usare l'output del database alla velocità con cui quest'ultimo invia i risultati. Dato che il database SQL non archivia i risultati delle query eseguite in un buffer, subisce un rallentamento e rimane in attesa che il client usi i risultati della query trasmessi prima di procedere. Questa condizione potrebbe anche essere correlata a una rete lenta che non è in grado di trasmettere in modo abbastanza rapido gli output dal database SQL al client che li usa.

Questa condizione viene generata solo se viene rilevata una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei sette giorni precedenti. Questo problema di prestazioni viene rilevato solo se si verifica una riduzione delle prestazioni statisticamente significativa rispetto al comportamento delle prestazioni precedenti.

### <a name="troubleshooting"></a>risoluzione dei problemi

Questo modello di prestazioni rilevabili indica una condizione sul lato client. Sono richiesti interventi di risoluzione dei problemi nell'applicazione sul lato client o nella rete sul lato client. Il log di diagnostica include gli hash e i tempi di attesa delle query che sembrano attendere da più tempo che il client ne usi i risultati nelle due ore precedenti. È possibile usare queste informazioni come base per la risoluzione dei problemi.

È possibile ottimizzare le prestazioni dell'applicazione per l'uso di queste query oppure valutare possibili problemi di latenza della rete. Dato che il problema di riduzione delle prestazioni è basato su una modifica nella baseline delle prestazioni negli ultimi sette giorni, è possibile verificare se questo evento di regressione delle prestazioni è causato da modifiche recenti delle condizioni per l'applicazione o la rete. 

## <a name="pricing-tier-downgrade"></a>Downgrade del piano tariffario

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui è stato eseguito il downgrade del piano tariffario per la sottoscrizione del database SQL. A causa della riduzione delle risorse (DTU) disponibili per il database, il sistema ha rilevato un calo delle prestazioni correnti del database rispetto alla baseline dei sette giorni precedenti.

Potrebbe essersi verificata anche una condizione in cui è stato eseguito il downgrade del piano tariffario della sottoscrizione del database SQL, aggiornato in seguito a un livello superiore entro un breve periodo di tempo. Il rilevamento di questa riduzione temporanea del livello delle prestazioni è indicato nella sezione dei dettagli del log di diagnostica come downgrade e aggiornamento del piano tariffario.

### <a name="troubleshooting"></a>risoluzione dei problemi

Se il piano tariffario è stato ridotto e di conseguenza sono state ridotte le DTU disponibili per il database SQL e le prestazioni sono soddisfacenti, non è necessario eseguire alcuna azione. Se il piano tariffario è stato ridotto e non si è soddisfatti delle prestazioni dei database SQL, ridurre i carichi di lavoro del database o provare a passare a un livello superiore del piano tariffario.

## <a name="recommended-troubleshooting-flow"></a>Flusso di risoluzione dei problemi consigliato

 Per un approccio consigliato alla risoluzione dei problemi di prestazioni con Intelligent Insights, seguire il diagramma di flusso.

Accedere a Intelligent Insights nel portale di Azure passando ad Analisi SQL di Azure. Tentare di individuare l'avviso relativo alle prestazioni in arrivo e selezionarlo. Identificare cosa accade nella pagina dei rilevamenti. Osservare l'analisi della causa radice del problema indicata, il testo e le tendenze nel tempo della query e l'evoluzione degli eventi imprevisti. Tentare di risolvere il problema usando i consigli di Intelligent Insights per ridurre il problema di prestazioni. 

[![Diagramma di flusso per la risoluzione dei problemi](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selezionare il diagramma di flusso per scaricarne una versione in formato PDF.

Intelligent Insights in genere ha bisogno di un'ora di tempo per eseguire l'analisi della causa radice del problema di prestazioni. Se non è possibile individuare il problema in Intelligent Insights ed è cruciale trovare una soluzione, usare Query Store per identificare manualmente la causa radice del problema di prestazioni. (In genere, questi problemi risultano generati nell'ultima ora.) Per altre informazioni, vedere [Monitoraggio delle prestazioni con Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui concetti di [Intelligent Insights](sql-database-intelligent-insights.md).
- Usare il [log di diagnostica delle prestazioni del database SQL di Azure generato da Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitorare un [database SQL di Azure usando Analisi SQL di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
