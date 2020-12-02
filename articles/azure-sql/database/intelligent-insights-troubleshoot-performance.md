---
title: Risolvere i problemi di prestazioni con Intelligent Insights
description: Intelligent Insights consente di risolvere i problemi relativi alle prestazioni del database SQL di Azure e di Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: c42db1445c939069f334d04ea26d54cdb843c336
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488834"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Risolvere i problemi relativi alle prestazioni del database SQL di Azure e di Azure SQL Istanza gestita con Intelligent Insights
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questa pagina contiene informazioni sui problemi di prestazioni del database SQL di Azure e del Istanza gestita SQL di Azure rilevati tramite il log delle risorse [Intelligent Insights](intelligent-insights-overview.md) . Le metriche e i log delle risorse possono essere trasmessi a [log di monitoraggio di Azure](../../azure-monitor/insights/azure-sql.md), [Hub eventi](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)di Azure, [archiviazione di Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)o una soluzione di terze parti per le funzionalità di avviso e creazione di report di DevOps personalizzate.

> [!NOTE]
> Per una guida rapida alla risoluzione dei problemi relativi alle prestazioni con Intelligent Insights, vedere il diagramma di flusso per la [risoluzione dei problemi consigliato](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) in questo documento.
>
> Intelligent Insights è una funzionalità di anteprima, non disponibile nelle aree geografiche seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 1 e Stati Uniti orientali 1.

## <a name="detectable-database-performance-patterns"></a>Modelli di prestazioni del database rilevabili

Intelligent Insights rileva automaticamente i problemi di prestazioni in base ai tempi di attesa, agli errori o ai timeout di esecuzione delle query. Intelligent Insights output ha rilevato modelli di prestazioni nel log delle risorse. La tabella seguente riepiloga i modelli di prestazioni rilevabili.

