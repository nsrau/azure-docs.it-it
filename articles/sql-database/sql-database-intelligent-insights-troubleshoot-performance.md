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
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights

Questa pagina contiene informazioni sui problemi di prestazioni del database SQL di Azure rilevati tramite il log di diagnostica delle prestazioni del database di [Intelligent Insights](sql-database-intelligent-insights.md). Il log diagnostica può essere inviato ad [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Hub eventi di Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Archiviazione di Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) oppure a una soluzione di terze parti per funzionalità di avvisi e report di DevOps personalizzate.

> [!NOTE]
> Per una rapida guida alla risoluzione dei problemi di prestazioni del database SQL di Azure, vedere il diagramma [Flusso di risoluzione dei problemi consigliato](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) in questo documento.
>

## <a name="detectable-database-performance-patterns"></a>Modelli di prestazioni del database rilevabili

Intelligent Insights rileva automaticamente i problemi di prestazioni del database SQL di Azure in base ai tempi di attesa, agli errori oppure ai timeout di esecuzione di una query e genera i modelli di prestazioni rilevati nel log di diagnostica. La tabella seguente riepiloga i modelli di prestazioni rilevabili.

| Modelli di prestazioni rilevabili | Dettagli restituiti |
| :------------------- | ------------------- |
| [Raggiungimento dei limiti delle risorse](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Il consumo di risorse disponibili (DTU), i thread di lavoro del database o le sessioni di accesso al database disponibili nella sottoscrizione monitorata hanno raggiunto i limiti provocando problemi di prestazioni del database SQL di Azure. |
| [Aumento del carico di lavoro](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | È stato rilevato un aumento o un accumulo continuo di carico di lavoro nel database che provoca problemi di prestazioni del database SQL di Azure. |
| [Utilizzo elevato della memoria](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | I thread di lavoro che richiedono concessioni di memoria sono in attesa delle allocazioni di memoria per quantità di tempo statisticamente significative oppure è presente un accumulo nell'aumento di thread di lavoro che richiedono concessioni di memoria che ha impatto sulle prestazioni del database SQL di Azure. |
| [Blocco](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | È stato rilevato un blocco eccessivo del database con impatto sulle prestazioni del database SQL di Azure. |
| [Valore di MAXDOP aumentato](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | L'opzione relativa al massimo grado di parallelismo (MAXDOP) è stata modificata e influisce sull'efficienza di esecuzione delle query.  |
| [Contesa di latch di pagina](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | È stata rilevata una contesa di latch di pagina che influisce sulle prestazioni del database SQL di Azure. Più thread tentano di accedere contemporaneamente alle stesse pagine di buffer di dati in memoria provocando un aumento dei tempi di attesa con impatto sulle prestazioni del database SQL di Azure. |
| [Indice mancante](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | È stato rilevato un problema di indice mancante con impatto sulle prestazioni del database SQL di Azure. |
| [Nuova query](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | È stata rilevata una nuova query con impatto sulle prestazioni complessive del database SQL di Azure.  |
| [Statistiche di attesa non comune](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Sono stati rilevati tempi di attesa del database non comuni con impatto sulle prestazioni del database SQL di Azure. |
| [Contesa di TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Più thread tentano di accedere alle stesse risorse di TempDB provocando un collo di bottiglia con impatto sulle prestazioni del database SQL di Azure. |
| [Carenza di DTU nel pool elastico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | La carenza di eDTU disponibili nel pool elastico influisce sulle prestazioni del database SQL di Azure. |
| [Regressione di piani](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | È stato rilevato un nuovo piano o una modifica nel carico di lavoro di un piano esistente con impatto sulle prestazioni del database SQL di Azure. |
| [Modifica del valore di configurazione in ambito database](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | La modifica di configurazione in ambito database influisce sulle prestazioni del database SQL di Azure. |
| [Client lento](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | È stato rilevato un client applicazione lento che non è in grado di usare in modo abbastanza rapido l'output del database SQL di Azure, con impatto sulle prestazioni del database stesso. |
| [Downgrade del piano tariffario](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | L'azione di downgrade del piano tariffario ha ridotto le risorse disponibili, con impatto sulle prestazioni del database SQL di Azure. |

> [!TIP]
> Per ottimizzare con continuità le prestazioni del database SQL di Azure, è opportuno abilitare l'[ottimizzazione automatica del database SQL di Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning), una funzionalità univoca di intelligenza incorporata di Azure SQL che monitora costantemente il database SQL di Azure e in modo automatico regola gli indici e applica correzioni al piano di esecuzione delle query.
>

La sezione seguente descrive in modo più dettagliato i modelli di prestazioni rilevabili elencati in precedenza.

## <a name="reaching-resource-limits"></a>Raggiungimento dei limiti delle risorse

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili combina i problemi di prestazioni correlati al raggiungimento dei limiti delle risorse disponibili, dei thread di lavoro e delle sessioni. Dopo che il problema è stato rilevato, un campo di descrizione del log di diagnostica indica se il problema di prestazioni è correlato ai limiti delle risorse, dei thread di lavoro o delle sessioni.

Le risorse nel database SQL di Azure vengono spesso denominate [risorse DTU](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu) e sono costituite da una misura combinata di risorse di CPU e I/O (I/O del log delle transazioni e dei dati). Il modello del raggiungimento dei limiti delle risorse viene riconosciuto quando la riduzione delle prestazioni delle query rilevata è provocata dal raggiungimento di uno qualsiasi dei limiti delle risorse misurate.

La risorsa con limiti di sessione indica il numero di accessi simultanei al database SQL di Azure. Questo modello di prestazioni viene riconosciuto nel caso in cui le applicazioni che si connettono ai database di SQL Azure abbiano raggiunto il numero di accessi simultanei disponibili nel database. Nel caso in cui le applicazioni tentino di usare più sessioni rispetto a quelle disponibili in un database, le prestazioni delle query ne sono influenzate.

Il raggiungimento dei limiti dei thread di lavoro rappresenta un caso di raggiungimento dei limiti delle risorse specifico poiché i thread di lavoro disponibili non vengono conteggiati nell'uso delle DTU. Il raggiungimento dei limiti dei thread di lavoro in un database può causare l'aumento dei tempi di attesa specifici di una risorsa e di conseguenza una riduzione delle prestazioni delle query.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera hash di query per le query che hanno avuto impatto sulle prestazioni e sulle percentuali di consumo delle risorse. È possibile usare queste informazioni come punto iniziale per ottimizzare il carico di lavoro del database. In particolare, è possibile valutare l'ottimizzazione delle query che influenzano la riduzione delle prestazioni, aggiungendo indici, oppure ottimizzare le applicazioni con una distribuzione del carico di lavoro più uniforme. Nel caso in cui non sia possibile ridurre i carichi di lavoro né eseguire un'ottimizzazione, valutare l'aumento del piano tariffario della sottoscrizione del database SQL di Azure per incrementare la quantità di risorse disponibili.

Se sono stati raggiunti i limiti delle sessioni disponibili, potrebbe essere opportuno valutare l'ottimizzazione delle applicazioni in termini di riduzione del numero di accessi al database. Se non è possibile ridurre il numero di accessi delle applicazioni al database, è possibile aumentare il piano tariffario del database o anche suddividere e spostare il database in più database per distribuire il carico di lavoro in modo più bilanciato.

Per altre informazioni sulla risoluzione dei problemi per i limiti delle sessioni, vedere [How to deal with the limits of Azure SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/) (Come gestire i limiti al numero massimo di accessi al database SQL di Azure). Per conoscere i limiti delle risorse disponibili per il proprio livello di sottoscrizione, vedere [Limiti delle risorse del database SQL di Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Aumento del carico di lavoro

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni identifica i problemi causati dall'aumento o, in forma peggiore, dall'accumulo del carico di lavoro.

Il rilevamento viene eseguito tramite una combinazione di diverse metriche. La metrica di base misurata è la rilevazione di un aumento del carico di lavoro rispetto alla baseline del carico di lavoro precedente. L'altra forma di rilevamento si basa sulla misurazione di un notevole aumento di thread di lavoro attivi, sufficiente per compromettere le prestazioni delle query.

Nella forma più grave il carico di lavoro si accumula di continuo perché il database SQL di Azure non è in grado di gestirlo e di conseguenza le dimensioni del carico di lavoro aumentano, determinando una condizione di accumulo. A causa di questa condizione, il tempo in cui il carico di lavoro rimane in attesa dell'esecuzione aumenta, provocando uno dei più gravi problemi di prestazioni di database. Questo problema viene rilevato monitorando l'aumento del numero di thread di lavoro interrotti. 

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera il numero di query la cui esecuzione è aumentata e l'hash della query con il maggior contributo all'aumento del carico di lavoro. È possibile usare queste informazioni come punto iniziale per ottimizzare il carico di lavoro e in particolare per la query che contribuisce maggiormente all'aumento del carico di lavoro.

È anche possibile distribuire i carichi di lavoro in modo più uniforme nel database oppure provare a ottimizzare la query che influisce sulle prestazioni e aggiungere indici. Un altro approccio da valutare è la distribuzione del carico di lavoro tra più database. Se questa operazione non è possibile, aumentare il piano tariffario della sottoscrizione del database SQL di Azure per incrementare la quantità di risorse disponibili.

## <a name="memory-pressure"></a>Utilizzo elevato della memoria

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database causata da un utilizzo elevato della memoria o, nella forma più grave, da una condizione di accumulo d richieste alla memoria rispetto alla baseline delle prestazioni dei 7 giorni precedenti.

L'uso elevato della memoria indica una condizione delle prestazioni in cui è presente un numero sufficientemente grande di thread di lavoro che richiedono concessioni di memoria nel database di SQL Azure. Questo problema causa una condizione di uso elevato della memoria in cui il database SQL di Azure non è in grado di allocare in modo efficiente la memoria per tutti i processi di lavoro che lo richiedono. Uno dei motivi più comuni alla base di questo problema è correlato alla quantità di memoria disponibile per il database SQL di Azure da un lato e un aumento del carico di lavoro che causa l'aumento di thread di lavoro e l'uso elevato della memoria.

Una forma più grave di uso elevato della memoria è l'accumulo di richieste alla memoria che indica la presenza di un numero di thread di lavoro che richiedono concessioni di memoria più elevato rispetto a quello delle query che la rilasciano. Il numero di thread di lavoro che richiedono concessioni di memoria potrebbe crescere di continuo, provocando in tal modo l'accumulo di richieste, perché il motore non è un grado di allocare memoria in modo sufficientemente efficiente per soddisfare la domanda. La condizione di accumulo di richieste alla memoria rappresenta uno dei più gravi problemi di prestazioni di database.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera dettagli dell'archivio di oggetti di memoria con il clerk di memoria, ovvero thread di lavoro, contrassegnato come il motivo principale per l'uso elevato della memoria e i timestamp pertinenti. Queste informazioni possono essere usate come base per la risoluzione dei problemi. 

È consigliabile ottimizzare o rimuovere query correlate ai clerk di memoria con l'uso della memoria più elevato. È anche possibile verificare che non siano in esecuzione query sui dati che non si intende usare. Una buona norma consiste nell'usare sempre una clausola WHERE nelle query. È consigliabile anche creare indici non cluster per cercare i dati anziché analizzare i dati.

È consigliabile anche provare a ridurre il carico di lavoro, nonché ottimizzare o distribuire le operazioni tra più database. Un altro approccio da valutare è la distribuzione del carico di lavoro tra più database. Se questa operazione non è possibile, aumentare il piano tariffario della sottoscrizione del database SQL di Azure per incrementare la quantità di risorse di memoria disponibili nel database.

Per altre informazioni sulla risoluzione dei problemi, vedere [Memory Grants Meditation: The mysterious SQL Server memory consumer with Many Names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)(Riflessione sulle concessioni di memoria: il misterioso consumer di memoria con molti nomi).

## <a name="locking"></a>Blocco

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica una riduzione delle prestazioni correnti del database in cui è stato rilevato un blocco eccessivo del database rispetto alla baseline delle prestazioni dei 7 giorni precedenti. 

In sistemi RDBMS moderni il blocco è essenziale per l'implementazione multithreading in cui le prestazioni sono massimizzate tramite l'esecuzione di più thread simultanei e di transazioni di database parallele ove possibile. In altre parole, il blocco in questo contesto si riferisce al meccanismo di accesso predefinito in cui solo un'unica transazione può accedere in modo esclusivo a righe, pagine, tabelle e file necessari e non entra in conflitto con un'altra transazione per accedere alle risorse. Quando la transazione che ha bloccato le risorse per l'uso è completata, il blocco su tali risorse viene rilasciato consentendo ad altre transazioni di accedere alle risorse necessarie. Per altre informazioni sul blocco, vedere [Utilizzo dei blocchi nel motore di database](https://msdn.microsoft.com/library/ms190615.aspx).

Nel caso in cui le transazioni eseguite dal motore SQL restino in attesa per periodi di tempo prolungati prima di accedere alle risorse bloccate per l'uso, il tempo di attesa influisce sul rallentamento delle prestazioni di esecuzione del carico di lavoro. 

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera i dettagli sui blocchi che possono essere usati come base per la risoluzione dei problemi. È possibile analizzare le query che causano il blocco segnalate, ovvero quelle che introducono la riduzione delle prestazioni correlate al blocco, e quindi rimuoverle. In alcuni casi potrebbe essere utile ottimizzare le query che causano il blocco.
Il modo più semplice e sicuro per attenuare il problema consiste nel mantenere brevi le transazioni e nel ridurre l'impatto del blocco causato dalle query più dispendiose. È consigliabile suddividere grandi batch di operazioni in blocchi di operazioni minori o ridurre l'impatto del blocco aumentando il più possibile l'efficienza della query. Provare anche a ridurre le analisi di grandi dimensioni poiché aumentano le possibilità di deadlock e influiscono negativamente sulle prestazioni generali del database. Per le query identificate che causano il blocco, è necessario prendere in considerazione la creazione di nuovi indici o l'aggiunta di colonne all'indice esistente per evitare le analisi della tabella. Per altre informazioni, vedere [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (Come risolvere i problemi di blocco causati dall'escalation blocchi in SQL Server).

## <a name="increased-maxdop"></a>Valore di MAXDOP aumentato

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui un piano di esecuzione di query selezionato è stato eseguito in parallelo in misura maggiore del previsto. Query Optimizer nel database SQL di Azure migliora le prestazioni del carico di lavoro prendendo decisioni sull'esecuzione delle query in parallelo per velocizzare le operazioni ove possibile. In alcuni casi, tuttavia, i thread di lavoro che elaborano in parallelo una query trascorrono più tempo in attesa tra loro per la sincronizzazione e l'unione dei risultati rispetto all'esecuzione della stessa query con meno thread di lavoro paralleli o, in alcuni casi, con un unico thread.

Il sistema esperto analizza le prestazioni correnti del database rispetto al periodo di baseline e determina se una query eseguita in precedenza viene ora eseguita più lentamente a causa di una parallelizzazione del piano di esecuzione delle query maggiore rispetto al necessario.

L'opzione di configurazione server MAXDOP nel database SQL di Azure consente di controllare il numero di core CPU che possono essere usati per eseguire la stessa query in parallelo. 

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera gli hash di query correlati alle query per cui la durata di esecuzione è aumentata a causa di una parallelizzazione maggiore rispetto al necessario. Il log restituisce anche i tempi di attesa CXP. Tale tempo rappresenta il tempo che un singolo thread organizzatore/coordinatore (thread 0) rimane in attesa della terminazione di tutti gli altri thread prima di unire i risultati e procedere. Il log di diagnostica genera anche i tempi di attesa per l'esecuzione complessiva delle query con prestazioni ridotte. Queste informazioni rappresentano la base per la risoluzione del problema.

Provare a valutare la possibilità di ottimizzare o semplificare query complesse, ad esempio suddividendo processi batch di grandi dimensioni in processi di dimensioni minori. Verificare anche di disporre di indici creati per supportare le query. È possibile applicare manualmente l'opzione MAXDOP (massimo grado di parallelismo) per una particolare query contrassegnata come query con prestazioni ridotte. Questa operazione viene configurata tramite l'uso di T-SQL. Per altre informazioni, vedere [Configurare l'opzione di configurazione del server max degree of parallelism](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

L'impostazione dell'opzione di configurazione server MAXDOP su zero (0) come valore predefinito indica che il database SQL di Azure può usare tutti i core CPU logici disponibili per parallelizzare i thread che eseguono una singola query. L'impostazione dell'opzione MAXDOP su uno (1) indica che un solo core può essere usato per l'esecuzione di una query, disattivando in tal modo il parallelismo. A seconda del caso, dei core disponibili nel database e delle informazioni del log di diagnostica, è possibile impostare l'opzione MAXDOP sul numero di core per l'esecuzione di query parallele che ha risolto il problema nel caso specifico.

## <a name="pagelatch-contention"></a>Contesa di latch di pagina

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa della contesa di latch di pagina rispetto alla baseline del carico di lavoro dei 7 giorni precedenti.

I latch sono semplici meccanismi di sincronizzazione usati dal database SQL di Azure per abilitare il multithreading e per garantire la coerenza di strutture in memoria, ad esempio indici, pagine di dati e altre strutture interne.

Nel database SQL di Azure sono disponibili molti tipi di latch. Per semplicità, i latch di buffer vengono usati per proteggere le pagine in memoria nel pool di buffer, mentre i latch di I/O vengono usati per proteggere le pagine non ancora caricate nel pool di buffer. Ogni volta che i dati vengono scritti o letti in una pagina nel pool di buffer, un thread di lavoro deve prima acquisire un latch di buffer per la pagina. Ogni volta che un thread di lavoro tenta di accedere a una pagina che non è già disponibile nel pool di buffer in memoria, viene eseguita una richiesta I/O per caricare le informazioni necessarie dalla risorsa di archiviazione, condizione che indica una forma più grave di riduzione delle prestazioni.

La contesa di latch di pagina si verifica quando più thread tentano di acquisire contemporaneamente latch sulla stessa struttura in memoria con il conseguente aumento del tempo di attesa per l'esecuzione delle query. In caso di contesa I/O di latch di pagina quando i dati devono essere accessibili dalla risorsa di archiviazione, il tempo di attesa è anche maggiore e può influire notevolmente sulle prestazioni del carico di lavoro. La contesa di latch di pagina è lo scenario più comune di thread in attesa tra loro e in conflitto per l'accesso alle risorse su più CPU.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera i dettagli sulla contesa di latch di pagina che possono essere usati come base per la risoluzione del problema.

Poiché il latch di pagina è un meccanismo di controllo interno del database SQL di Azure, determina automaticamente quando usare tali dettagli. Le decisioni correlate alle applicazioni, ad esempio la progettazione dello schema, possono influenzare il funzionamento del latch di pagina a causa del comportamento deterministico dei latch.
Un metodo per gestire una contesa di latch consiste nel sostituire una chiave di indice sequenziale con una chiave non sequenziale per distribuire uniformemente inserimenti in un intervallo di indici. Questa operazione viene eseguita in genere tramite una colonna iniziale dell'indice che distribuisce il carico di lavoro in modo proporzionale. Un altro metodo da considerare consiste nel partizionamento delle tabelle. La creazione di uno schema di partizionamento hash con una colonna calcolata in una tabella partizionata è un approccio comune per attenuare la contesa di latch eccessiva. Nel caso di contesa I/O di latch di pagina l'introduzione degli indici è consigliata per ridurre questo problema di prestazioni. Per altre informazioni, vedere il documento in formato PDF [Diagnosing and Resolving Latch Contention on SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (Diagnosi e risoluzione delle contese di latch in SQL Server).

## <a name="missing-index"></a>Indice mancante

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni indica la riduzione delle prestazioni correnti correlate al carico di lavoro del database a causa di un indice mancante rispetto alla baseline dei 7 giorni precedenti.

Per velocizzare le prestazioni delle query, viene usato un indice che consente di accedere rapidamente ai dati della tabella grazie alla riduzione del numero di pagine dei set di dati che devono essere visitate o analizzate.

Le query specifiche che hanno causato una riduzione delle prestazioni vengono identificate tramite il rilevamento per cui la creazione degli indici influirebbe favorevolmente sulle prestazioni.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera hash di query per le query che hanno avuto impatto sulle prestazioni del carico di lavoro. Provare a creare indici per queste query oppure ottimizzarle o rimuoverle qualora non siano necessarie. È consigliabile anche evitare di eseguire query sui dati che non vengono usati.

> [!TIP]
> Le funzionalità di intelligenza incorporate di Azure possono gestire automaticamente gli indici con le prestazioni migliori per i database in uso.
>
> Per ottimizzare con continuità le prestazioni del database SQL di Azure, è opportuno abilitare l'[ottimizzazione automatica del database SQL di Azure](sql-database-automatic-tuning.md), una funzionalità univoca di intelligenza incorporata di Azure SQL che monitora costantemente il database SQL di Azure e in modo automatico regola e crea gli indici per i database in uso.
>

## <a name="new-query"></a>Nuova query

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica che è stata rilevata una nuova query con prestazioni insufficienti e con impatto sulle prestazioni del carico di lavoro rispetto alla baseline delle prestazioni dei 7 giorni precedenti.

La scrittura di una query con ottime prestazioni può essere un'attività complessa. Per altre informazioni sulla scrittura delle query, vedere [Writing SQL Queries](https://msdn.microsoft.com/library/bb264565.aspx) (Scrittura di query SQL). Per ottimizzare query esistenti, vedere [Ottimizzazione delle query](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera informazioni sulle due (2) nuove query che usano maggiormente la CPU, includendo gli hash corrispondenti. Poiché la query rilevata influisce sulle prestazioni del carico di lavoro, può essere opportuno ottimizzarla. È consigliabile non recuperare i dati che non vengono usati, usare query con la clausola WHERE, semplificare query complesse e suddividerle in query di dimensioni inferiori. Valutare anche la suddivisione di query in batch di grandi dimensioni in query in batch di dimensioni minori. L'introduzione di indici per le nuove query è in genere consigliabile per ridurre questo problema di prestazioni.

È anche consigliabile usare [Informazioni dettagliate prestazioni query del database SQL di Azure](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Statistiche di attesa non comune

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una riduzione delle prestazioni del carico di lavoro in cui vengono identificate le query con prestazioni ridotte rispetto alla baseline del carico di lavoro dei 7 giorni precedenti.

In questo caso il sistema non è stato in grado di classificare le query con prestazioni ridotte in altre categorie di prestazioni rilevabili standard, ma ha rilevato una statistica di attesa responsabile della regressione e di conseguenza le considera come query con &#8220;*statistiche di attesa non comune*&#8221; in cui viene esposta anche la statistica di attesa responsabile della regressione. 

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera informazioni sui dettagli del tempo di attesa non comune, sugli hash di query delle query interessate e sui tempi di attesa.

Poiché in questo caso il sistema non è stato in grado di identificare la causa radice delle query con prestazioni ridotte, le informazioni di diagnostica su tali query possono essere usate come punto iniziale per una risoluzione dei problemi manuale. Provare a ottimizzare le prestazioni di queste query, ad esempio evitando di recuperare i dati che non vengono usati e semplificando le query più complesse in query di dimensioni minori. Per altre informazioni sull'ottimizzazione delle prestazioni delle query, vedere [Ottimizzazione delle query](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contesa di TempDB

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione delle prestazioni del database in cui è presente un collo di bottiglia di thread che tentano di accedere a risorse di TempDB (questa condizione non è correlata all'I/O). Lo scenario tipico per questo problema di prestazioni è la presenza di centinaia di query simultanee che creano, usano e rilasciano tabelle TempDB di piccole dimensioni, ovvero tabelle TempDB. Il sistema ha rilevato che il numero di query simultanee che usano le stesse tabelle TempDB è aumentato in modo statisticamente significativo per influire sulle prestazioni del database rispetto alla baseline delle prestazioni degli ultimi 7 giorni.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera dettagli sulle contese di TempDB che possono essere usati come punto iniziale nella risoluzione dei problemi di prestazioni. Per risolvere questo tipo di contesa e aumentare la velocità effettiva del carico di lavoro globale, è possibile interrompere l'uso delle tabelle temporanee oppure usare tabelle ottimizzate per la memoria. Per altre informazioni, vedere [Introduzione alle tabelle ottimizzate per la memoria](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Carenza di DTU nel pool elastico

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una riduzione delle prestazioni correnti relative al carico di lavoro del database rispetto alla baseline degli ultimi 7 giorni a causa della carenza delle DTU disponibili nel pool elastico della sottoscrizione. 

Le risorse nel database SQL di Azure vengono spesso denominate [risorse DTU](sql-database-what-is-a-dtu.md) e sono costituite da una misura combinata di risorse di CPU e I/O (I/O del log delle transazioni e dei dati). Le [risorse del pool elastico di Azure](sql-database-elastic-pool.md) vengono usate come un pool di risorse eDTU disponibili condivise tra più database a scopo di ridimensionamento. Nel caso in cui le risorse eDTU disponibili nel pool elastico non siano in numero sufficiente per supportare tutti i database nel pool, il sistema rileva un problema di prestazioni per carenza di eDTU.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera informazioni sul pool elastico, elenca i database che usano più DTU e restituisce una percentuale di DTU del pool usate dal database più dispendioso.

Poiché questa condizione delle prestazioni è correlata a più database che usano lo stesso pool di eDTU del pool elastico, la procedura di risoluzione dei problemi deve essere focalizzata sui database che usano più DTU. Provare a ridurre il carico di lavoro nei database più dispendiosi, ad esempio ottimizzando le query più dispendiose su tali database. Provare anche a verificare che non vengano eseguite query su dati non usati. Un altro approccio consiste nell'ottimizzazione delle applicazioni coinvolte nei database che usano più DTU e nella ridistribuzione del carico di lavoro tra più database.

Se la riduzione e l'ottimizzazione del carico di lavoro corrente nei database che usano più DTU non sono possibili, provare ad aumentare il piano tariffario del pool elastico. Tale aumento determina l'incremento delle DTU disponibili nel pool elastico.

## <a name="plan-regression"></a>Regressione di piani

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in base alla quale il database SQL di Azure inizia a usare un piano di esecuzione delle query non ottimale. Tale piano causa in genere un aumento del tempo di esecuzione di query che provoca a sua volta tempi di attesa superiori per la query corrente e le altre.

Il database SQL di Azure determina il piano di esecuzione delle query con il minor costo di esecuzione. Il tipo delle query e dei carichi di lavoro, tuttavia, può variare e di conseguenza i piani esistenti talvolta non sono più efficienti o probabilmente il database SQL di Azure non esegue una valutazione corretta. In termini di correzione i piani di esecuzione delle query possono essere forzati manualmente.

Questo modello di prestazioni rilevabili combina tre casi diversi di regressione di piani, ovvero regressione di nuovi piani, regressione di piani precedenti e carico di lavoro modificato di piani esistenti. Il tipo di regressione di piani che si è verificato è indicato dalla proprietà &#8220;*details*&#8221; nel log di diagnostica.

La condizione di regressione di nuovi piani fa riferimento a uno stato in cui il database SQL di Azure inizia l'esecuzione di un nuovo piano non efficiente come quello precedente. La condizione di regressione di piani precedenti fa riferimento allo stato in cui il database SQL di Azure passa dall'uso di un nuovo piano più efficiente all'uso del piano precedente non efficiente come quello nuovo. La regressione correlata al carico di lavoro modificato di piani esistenti fa riferimento allo stato in cui due piani, quello precedente e quello nuovo, vengono continuamente alternati, con una preferenza verso il piano con prestazioni ridotte.

Per altre informazioni sulle regressioni di piani, vedere [What is plan regression in SQL server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/) (Informazioni sulla regressione di piani in SQL Server). 

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera gli hash delle query, l'ID del piano appropriato, l'ID del piano non appropriato e gli ID delle query che possono essere usati come una base per la risoluzione dei problemi di prestazioni di questa condizione.

Provare ad analizzare il piano con prestazioni migliori per le query specifiche che è possibile identificare con gli hash di query forniti. Dopo aver determinato il piano più adatto alle query, è possibile applicarlo manualmente. Per altre informazioni, vedere [How SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/) (Come SQL Server impedisce le regressioni di piani).

> [!TIP]
> Le funzionalità di intelligenza incorporate di Azure possono gestire automaticamente i piani di esecuzione delle query con le prestazioni migliori per i database in uso.
>
> Per ottimizzare con continuità le prestazioni del database SQL di Azure, è opportuno abilitare l'[ottimizzazione automatica del database SQL di Azure](sql-database-automatic-tuning.md), una funzionalità univoca di intelligenza incorporata di Azure SQL che monitora costantemente il database SQL di Azure e in modo automatico regola e crea i piani di esecuzione delle query con le prestazioni migliori per i database in uso.
>

## <a name="database-scoped-configuration-value-change"></a>Modifica del valore di configurazione in ambito database

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui è stata rilevata una modifica nella configurazione in ambito database che ha causato una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei 7 giorni precedenti. Ciò indica che le modifiche recenti apportate alla configurazione in ambito database non sembrano essere utili per le prestazioni del database in uso.

La modifica della configurazione in ambito database può essere impostata per ogni singolo database. Questa configurazione viene usata caso per caso per ottimizzare le singole prestazioni del database. Le opzioni seguenti possono essere configurate per ogni singolo database: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il log di diagnostica genera le modifiche della configurazione in ambito database apportate di recente che hanno causato la riduzione delle prestazioni rispetto al comportamento del carico di lavoro dei 7 giorni precedenti. È consigliabile reimpostare le modifiche di configurazione sui valori precedenti oppure eseguire un'ottimizzazione valore per valore fino a quando viene raggiunto il livello di prestazioni desiderato. È anche possibile provare a copiare i valori di configurazione in ambito database da un database simile con prestazioni soddisfacenti. Se non è stato possibile risolvere i problemi di prestazioni, è necessario ripristinare i valori predefiniti del database SQL di Azure e tentare di eseguire un'ottimizzazione da questa baseline.

Per altre informazioni sull'ottimizzazione della configurazione in ambito database e sulla sintassi T-SQL per la modifica della configurazione, vedere [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Client lento

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui il client che usa il database SQL di Azure non è in grado di usare l'output del database alla velocità con cui quest'ultimo invia i risultati. Poiché il database SQL di Azure non archivia i risultati delle query eseguite in un buffer, subisce un rallentamento e rimane in attesa che il client usi i risultati della query trasmessi prima di procedere. Questa condizione potrebbe anche essere correlata a una rete lenta che non è in grado di trasmettere in modo abbastanza rapido gli output dal database SQL di Azure per il client che li usa.

Questa condizione viene generata solo se viene rilevata una regressione delle prestazioni rispetto al comportamento del carico di lavoro del database dei 7 giorni precedenti. In questo modo si garantisce che il problema di prestazioni venga rilevato solo se si è verificata una riduzione delle prestazioni statisticamente significativa rispetto al comportamento delle prestazioni precedenti.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Questo modello di prestazioni rilevabili indica una condizione sul lato client che richiede una risoluzione dei problemi a livello di applicazione o di rete lato client. Il log di diagnostica genera gli hash e i tempi di attesa delle query che sembrano attendere da più tempo che il client ne usi i risultati nelle due ore precedenti. Questi dati possono essere usati come una base per la risoluzione dei problemi.

Provare a ottimizzare le prestazioni dell'applicazione per l'uso di queste query oppure a considerare possibili problemi di latenza della rete. Poiché il problema della riduzione delle prestazioni era basato su una modifica della baseline delle prestazioni degli ultimi 7 giorni, è consigliabile esaminare se di recente sono state apportate modifiche all'applicazione o alla condizione di rete che potrebbero aver causato l'evento di regressione delle prestazioni. 

## <a name="pricing-tier-downgrade"></a>Downgrade del piano tariffario

### <a name="what-is-happening"></a>Situazione

Questo modello di prestazioni rilevabili indica una condizione in cui è stato eseguito il downgrade del piano tariffario del database SQL di Azure. A causa della riduzione delle risorse (Dtu) disponibili per il database, il sistema ha rilevato un calo delle prestazioni correnti del database rispetto alla baseline dei 7 giorni precedenti.

Potrebbe essersi verificata anche una condizione in cui è stato eseguito il downgrade del piano tariffario della sottoscrizione del database SQL di Azure, aggiornato in seguito a un livello superiore solo all'interno di un breve periodo di tempo. Ciò indica un rilevamento temporaneo della riduzione delle prestazioni che viene restituito nella sezione dei dettagli del log di diagnostica come downgrade e aggiornamento del piano tariffario.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se il piano tariffario è stato ridotto e di conseguenza sono state ridotte le DTU disponibili per il database SQL di Azure e le prestazioni sono soddisfacenti, non è necessario eseguire alcuna azione. Se in seguito alla riduzione del piano tariffario le prestazioni del database SQL di Azure non sono soddisfacenti, è consigliabile provare a ridurre i carichi di lavoro del database o aumentare il piano tariffario a un livello superiore.

## <a name="recommended-troubleshooting-flow"></a>Flusso di risoluzione dei problemi consigliato

Per un approccio consigliato alla risoluzione dei problemi di prestazioni con Intelligent Insights, seguire il diagramma di flusso indicato.

Accedere a Intelligent Insights nel portale di Azure spostandosi ad Analisi SQL di Azure. Tentare di individuare l'avviso relativo alle prestazioni in arrivo e fare clic su di esso. Identificare cosa accade nella pagina dei rilevamenti. Osservare l'analisi della causa radice del problema indicata, il testo e le tendenze nel tempo della query e l'evoluzione degli eventi imprevisti. Usare i consigli di Intelligent Insights per ridurre il problema di prestazioni e tentare di risolverlo. 

[![Diagramma di flusso per la risoluzione dei problemi](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Fare clic sul diagramma di flusso per scaricarne una versione in formato PDF.

Intelligent Insights in genere ha bisogno di un'ora di tempo per eseguire l'analisi della causa radice del problema di prestazioni. Nel caso i cui non sia possibile individuare il problema in Intelligent Insights (nella maggior parte dei casi si tratta di problemi che si sono verificati in un intervallo di tempo precedente minore di un'ora) e se il problema è critico, usare Query Data Store (QDS) per identificare manualmente la causa radice del problema di prestazioni. Per altre informazioni, vedere [Monitoraggio delle prestazioni con Query Store](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui concetti di [Intelligent Insights](sql-database-intelligent-insights.md)
- Usare il [log di diagnostica delle prestazioni del database SQL di Azure generato da Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- Monitorare un [database SQL di Azure usando Analisi SQL di Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
