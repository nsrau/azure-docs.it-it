---
title: Monitorare le prestazioni del database con Intelligent Insights
description: Intelligent Insights nel database SQL di Azure e in Azure SQL Istanza gestita usa l'Intelligence incorporata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare gli eventi che provocano una riduzione delle prestazioni.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 6133d838fa31919a37ddd633193e5559c50de9b6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790441"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights uso di AI per monitorare e risolvere i problemi relativi alle prestazioni del database (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights nel database SQL di Azure e in Azure SQL Istanza gestita ti permette di sapere cosa accade con le prestazioni del database.

Intelligent Insights usa le funzionalità di intelligence integrata per monitorare in modo continuativo l'utilizzo del database grazie all'intelligenza artificiale e rilevare gli eventi che possono causare interruzioni del funzionamento e una riduzione delle prestazioni. Una volta rilevata, viene eseguita un'analisi dettagliata che genera un log delle risorse Intelligent Insights (denominato sqlinsights) con una valutazione intelligente del problema. Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni.

## <a name="what-can-intelligent-insights-do-for-you"></a>Vantaggi offerti da Intelligent Insights

Intelligent Insights è una funzionalità unica dell'intelligence integrata di Azure che offre i vantaggi seguenti:

- Monitoraggio proattivo
- Analisi delle prestazioni personalizzate
- Rilevamento tempestivo della riduzione delle prestazioni del database
- Analisi della causa radice dei problemi rilevati
- Raccomandazioni per il miglioramento delle prestazioni
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="how-does-intelligent-insights-work"></a>Funzionamento di Intelligent Insights

Intelligent Insights analizza le prestazioni del database confrontando il carico di lavoro del database dall'ultima ora con la baseline del carico di lavoro degli ultimi sette giorni. Il carico di lavoro del database è costituito dalle query identificate come maggiormente significative per le prestazioni del database, ad esempio quelle più ripetute e di dimensioni maggiori. Poiché ogni database è univoco in base alla relativa struttura, dati, utilizzo e applicazione, ogni linea di base del carico di lavoro generata è specifica e univoca per tale carico di lavoro. Intelligent Insights, indipendentemente dalla baseline del carico di lavoro, consente inoltre di monitorare le soglie operative assolute e di rilevare problemi sulla base di tempi di attesa eccessivi ed eccezioni critiche e problemi con parametrizzazioni delle query che potrebbero influire sulle prestazioni.

Dopo aver rilevato un problema di riduzione delle prestazioni in base a più metriche osservate con l'ausilio dell'intelligenza artificiale, viene eseguita l'analisi. Viene generato un log di diagnostica con un'analisi intelligente della situazione del database. Con Intelligent Insights è semplice tenere traccia del problema di prestazioni del database dalla sua comparsa fino alla risoluzione. Ogni problema individuato viene monitorato per tutto il suo ciclo di vita, dal rilevamento del problema iniziale alla verifica del miglioramento delle prestazioni, fino al suo completamento.