| Modelli di prestazioni rilevabili | Database SQL di Azure | Istanza gestita di SQL di Azure |
| :------------------- | ------------------- | ------------------- |
| [Raggiungimento dei limiti delle risorse](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Il consumo di risorse disponibili (DTU), dei thread di lavoro del database o delle sessioni di accesso al database disponibili nella sottoscrizione monitorata ha raggiunto i limiti delle risorse. Questa operazione influisce sulle prestazioni. | Il consumo di risorse della CPU sta raggiungendo i limiti delle risorse. Questo influisce sulle prestazioni del database. |
| [Aumento del carico di lavoro](intelligent-insights-troubleshoot-performance.md#workload-increase) | È stato rilevato un aumento o un accumulo continuo di carico di lavoro nel database. Questa operazione influisce sulle prestazioni. | È stato rilevato un aumento del carico di lavoro. Questo influisce sulle prestazioni del database. |
| [Pressione della memoria](intelligent-insights-troubleshoot-performance.md#memory-pressure) | I thread di lavoro che hanno richiesto concessioni di memoria devono attendere le allocazioni di memoria per una quantità di tempo statisticamente significativa o un aumento dell'accumulo di ruoli di lavoro che hanno richiesto concessioni di memoria. Questa operazione influisce sulle prestazioni. | I thread di lavoro che hanno richiesto concessioni di memoria devono attendere le allocazioni di memoria per una quantità di tempo statisticamente significativa. Questo influisce sulle prestazioni del database. |
| [Blocco](intelligent-insights-troubleshoot-performance.md#locking) | È stato rilevato un blocco eccessivo del database che influisce sulle prestazioni. | È stato rilevato un blocco eccessivo del database con effetti sulle prestazioni del database. |
| [Valore di MAXDOP aumentato](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | L'opzione relativa al massimo grado di parallelismo (MAXDOP) è stata modificata, con effetti sull'efficienza di esecuzione delle query. Questa operazione influisce sulle prestazioni. | L'opzione relativa al massimo grado di parallelismo (MAXDOP) è stata modificata, con effetti sull'efficienza di esecuzione delle query. Questa operazione influisce sulle prestazioni. |
| [Contesa di latch di pagina](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Più thread tentano di accedere contemporaneamente alle stesse pagine di buffer di dati in memoria provocando un aumento dei tempi di attesa e causando una contesa di latch di pagina. Questa operazione influisce sulle prestazioni. | Più thread tentano di accedere contemporaneamente alle stesse pagine di buffer di dati in memoria provocando un aumento dei tempi di attesa e causando una contesa di latch di pagina. Questo influisce sulle prestazioni del database. |
| [Indice mancante](intelligent-insights-troubleshoot-performance.md#missing-index) | È stato rilevato un indice mancante che influisce sulle prestazioni. | È stato rilevato un indice mancante con effetti sulle prestazioni del database. |
| [Nuova query](intelligent-insights-troubleshoot-performance.md#new-query) | È stata rilevata una nuova query che influisce sulle prestazioni complessive. | È stata rilevata una nuova query con impatto sulle prestazioni complessive del database. |
| [Statistiche di attesa in aumento](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Sono stati rilevati tempi di attesa del database maggiori che influiscono sulle prestazioni. | Sono stati rilevati tempi di attesa del database in aumento con impatto sulle prestazioni del database. |
| [Contesa di TempDB](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Più thread tentano di accedere alla stessa risorsa di TempDB provocando un collo di bottiglia. Questa operazione influisce sulle prestazioni. | Più thread tentano di accedere alla stessa risorsa di TempDB provocando un collo di bottiglia. Questo influisce sulle prestazioni del database. |
| [Carenza di DTU nel pool elastico](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | La carenza di edtu disponibili nel pool elastico influisce sulle prestazioni. | Non disponibile per Istanza gestita SQL di Azure perché usa il modello vCore. |
| [Regressione del piano](intelligent-insights-troubleshoot-performance.md#plan-regression) | È stato rilevato un nuovo piano o una modifica nel carico di lavoro di un piano esistente. Questa operazione influisce sulle prestazioni. | È stato rilevato un nuovo piano o una modifica nel carico di lavoro di un piano esistente. Questo influisce sulle prestazioni del database. |
| [Modifica del valore di configurazione con ambito database](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | È stata rilevata una modifica della configurazione del database con impatto sulle prestazioni del database. | È stata rilevata una modifica della configurazione del database con impatto sulle prestazioni del database. |
| [Client lento](intelligent-insights-troubleshoot-performance.md#slow-client) | Un client applicazione lento non riesce a usare in modo abbastanza veloce l'output del database. Questa operazione influisce sulle prestazioni. | Un client applicazione lento non riesce a usare in modo abbastanza veloce l'output del database. Questo influisce sulle prestazioni del database. |
| [Downgrade del piano tariffario](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Un'azione di downgrade del piano tariffario ha ridotto le risorse disponibili. Questa operazione influisce sulle prestazioni. | Un'azione di downgrade del piano tariffario ha ridotto le risorse disponibili. Questo influisce sulle prestazioni del database. |

> [!TIP]
> Per l'ottimizzazione continua delle prestazioni dei database, abilitare l' [ottimizzazione automatica](automatic-tuning-overview.md). Questa funzionalità di Intelligence incorporata monitora costantemente il database, ottimizza automaticamente gli indici e applica le correzioni del piano di esecuzione delle query.
>

La sezione seguente descrive in modo più dettagliato i modelli di prestazioni rilevabili.

## <a name="reaching-resource-limits"></a>Raggiungimento dei limiti delle risorse

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili combina i problemi di prestazioni correlati al raggiungimento dei limiti delle risorse disponibili, dei thread di lavoro e delle sessioni. Dopo che il problema è stato rilevato, un campo di descrizione del log di diagnostica indica se il problema di prestazioni è correlato ai limiti delle risorse, dei thread di lavoro o delle sessioni.

Le risorse nel database SQL di Azure vengono in genere definite risorse [DTU](service-tiers-dtu.md) o [vCore](service-tiers-vcore.md) e le risorse in Azure SQL istanza gestita sono denominate risorse vCore. Il modello del raggiungimento dei limiti delle risorse viene riconosciuto quando la riduzione delle prestazioni delle query rilevata è provocata dal raggiungimento di uno qualsiasi dei limiti delle risorse misurate.

La risorsa limiti sessione indica il numero di accessi simultanei disponibili al database. Questo modello di prestazioni viene riconosciuto quando le applicazioni connesse ai database hanno raggiunto il numero di accessi simultanei disponibili al database. Se le applicazioni tentano di usare più sessioni rispetto a quelle disponibili in un database, le prestazioni delle query ne sono influenzate.

Il raggiungimento dei limiti dei thread di lavoro rappresenta un caso di raggiungimento dei limiti delle risorse specifico poiché i thread di lavoro disponibili non vengono conteggiati nell'uso di DTU o vCore. Il raggiungimento dei limiti dei thread di lavoro in un database può causare l'aumento dei tempi di attesa specifici di una risorsa, con conseguente riduzione delle prestazioni delle query.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera hash di query per le query con effetti sulle prestazioni e sulle percentuali di consumo delle risorse. È possibile usare queste informazioni come punto iniziale per ottimizzare il carico di lavoro del database. In particolare, è possibile ottimizzare le query con effetti sulla riduzione delle prestazioni tramite l'aggiunta di indici. Oppure è possibile ottimizzare le applicazioni con una distribuzione più uniforme dei carichi di lavoro. Se non è possibile ridurre i carichi di lavoro o eseguire ottimizzazioni, provare ad aumentare il piano tariffario della sottoscrizione del database per aumentare la quantità di risorse disponibili.

Se sono stati raggiunti i limiti delle sessioni disponibili, è possibile ottimizzare le applicazioni riducendo il numero di accessi al database. Se non si è in grado di ridurre il numero di accessi dalle applicazioni al database, provare ad aumentare il piano tariffario della sottoscrizione del database. In alternativa, è possibile suddividere e spostare il database in più database per una distribuzione più equilibrata del carico di lavoro.

Per ulteriori suggerimenti sulla risoluzione dei limiti delle sessioni, vedere [How to deal with the limits of Maximum Logins](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Per informazioni sui limiti a livello del server e della sottoscrizione, vedere [Cenni preliminari sui limiti delle risorse in un server](resource-limits-logical-server.md) .

## <a name="workload-increase"></a>Aumento del carico di lavoro

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni identifica i problemi causati da un aumento o, in forma peggiore, da un accumulo del carico di lavoro.

Il rilevamento viene eseguito tramite una combinazione di diverse metriche. La metrica di base misurata è la rilevazione di un aumento del carico di lavoro rispetto alla baseline del carico di lavoro precedente. L'altra forma di rilevamento si basa sulla misurazione di un notevole aumento di thread di lavoro attivi, sufficiente per influire sulle prestazioni delle query.

Nella sua forma più grave, il carico di lavoro potrebbe essere continuamente accumulato a causa dell'impossibilità di un database di gestire il carico di lavoro. La conseguenza è un continuo aumento delle dimensioni del carico di lavoro, ovvero la condizione che determina l'accumulo. A causa di questa condizione, aumenta il tempo di attesa dell'esecuzione per il carico di lavoro. Questa condizione rappresenta uno dei più gravi problemi di prestazioni di database. Questo problema viene rilevato monitorando l'aumento del numero di thread di lavoro interrotti.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica riporta il numero di query la cui esecuzione è aumentata e l'hash della query che contribuisce maggiormente all'aumento del carico di lavoro. È possibile usare queste informazioni come punto di partenza per l'ottimizzazione del carico di lavoro. La query identificata come quella che contribuisce maggiormente all'aumento del carico di lavoro è particolarmente utile come punto di partenza.

Si potrebbe valutare una distribuzione più uniforme dei carichi di lavoro nel database. Prendere in considerazione l'ottimizzazione della query che influisce sulle prestazioni tramite l'aggiunta di indici. Si può anche distribuire il carico di lavoro tra più database. Se queste soluzioni non sono possibili, è consigliabile aumentare il piano tariffario della sottoscrizione del database per aumentare la quantità di risorse disponibili.

## <a name="memory-pressure"></a>Utilizzo elevato della memoria

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database causata da un utilizzo elevato della memoria o, nella forma più grave, da una condizione di accumulo d richieste alla memoria rispetto alla baseline delle prestazioni dei sette giorni precedenti.

L'utilizzo elevato della memoria indica una condizione delle prestazioni in cui è presente un numero elevato di thread di lavoro che richiedono concessioni di memoria. Il volume elevato causa una condizione di utilizzo elevato della memoria in cui il database non è in grado di allocare in modo efficiente la memoria a tutti i ruoli di lavoro che lo richiedono. Uno dei motivi più comuni per questo problema è correlato alla quantità di memoria disponibile per il database da un lato. Dall'altro, un aumento del carico di lavoro causa l'aumento dei thread di lavoro e l'utilizzo elevato della memoria.

La forma più grave di utilizzo elevato della memoria è la condizione di accumulo di richieste di memoria. Questa condizione indica la presenza di un numero di thread di lavoro che richiede concessioni di memoria maggiore rispetto alle query che rilasciano la memoria. Anche questo numero di thread di lavoro che richiedono concessioni di memoria potrebbe essere in continua crescita (accumulo), perché il motore di database non è in grado di allocare memoria in modo sufficientemente efficiente per soddisfare la richiesta. La condizione di accumulo di richieste alla memoria rappresenta uno dei più gravi problemi di prestazioni di database.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica restituisce i dettagli dell'archivio di oggetti di memoria con il clerk di memoria, ovvero thread di lavoro, contrassegnato come il motivo principale per l'uso elevato della memoria e i timestamp pertinenti. È possibile usare queste informazioni come base per la risoluzione dei problemi.

È possibile ottimizzare o rimuovere query correlate ai clerk con l'utilizzo della memoria più elevato. È anche possibile assicurarsi di evitare l'esecuzione di query su dati che non si prevede di usare. Una buona norma consiste nell'usare sempre una clausola WHERE nelle query. Inoltre, è consigliabile creare indici non cluster per la ricerca dei dati, anziché eseguirne l'analisi.

È anche possibile ridurre il carico di lavoro ottimizzandolo o distribuendolo su più database. Oppure è possibile distribuire il carico di lavoro tra più database. Se queste soluzioni non sono possibili, provare ad aumentare il piano tariffario del database per aumentare la quantità di risorse di memoria disponibili per il database.

Per altre informazioni sulla risoluzione dei problemi, vedere [Memory grants meditation: The mysterious SQL Server memory consumer with many names](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994) (Riflessione sulle concessioni di memoria: il misterioso consumer di memoria di SQL Server con molti nomi).

## <a name="locking"></a>Blocco

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database in cui viene rilevato un blocco eccessivo del database rispetto alla baseline delle prestazioni dei sette giorni precedenti.

Nei sistemi RDBMS moderni il blocco è essenziale per l'implementazione multithreading in cui le prestazioni sono massimizzate tramite l'esecuzione di più thread di lavoro simultanei e di transazioni di database parallele ove possibile. Il termine blocco in questo contesto si riferisce al meccanismo di accesso predefinito in cui solo un'unica transazione può accedere in modo esclusivo a righe, pagine, tabelle e file necessari e non entra in conflitto con un'altra transazione per accedere alle risorse. Quando la transazione che ha bloccato le risorse per l'uso è completata, il blocco su tali risorse viene rilasciato consentendo ad altre transazioni di accedere alle risorse necessarie. Per altre informazioni sul blocco, vedere [Utilizzo dei blocchi in Motore di database](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Se le transazioni eseguite dal motore SQL rimangono in attesa per periodi di tempo prolungati prima di accedere alle risorse bloccate per l'uso, il tempo di attesa causa il rallentamento delle prestazioni di esecuzione del carico di lavoro.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica restituisce dettagli di blocchi che è possibile usare come base per la risoluzione dei problemi. È possibile analizzare le query che causano il blocco segnalate, ovvero quelle che introducono la riduzione delle prestazioni correlate al blocco, e quindi rimuoverle. In alcuni casi potrebbe essere utile ottimizzare le query che causano il blocco.

Il modo più semplice e sicuro per attenuare il problema consiste nel mantenere brevi le transazioni e nel ridurre l'impatto del blocco causato dalle query più dispendiose. È possibile suddividere un batch di grandi dimensioni di operazioni in operazioni più piccole. È buona norma ridurre l'impatto del blocco aumentando il più possibile l'efficienza della query. Ridurre le analisi di grandi dimensioni perché aumentano le possibilità di deadlock e influiscono negativamente sulle prestazioni generali del database. Per le query identificate che causano il blocco, è possibile creare nuovi indici o aggiungere colonne all'indice esistente per evitare scansioni di tabella.

Per altre informazioni, vedere [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (Come risolvere i problemi di blocco causati dall'escalation blocchi in SQL Server).

## <a name="increased-maxdop"></a>Valore di MAXDOP aumentato

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui un piano di esecuzione di query scelto è stato eseguito in parallelo in misura maggiore del previsto. Il Query Optimizer può migliorare le prestazioni del carico di lavoro eseguendo query in parallelo per velocizzare le operazioni laddove possibile. In alcuni casi, i thread di lavoro che elaborano in parallelo una query trascorrono più tempo in attesa della sincronizzazione e dell'unione dei risultati reciproche, rispetto all'esecuzione della stessa query con meno thread di lavoro paralleli o, in alcuni casi, con un unico thread.

Un sistema esperto analizza le prestazioni del database correnti confrontandole con il periodo della baseline e determina se una query eseguita in precedenza è più lenta rispetto a prima perché il piano di esecuzione di query viene eseguito in parallelo più di quanto necessario.

L'opzione di configurazione del server MAXDOP viene usata per controllare il numero di core CPU che possono essere usati per eseguire la stessa query in parallelo.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica restituisce gli hash di query correlati alle query per cui la durata di esecuzione è aumentata a causa di una parallelizzazione maggiore rispetto al necessario. Il log restituisce anche i tempi di attesa CXP. Tale tempo rappresenta il tempo che un singolo thread organizzatore/coordinatore (thread 0) rimane in attesa della terminazione di tutti gli altri thread prima di unire i risultati e procedere. Il log di diagnostica restituisce anche i tempi di attesa complessivi per l'esecuzione delle query con prestazioni ridotte. È possibile usare queste informazioni come base per la risoluzione dei problemi.

Ottimizzare o semplificare prima di tutto le query più complesse. ad esempio suddividendo processi batch di grandi dimensioni in processi di dimensioni minori. Assicurarsi inoltre che siano stati creati indici a supporto delle query. È anche possibile applicare manualmente l'opzione di massimo grado di parallelismo (MAXDOP) per una query contrassegnata con prestazioni insufficienti. Per configurare questa operazione tramite T-SQL, vedere [Configurare l'opzione di configurazione del server max degree of parallelism](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

L'impostazione dell'opzione di configurazione del server MAXDOP su zero (0) come valore predefinito indica che il database può usare tutti i core CPU disponibili per parallelizzare i thread per l'esecuzione di una singola query. L'impostazione di MAXDOP su uno (1) indica che è possibile usare un solo core per l'esecuzione di una singola query. In pratica, ciò significa che il parallelismo è disattivato. A seconda del caso, dei core disponibili per il database e delle informazioni del log di diagnostica, è possibile impostare l'opzione MAXDOP sul numero di core per l'esecuzione di query parallele che consente di risolvere il problema nel caso specifico.

## <a name="pagelatch-contention"></a>Contesa di latch di pagina

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa della contesa di latch di pagina rispetto alla baseline del carico di lavoro dei sette giorni precedenti.

I latch sono meccanismi di sincronizzazione leggeri usati per abilitare il multithreading. per garantire la coerenza delle strutture in memoria che includono indici, pagine di dati e altre strutture interne.

Sono disponibili molti tipi di latch. Per semplificare, i latch di buffer vengono usati per proteggere le pagine in memoria nel pool di buffer. I latch di I/O vengono usati per proteggere le pagine non ancora caricate nel pool di buffer. Ogni volta che i dati vengono scritti o letti in una pagina nel pool di buffer, un thread di lavoro deve prima acquisire un latch di buffer per la pagina. Ogni volta che un thread di lavoro tenta di accedere a una pagina che non è già disponibile nel pool di buffer in memoria, viene eseguita una richiesta di I/O per caricare le informazioni necessarie dall'archiviazione. Questa sequenza di eventi indica una forma più grave di riduzione delle prestazioni.

La contesa di latch di pagina si verifica quando più thread tentano di acquisire contemporaneamente latch sulla stessa struttura in memoria con il conseguente aumento del tempo di attesa per l'esecuzione delle query. In caso di contesa I/O di latch di pagina quando occorre accedere ai dati dall'archiviazione, il tempo di attesa è anche maggiore. Ciò può influire notevolmente sulle prestazioni del carico di lavoro. La contesa di latch di pagina è lo scenario più comune di thread in attesa tra loro e in conflitto per l'accesso alle risorse su più CPU.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include informazioni dettagliate sulle contese di latch di pagina. È possibile usare queste informazioni come base per la risoluzione dei problemi.

Poiché un latch è un meccanismo di controllo interno, determina automaticamente quando usarli. Le decisioni correlate alle applicazioni, ad esempio la progettazione dello schema, possono influire sul funzionamento del latch di pagina a causa del comportamento deterministico dei latch.

Un metodo per gestire una contesa di latch consiste nel sostituire una chiave di indice sequenziale con una chiave non sequenziale per distribuire uniformemente gli inserimenti su un intervallo di indici. Una colonna iniziale nell'indice distribuisce in genere il carico di lavoro in modo proporzionale. Un altro metodo da considerare consiste nel partizionamento delle tabelle. La creazione di uno schema di partizionamento hash con una colonna calcolata in una tabella partizionata è un approccio comune per attenuare la contesa di latch eccessiva. Nel caso di contesa I/O di latch di pagina l'introduzione degli indici è utile per ridurre questo problema di prestazioni.

Per altre informazioni, vedere il documento in formato PDF [Diagnose and resolve latch contention on SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (Diagnosi e risoluzione delle contese di latch in SQL Server).

## <a name="missing-index"></a>Indice mancante

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa di un indice mancante rispetto alla baseline dei sette giorni precedenti.

Per velocizzare le prestazioni delle query, viene usato un indice che consente di accedere rapidamente ai dati della tabella grazie alla riduzione del numero di pagine dei set di dati che devono essere visitate o analizzate.

Le query specifiche che hanno causato una riduzione delle prestazioni vengono identificate tramite il rilevamento, per cui la creazione degli indici influirebbe favorevolmente sulle prestazioni.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include gli hash delle query identificate con effetti sulle prestazioni del carico di lavoro. È possibile creare indici per queste query. È anche possibile ottimizzare o rimuovere queste query se non sono necessarie. È buona norma evitare di eseguire query sui dati che non vengono usati.

> [!TIP]
> Sapevi che l'Intelligence incorporata è in grado di gestire automaticamente gli indici con le prestazioni migliori per i tuoi database?
>
> Per ottimizzare le prestazioni continue, è consigliabile abilitare l' [ottimizzazione automatica](automatic-tuning-overview.md). Questa funzionalità di intelligence integrata univoca monitora costantemente il database e ottimizza e crea automaticamente gli indici per i database.
>

## <a name="new-query"></a>Nuova query

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica che è stata rilevata una nuova query con prestazioni insufficienti e con impatto sulle prestazioni del carico di lavoro rispetto alla baseline delle prestazioni dei sette giorni precedenti.

La scrittura di una query con buone prestazioni può essere un'attività complessa. Per altre informazioni sulla scrittura di query, vedere [Writing SQL queries](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) (Scrittura di query SQL). Per ottimizzare le prestazioni delle query esistenti, vedere [Ottimizzazione delle query](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include informazioni sulle due nuove query che usano maggiormente la CPU, includendo gli hash corrispondenti. Dato che la query rilevata influisce sulle prestazioni del carico di lavoro, è possibile ottimizzarla. È buona norma recuperare solo i dati necessari. È anche consigliabile usare query con una clausola WHERE. Si consiglia inoltre di semplificare le query più complesse e suddividerle in query di dimensioni inferiori. Un'altra buona norma consiste nel suddividere query in batch di grandi dimensioni in query in batch più piccole. L'introduzione di indici per le nuove query è in genere consigliabile per ridurre questo problema di prestazioni.

Nel database SQL di Azure provare a usare [informazioni dettagliate prestazioni query](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Maggiore statistica di attesa

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una riduzione delle prestazioni del carico di lavoro in cui vengono identificate le query con prestazioni ridotte rispetto alla baseline del carico di lavoro dei sette giorni precedenti.

In questo caso, il sistema non può classificare le query con prestazioni scarse in nessun'altra categoria di prestazioni rilevabili standard, ma è in grado di rilevare la statistica di attesa responsabile della regressione. Queste query vengono quindi considerate con *statistiche di attesa in aumento* e viene anche esposta la statistica di attesa responsabile della regressione.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include informazioni sui dettagli del tempo di attesa in aumento e sugli hash di query delle query interessate.

Dato che in questo caso il sistema non è stato in grado di identificare la causa radice delle query con prestazioni ridotte, le informazioni di diagnostica rappresentano un buon punto di partenza per una risoluzione dei problemi manuale. È possibile ottimizzare le prestazioni di queste query. È buona norma recuperare solo i dati che devono essere usati e semplificare e suddividere le query complesse in query di dimensioni minori.

Per ulteriori informazioni sull'ottimizzazione delle prestazioni delle query, vedere [ottimizzazione](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105))delle query.

## <a name="tempdb-contention"></a>Contesa di TempDB

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione delle prestazioni del database in cui è presente un collo di bottiglia di thread che tentano di accedere a risorse di tempDB. Questa condizione non è correlata a IO. Lo scenario tipico per questo problema di prestazioni è costituito da centinaia di query simultanee che creano, usano e rilasciano le tabelle tempDB di piccole dimensioni. Il sistema ha rilevato che il numero di query simultanee che usano le stesse tabelle tempDB è aumentato in modo statisticamente significativo sufficiente da influire sulle prestazioni del database rispetto alla baseline delle prestazioni degli ultimi sette giorni.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include informazioni dettagliate sulle contese di tempDB. È possibile usare queste informazioni come punto di partenza per la risoluzione dei problemi. Esistono due operazioni che è possibile eseguire per risolvere questo tipo di contesa e aumentare la velocità effettiva del carico di lavoro globale, ovvero è possibile interrompere l'uso di tabelle temporanee e usare tabelle ottimizzate per la memoria.

Per altre informazioni, vedere [Introduzione alle tabelle ottimizzate per la memoria](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Carenza di DTU nel pool elastico

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database rispetto alla baseline dei sette giorni precedenti. La causa è la carenza di DTU disponibili nel pool elastico della sottoscrizione.

[Le risorse del pool elastico di Azure](elastic-pool-overview.md) vengono usate come un pool di risorse disponibili condivise tra più database per motivi di scalabilità. Quando le risorse eDTU disponibili nel pool elastico non sono in numero sufficiente per supportare tutti i database nel pool, il sistema rileva un problema di prestazioni per carenza di eDTU nel pool elastico.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include informazioni sul pool elastico, elenca i database che usano più DTU e restituisce la percentuale delle DTU del pool usate dal database più dispendioso.

Dato che questa condizione delle prestazioni è correlata a più database che usano lo stesso pool di eDTU del pool elastico, la procedura di risoluzione dei problemi è focalizzata sui database che usano più DTU. È possibile ridurre il carico di lavoro nei database più dispendiosi, ad esempio ottimizzando le query più dispendiose su tali database. È anche possibile assicurarsi di evitare l'esecuzione di query su dati che non si prevede di usare. Un altro approccio consiste nell'ottimizzare le applicazioni coinvolte nei database che usano più DTU e nel ridistribuire il carico di lavoro tra più database.

Se la riduzione e l'ottimizzazione del carico di lavoro corrente nei database che usano più DTU non sono possibili, valutare la possibilità di aumentare il piano tariffario del pool elastico. Tale aumento determina l'incremento delle DTU disponibili nel pool elastico.

## <a name="plan-regression"></a>Regressione di piani

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui il database utilizza un piano di esecuzione della query non ottimale. Tale piano causa in genere un aumento del tempo di esecuzione di query che comporta a sua volta tempi di attesa superiori per la query corrente e le altre.

Il motore di database determina il piano di esecuzione della query con il minor costo di esecuzione di una query. Quando il tipo di query e carichi di lavoro cambiano, a volte i piani esistenti non sono più efficienti o forse il motore di database non ha fatto una valutazione ottimale. In termini di correzione i piani di esecuzione delle query possono essere forzati manualmente.

Questo modello di prestazioni rilevabili combina tre casi diversi di regressione di piani, ovvero regressione di nuovi piani, regressione di piani precedenti e carico di lavoro modificato di piani esistenti. Il tipo specifico di regressione del piano che si è verificato è indicato nella proprietà *details* nel log di diagnostica.

La nuova condizione di regressione dei piani fa riferimento a uno stato in cui il motore di database avvia l'esecuzione di un nuovo piano di esecuzione della query che non è altrettanto efficiente del piano precedente. La condizione di regressione del piano precedente si riferisce allo stato quando il motore di database passa dall'uso di un nuovo piano più efficiente al vecchio piano, che non è altrettanto efficiente del nuovo piano. La regressione correlata al carico di lavoro modificato di piani esistenti fa riferimento allo stato in cui il piano precedente e quello nuovo vengono continuamente alternati, con una preferenza verso il piano con prestazioni inferiori.

Per altre informazioni sulle regressioni di piani, vedere [What is plan regression in SQL server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server) (Informazioni sulla regressione di piani in SQL Server).

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il logo di diagnostica include gli hash delle query, l'ID del piano appropriato, l'ID del piano non valido e gli ID delle query. È possibile usare queste informazioni come base per la risoluzione dei problemi.

È possibile analizzare i dati per individuare il piano con prestazioni migliori per le query specifiche che è possibile identificare in base agli hash forniti. Dopo aver determinato il piano più adatto alle query, è possibile applicarlo manualmente.

Per altre informazioni, vedere [How SQL Server prevents plan regressions](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions) (Come SQL Server impedisce le regressioni di piani).

> [!TIP]
> Sapevate che la funzionalità di Intelligence incorporata è in grado di gestire automaticamente i piani di esecuzione delle query con le prestazioni migliori per i database?
>
> Per ottimizzare le prestazioni continue, è consigliabile abilitare l' [ottimizzazione automatica](automatic-tuning-overview.md). Questa funzionalità di Intelligence incorporata monitora costantemente il database e ottimizza automaticamente i piani di esecuzione delle query con prestazioni ottimali per i database.

## <a name="database-scoped-configuration-value-change"></a>Modifica del valore di configurazione con ambito database

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui viene rilevata una modifica nella configurazione in ambito database che causa una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei sette giorni precedenti. Ciò indica che le modifiche recenti apportate alla configurazione in ambito database non sembrano essere utili per le prestazioni del database in uso.

La modifica della configurazione in ambito database può essere impostata per ogni singolo database. Questa configurazione viene usata caso per caso per ottimizzare le singole prestazioni del database. Le opzioni seguenti possono essere configurate per ogni singolo database: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica include le modifiche della configurazione in ambito database apportate di recente che hanno causato la riduzione delle prestazioni rispetto al comportamento del carico di lavoro dei sette giorni precedenti. È possibile ripristinare le modifiche di configurazione reimpostando i valori precedenti. Si possono anche ottimizzare i singoli valori fino a ottenere il livello di prestazioni desiderato. È possibile copiare i valori di configurazione in ambito database da un database simile con prestazioni soddisfacenti. Se non si riesce a risolvere i problemi relativi alle prestazioni, ripristinare i valori predefiniti e provare a eseguire la regolazione a partire da questa baseline.

Per altre informazioni sull'ottimizzazione della configurazione in ambito database e sulla sintassi T-SQL per la modifica della configurazione, vedere [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Client lento

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui il client che utilizza il database non può utilizzare l'output del database con la stessa velocità con cui il database invia i risultati. Poiché il database non archivia i risultati delle query eseguite in un buffer, rallenta e attende che il client utilizzi gli output della query trasmessi prima di procedere. Questa condizione potrebbe anche essere correlata a una rete che non è sufficientemente veloce da trasmettere gli output dal database al client di consumo.

Questa condizione viene generata solo se viene rilevata una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei sette giorni precedenti. Questo problema di prestazioni viene rilevato solo se si verifica una riduzione delle prestazioni statisticamente significativa rispetto al comportamento delle prestazioni precedenti.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Questo modello di prestazioni rilevabili indica una condizione sul lato client. Sono richiesti interventi di risoluzione dei problemi nell'applicazione sul lato client o nella rete sul lato client. Il log di diagnostica include gli hash e i tempi di attesa delle query che sembrano attendere da più tempo che il client ne usi i risultati nelle due ore precedenti. È possibile usare queste informazioni come base per la risoluzione dei problemi.

È possibile ottimizzare le prestazioni dell'applicazione per l'uso di queste query oppure valutare possibili problemi di latenza della rete. Dato che il problema di riduzione delle prestazioni è basato su una modifica nella baseline delle prestazioni negli ultimi sette giorni, è possibile verificare se questo evento di regressione delle prestazioni è causato da modifiche recenti delle condizioni per l'applicazione o la rete.

## <a name="pricing-tier-downgrade"></a>Downgrade del piano tariffario

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui è stato eseguito il downgrade del piano tariffario della sottoscrizione del database. A causa della riduzione delle risorse (DTU) disponibili per il database, il sistema ha rilevato un calo delle prestazioni correnti del database rispetto alla baseline dei sette giorni precedenti.

Potrebbe inoltre essere presente una condizione in cui è stato effettuato il downgrade del piano tariffario della sottoscrizione del database e quindi l'aggiornamento a un livello superiore entro un breve periodo di tempo. Il rilevamento di questa riduzione temporanea del livello delle prestazioni è indicato nella sezione dei dettagli del log di diagnostica come downgrade e aggiornamento del piano tariffario.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se il piano tariffario è stato ridotto e, di conseguenza, il DTU è disponibile e le prestazioni sono soddisfacenti, non è necessario eseguire alcuna operazione. Se il piano tariffario è stato ridotto e non si è soddisfatti delle prestazioni del database, ridurre i carichi di lavoro del database o prendere in considerazione l'aumento del piano tariffario a un livello superiore.

## <a name="recommended-troubleshooting-flow"></a>Flusso di risoluzione dei problemi consigliato

 Per un approccio consigliato alla risoluzione dei problemi di prestazioni con Intelligent Insights, seguire il diagramma di flusso.

Accedere a Intelligent Insights nel portale di Azure passando ad Analisi SQL di Azure. Tentare di individuare l'avviso relativo alle prestazioni in arrivo e selezionarlo. Identificare cosa accade nella pagina dei rilevamenti. Osservare l'analisi della causa radice del problema indicata, il testo e le tendenze nel tempo della query e l'evoluzione degli eventi imprevisti. Tentare di risolvere il problema usando i consigli di Intelligent Insights per ridurre il problema di prestazioni.

[![Diagramma di flusso per la risoluzione dei problemi](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selezionare il diagramma di flusso per scaricarne una versione in formato PDF.

Intelligent Insights in genere ha bisogno di un'ora di tempo per eseguire l'analisi della causa radice del problema di prestazioni. Se non è possibile individuare il problema in Intelligent Insights ed è cruciale trovare una soluzione, usare Query Store per identificare manualmente la causa radice del problema di prestazioni. In genere, questi problemi sono inferiori a un'ora precedente. Per altre informazioni, vedere [monitorare le prestazioni usando il query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [Intelligent Insights](intelligent-insights-overview.md) concetti.
- Utilizzare il [log di diagnostica delle prestazioni Intelligent Insights](intelligent-insights-use-diagnostics-log.md).
- Monitoraggio con [analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).
- Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](../../azure-monitor/platform/platform-logs-overview.md).