![Flusso delle analisi delle prestazioni del database](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Le metriche usate per misurare e rilevare i problemi di prestazioni del database si basano su durata delle query, richieste di timeout, tempi di attesa eccessivi e richieste con errori. Per altre informazioni sulle metriche, vedere [metriche di rilevamento](#detection-metrics).

Le riduzioni delle prestazioni del database identificate vengono registrate nel registro sqlinsights con voci intelligenti costituite dalle proprietà seguenti:

| Proprietà | Dettagli |
| :------------------- | ------------------- |
| Informazioni sul database | Metadati relativi a un database per cui sono state rilevate informazioni, ad esempio URI di risorsa. |
| Intervallo di tempo osservato | Ora di inizio e di fine per il periodo delle informazioni rilevate. |
| Metriche interessate | Metriche che hanno causato la generazione di un'informazione: <ul><li>Aumento della durata di una query [secondi].</li><li>Attesa eccessiva [secondi].</li><li>Richieste che hanno raggiunto il timeout [percentuale].</li><li>Richieste che hanno generato errori [percentuale].</li></ul>|
| Valore di impatto | Valore di una metrica misurata. |
| Query interessate e codici di errore | Codice hash o codice di errore di una query. Possono essere usati per creare facilmente correlazioni alle query interessate. Vengono fornite metriche costituite da aumento della durata di una query, tempo di attesa, numero di timeout o codici di errore. |
| Rilevamenti | Rilevamento identificato nel database nel momento in cui si è verificato un evento. Sono disponibili 15 modelli di rilevamento. Per altre informazioni, vedere [Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](intelligent-insights-troubleshoot-performance.md). |
| Analisi della causa radice | Analisi della causa radice del problema identificato in un formato umanamente leggibile. Alcune analisi potrebbero contenere raccomandazioni per il miglioramento delle prestazioni, se possibile. |
|||

Per una panoramica pratica sull'uso di Intelligent Insights con Analisi SQL di Azure e per gli scenari di utilizzo tipici, vedere questo video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights risplende nell'individuazione e nella risoluzione dei problemi relativi alle prestazioni del database. Per usare Intelligent Insights per risolvere i problemi relativi alle prestazioni del database, vedere [risolvere i problemi relativi alle prestazioni con Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opzioni di Intelligent Insights

Intelligent Insights opzioni disponibili sono:

| Opzione Intelligent Insights | Supporto del database SQL di Azure | Supporto per Istanza gestita di database SQL di Azure |
| :----------------------------- | ----- | ----- |
| **Configurare Intelligent Insights** : configurare l'analisi Intelligent Insights per i database. | Sì | Sì |
| Eseguire il **flusso di informazioni dettagliate per analisi SQL di Azure** --trasmettere informazioni approfondite al analisi SQL di Azure. | Sì | Sì |
| **Trasmettere informazioni dettagliate a hub eventi di Azure** : informazioni sul flusso a hub eventi per ulteriori integrazioni personalizzate. | Sì | Sì |
| **Trasmettere informazioni approfondite ad archiviazione di Azure** : informazioni dettagliate sul flusso di archiviazione di Azure per un'ulteriore analisi e l'archiviazione a lungo termine. | Sì | Sì |

> [!NOTE]
> Intelligent Insights è una funzionalità di anteprima, non disponibile nelle aree geografiche seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 1 e Stati Uniti orientali 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Configurare l'esportazione del registro Intelligent Insights

L'output del Intelligent Insights può essere trasmesso a una delle diverse destinazioni per l'analisi:

- L'output trasmesso a un'area di lavoro di Log Analytics può essere usato con [analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md) per visualizzare informazioni dettagliate attraverso l'interfaccia utente del portale di Azure. Questa è la soluzione integrata di Azure e il modo più comune per visualizzare informazioni dettagliate.
- L'output trasmesso a hub eventi di Azure può essere usato per lo sviluppo di scenari di monitoraggio e avviso personalizzati
- L'output trasmesso in archiviazione di Azure può essere usato per lo sviluppo di applicazioni personalizzate, ad esempio per la creazione di report personalizzati, l'archiviazione di dati a lungo termine e così via.

L'integrazione di Analisi SQL di Azure, Hub eventi di Azure, archiviazione di Azure o prodotti di terze parti per l'utilizzo viene eseguita prima di abilitare la registrazione Intelligent Insights (il log "sqlinsights") nel pannello impostazioni di diagnostica di un database e quindi di configurare Intelligent Insights i dati di log da trasmettere in una di queste destinazioni.

Per altre informazioni su come abilitare la registrazione Intelligent Insights e configurare la metrica e i dati del log delle risorse da trasmettere a un prodotto di consumo, vedere [metriche e registrazione diagnostica](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configurazione con Analisi SQL di Azure

Analisi SQL di Azure soluzione fornisce un'interfaccia utente grafica, funzionalità per la creazione di report e avvisi per le prestazioni del database, usando i dati del log delle risorse Intelligent Insights.

Aggiungere Analisi SQL di Azure al dashboard portale di Azure dal Marketplace e creare un'area di lavoro, vedere [configurare analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md#configuration)

Per usare Intelligent Insights con Analisi SQL di Azure, configurare Intelligent Insights i dati di log da trasmettere a Analisi SQL di Azure area di lavoro creata nel passaggio precedente, vedere [metriche e registrazione diagnostica](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Di seguito è riportato un esempio del report di Intelligent Insights attraverso Analisi SQL di Azure:

![Report di Intelligent Insights](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurare con Hub eventi

Per usare Intelligent Insights con hub eventi, configurare Intelligent Insights i dati di log da trasmettere a hub eventi, vedere [metriche e registrazione diagnostica](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) e [trasmettere log di diagnostica di Azure a hub eventi](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

Per usare hub eventi per configurare il monitoraggio e gli avvisi personalizzati, vedere [cosa fare con le metriche e i log di diagnostica in hub eventi](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configurare con Archiviazione di Azure

Per usare Intelligent Insights con lo spazio di archiviazione, configurare Intelligent Insights i dati di log da trasmettere allo spazio di archiviazione, vedere la pagina relativa alla registrazione e al flusso di [metriche e diagnostica](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) [in archiviazione di Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrazioni personalizzate del log di Intelligent Insights

Per usare Intelligent Insights con strumenti di terze parti o per lo sviluppo di avvisi e monitoraggio personalizzati, vedere [usare il log di diagnostica delle prestazioni di Intelligent Insights database](intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Metriche di rilevamento

Le metriche usate per i modelli di rilevamento per la generazione di analisi intelligenti si basano sul monitoraggio degli elementi seguenti:

- Durata delle query
- Richieste di timeout
- Tempo di attesa eccessivo
- Richieste che hanno generato errori

La durata delle query e le richieste di timeout vengono usate come modelli principali per il rilevamento dei problemi di prestazioni del carico di lavoro del database, dal momento che misurano direttamente quello che accade con il carico di lavoro. Per rilevare tutti i casi possibili di riduzione delle prestazioni del carico di lavoro, vengono usati i tempi di attesa eccessivi e le richieste che hanno generato errori come ulteriori modelli per indicare problemi riguardanti le prestazioni del carico di lavoro.

Il sistema prende automaticamente in considerazione le modifiche del carico di lavoro e le modifiche del numero di richieste di query effettuate al database per determinare in modo dinamico le soglie normali e straordinarie per le prestazioni del database.

Tutte le metriche vengono prese in considerazione complessivamente in diverse relazioni tramite un modello di dati derivato scientificamente che categorizza ogni problema di prestazioni rilevato. Le informazioni fornite con un'analisi intelligente includono:

- Dettagli dei problemi di prestazioni rilevati.
- Analisi della causa radice dei problemi rilevati.
- Suggerimenti su come migliorare le prestazioni del database monitorato, ove possibile.

## <a name="query-duration"></a>Durata delle query

Il modello di riduzione delle prestazioni basato sulla durata delle query analizza le singole query e rileva l'aumento del tempo necessario per compilare ed eseguire una query rispetto alla baseline delle prestazioni.

Se l'Intelligence incorporata rileva un aumento significativo della compilazione di query o del tempo di esecuzione delle query che influiscono sulle prestazioni del carico di lavoro, queste query vengono contrassegnate come problemi di riduzione delle prestazioni della durata delle query.

Il log di diagnostica di Intelligent Insights restituisce il codice hash della query per cui è stata rilevata una riduzione delle prestazioni. L'hash della query indica se la riduzione delle prestazioni è correlata a un aumento del tempo di compilazione o di esecuzione della query e l'aumento della durata della query.

## <a name="timeout-requests"></a>Richieste di timeout

Il modello di riduzione delle prestazioni basato sulle richieste di timeout analizza le singole query e rileva l'aumento dei timeout a livello di esecuzione della query e i timeout delle richieste complessivi a livello di database rispetto al periodo della baseline delle prestazioni.

Alcune delle query potrebbero raggiungere il timeout anche prima di raggiungere la fase di esecuzione. Attraverso il mezzo di processi di lavoro interrotti rispetto alle richieste effettuate, misure di intelligence predefinite e analizza tutte le query che hanno raggiunto il database indipendentemente dalla fase di esecuzione.

Dopo che il numero di timeout per le query eseguite o il numero di processi di lavoro interrotti è aumentato fino a superare la soglia gestita dal sistema, un log di diagnostica viene popolato con analisi intelligenti.

Le informazioni generate contengono il numero di richieste che hanno raggiunto il timeout e il numero di query che hanno raggiunto il timeout. Viene fornita l'indicazione se la riduzione delle prestazioni è correlata all'aumento del timeout in fase di esecuzione o a livello complessivo di database. Quando l'aumento dei timeout viene considerato significativo per le prestazioni del database, queste query sono contrassegnate con l'indicazione di un problema di riduzione delle prestazioni basato sul timeout.

## <a name="excessive-wait-times"></a>Tempi di attesa eccessivi

Il modello basato sui tempi di attesa eccessivi monitora le singole query di database e rileva le statistiche di attesa delle query insolitamente elevate che superano le soglie assolute gestite dal sistema. Si osservano le metriche di tempo di attesa eccessive della query seguenti usando, [query Store le statistiche di attesa (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Raggiungimento dei limiti delle risorse
- Raggiungimento dei limiti delle risorse del pool elastico
- Numero eccessivo di thread di sessioni o di processi di lavoro
- Blocco eccessivo del database
- Utilizzo elevato della memoria
- Altre statistiche di attesa

Il raggiungimento dei limiti delle risorse o dei limiti delle risorse del pool elastico indica che il consumo di risorse disponibili in una sottoscrizione o nel pool elastico ha superato le soglie assolute, indicando una riduzione delle prestazioni del carico di lavoro. Un numero eccessivo di thread di sessioni o di processi di lavoro indica una condizione in cui il numero di thread di processi di lavoro o di sessioni avviate ha superato le soglie assolute, indicando una riduzione delle prestazioni del carico di lavoro.

Un blocco eccessivo del database indica una condizione in cui il numero di blocchi in un database ha superato una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro. Un uso elevato della memoria è una condizione in cui il numero di thread che richiedono concessioni di memoria ha superato una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro.

Il rilevamento di altre statistiche di attesa indica una condizione in cui le diverse metriche misurate tramite le statistiche di attesa di Query Store superano una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro.

Dopo che sono stati rilevati tempi di attesa eccessivi, il log di diagnostica di Intelligent Insights restituisce, a seconda dei dati disponibili, i codici hash delle query responsabili e interessate dalla riduzione delle prestazioni, i dettagli delle metriche che comportano l'attesa delle query in esecuzione e i tempi di attesa misurati.

## <a name="errored-requests"></a>Richieste con errori

Il modello di riduzione delle prestazioni basato sulle richieste con errori monitora le singole query e rileva un aumento del numero di query che hanno generato errori rispetto al periodo della baseline. Questo modello monitora anche le eccezioni critiche che hanno superato le soglie assolute gestite dall'Intelligence incorporata. Il sistema prende automaticamente in considerazione il numero di richieste di query effettuate nel database e tutte le modifiche del carico di lavoro nel periodo monitorato.

Quando l'aumento misurato nelle richieste con errori in relazione al numero complessivo delle richieste effettuate viene considerato significativo per le prestazioni del carico di lavoro, le query interessate sono contrassegnate con l'indicazione di un problema di riduzione delle prestazioni basato sulle richieste con errori.

Il log di Intelligent Insights restituisce il numero di richieste con errori. Indica se la riduzione delle prestazioni è correlata all'aumento delle richieste con errori o al raggiungimento di una delle soglie di eccezioni critiche monitorate e il tempo misurato della riduzione delle prestazioni.

Nel caso in cui una delle eccezioni critiche monitorate superi le soglie assolute gestite dal sistema, viene generata un'analisi intelligente con informazioni dettagliate sull'eccezione critica.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare i database usando analisi SQL](../../azure-monitor/insights/azure-sql.md).
- Informazioni su come [risolvere i problemi relativi alle prestazioni con Intelligent Insights](intelligent-insights-troubleshoot-performance.md).