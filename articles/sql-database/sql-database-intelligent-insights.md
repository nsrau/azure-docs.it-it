---
title: Monitorare le prestazioni del database con Intelligent Insights - Database SQL di Azure | Microsoft Docs
description: Intelligent Insights per il database SQL di Azure usa le funzionalità di intelligence integrata per monitorare in modo continuativo l'utilizzo del database grazie all'intelligenza artificiale e di rilevare gli eventi che possono causare interruzioni del funzionamento e una riduzione delle prestazioni.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 573e0dd1bae584b12cfd3859fe63d7ca20bb7b52
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278135"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Intelligent Insights con intelligenza artificiale per eseguire il monitoraggio e risolvere i problemi delle prestazioni del database

Intelligent Insights per il database SQL di Azure assicura un maggior controllo sulle prestazioni dei database SQL di Azure e dei database di Istanza gestita.

Intelligent Insights usa le funzionalità di intelligence integrata per monitorare in modo continuativo l'utilizzo del database grazie all'intelligenza artificiale e rilevare gli eventi che possono causare interruzioni del funzionamento e una riduzione delle prestazioni. Dopo aver rilevato questo tipo di eventi, viene eseguita un'analisi dettagliata che genera un log di diagnostica con una valutazione intelligente del problema. Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni.

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

Intelligent Insights analizza le prestazioni del database confrontando il carico di lavoro del database dall'ultima ora con la baseline del carico di lavoro degli ultimi sette giorni. Il carico di lavoro del database è costituito dalle query identificate come maggiormente significative per le prestazioni del database, ad esempio quelle più ripetute e di dimensioni maggiori. Ogni database è unico per struttura, dati, uso e applicazione, motivo per cui ogni baseline del carico di lavoro generata è specifica e unica per una singola istanza. Intelligent Insights, indipendentemente dalla baseline del carico di lavoro, consente inoltre di monitorare le soglie operative assolute e di rilevare problemi sulla base di tempi di attesa eccessivi ed eccezioni critiche e problemi con parametrizzazioni delle query che potrebbero influire sulle prestazioni.

Dopo aver rilevato un problema di riduzione delle prestazioni in base a più metriche osservate con l'ausilio dell'intelligenza artificiale, viene eseguita l'analisi. Viene generato un log di diagnostica con un'analisi intelligente della situazione del database. Con Intelligent Insights è semplice tenere traccia del problema di prestazioni del database dalla sua comparsa fino alla risoluzione. Ogni problema individuato viene monitorato per tutto il suo ciclo di vita, dal rilevamento del problema iniziale alla verifica del miglioramento delle prestazioni, fino al suo completamento.

![Flusso delle analisi delle prestazioni del database](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Le metriche usate per misurare e rilevare i problemi di prestazioni del database si basano su durata delle query, richieste di timeout, tempi di attesa eccessivi e richieste con errori. Per altre informazioni, vedere la sezione [Metriche di rilevamento](sql-database-intelligent-insights.md#detection-metrics) in questo documento.

Le riduzioni delle prestazioni del database SQL identificate sono registrate nel log di diagnostica con voci intelligenti costituite dalle proprietà seguenti:

| Proprietà             | Dettagli              |
| :------------------- | ------------------- |
| Informazioni sul database | Metadati relativi a un database per cui sono state rilevate informazioni, ad esempio URI di risorsa. |
| Intervallo di tempo osservato | Ora di inizio e di fine per il periodo delle informazioni rilevate. |
| Metriche interessate | Metriche che hanno causato la generazione di un'informazione: <ul><li>Aumento della durata di una query [secondi].</li><li>Attesa eccessiva [secondi].</li><li>Richieste che hanno raggiunto il timeout [percentuale].</li><li>Richieste che hanno generato errori [percentuale].</li></ul>|
| Valore di impatto | Valore di una metrica misurata. |
| Query interessate e codici di errore | Codice hash o codice di errore di una query. Possono essere usati per creare facilmente correlazioni alle query interessate. Vengono fornite metriche costituite da aumento della durata di una query, tempo di attesa, numero di timeout o codici di errore. |
| Rilevamenti | Rilevamento identificato nel database nel momento in cui si è verificato un evento. Sono disponibili 15 modelli di rilevamento. Per altre informazioni, vedere [Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analisi della causa radice | Analisi della causa radice del problema identificato in un formato umanamente leggibile. Alcune analisi potrebbero contenere raccomandazioni per il miglioramento delle prestazioni, se possibile. |
|||

Per una panoramica pratica sull'uso di Intelligent Insights con Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights si distingue per l'individuazione e la risoluzione dei problemi di prestazioni del database SQL. Per usare Intelligent Insights per risolvere i problemi di prestazioni di database SQL e istanza gestita, vedere [Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Configurare Intelligent Insights

L'output di Intelligent Insights è un log di diagnostica delle prestazioni intelligenti. Questo log può essere usato in diversi modi, tramite trasmissione ad Analisi SQL di Azure, Hub eventi di Azure e archiviazione di Azure o di terze parti del prodotto.

- Usare il prodotto con [Analisi SQL di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) per visualizzare informazioni dettagliate tramite l'interfaccia utente del portale di Azure. Questa è la soluzione integrata di Azure e il modo più comune per visualizzare informazioni dettagliate.
- Usare il prodotto con Hub eventi di Azure per lo sviluppo di monitoraggio e scenari di avviso personalizzati
- Usare il prodotto con archiviazione di Azure per lo sviluppo di applicazioni personalizzate, come ad esempio la creazione di report personalizzati, l'archiviazione a lungo termine dei dati e così via.

L'integrazione di Intelligent Insights con altri prodotti quali Analisi SQL di Azure, Hub eventi di Azure, Archiviazione di Azure o prodotti di terze parti per l'uso viene eseguita innanzitutto abilitando la registrazione di Intelligent Insights ("log SQLInsights") nel pannello impostazioni di diagnostica e configurando i dati di log di Intelligent Insights da trasmettere a uno di questi prodotti.

Per altre informazioni su come abilitare la registrazione di Intelligent Insights e configurare i dati di log da trasmettere a un prodotto che li usi, vedere [Metriche del database SQL di Azure e registrazione diagnostica](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configurazione con Analisi SQL di Azure

La soluzione Analisi SQL di Azure fornisce l'interfaccia utente grafica, la creazione di report e le funzionalità di avviso sulle prestazioni del database, insieme al log di diagnostica dei dati di Intelligent Insights.

> [!TIP]
> Guida rapida introduttiva: il modo più semplice per cominciare con l'uso di Intelligent Insights è usarlo insieme ad Analisi SQL di Azure in modo da fornire un'interfaccia utente grafica per problemi di prestazioni del database. Aggiungere la soluzione Analisi SQL di Azure dal marketplace, creare un'area di lavoro all'interno di questa soluzione e quindi, per ogni database per cui si desidera attivare Intelligent Insights, configurare la trasmissione del log di "SQLInsights" nel pannello delle impostazioni di diagnostica di un database per l'area di lavoro di Azure SQL Analytics.
>

Il pre-requisito consiste nel disporre di Analisi SQL di Azure insieme alla dashboard del portale di Azure dal marketplace e creare un'area di lavoro, vedere [ConfigurareAzure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Per usare Intelligent Insights con Analisi SQL di Azure, configurare i dati di log di Intelligent Insights da trasmettere all'area di lavoro Azure Log Analytics creata nel precedente passaggio, vedere [Metriche del database SQL di Azure e registrazione diagnostica](sql-database-metrics-diag-logging.md).

Di seguito è riportato un esempio del report di Intelligent Insights attraverso Analisi SQL di Azure:

![Report di Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configurare con Hub eventi

Per usare Intelligent Insights con Hub eventi e configurare i dati di log di Intelligent Insights da trasmettere all'Hub eventi, vedere [Trasmettere i log di diagnostica di Azure agli hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Per usare Hub di eventi per configurare gli avvisi e il monitoraggio personalizzati, vedere [Cosa fare con le metriche e i log di diagnostica in Hub eventi](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configurare con Archiviazione di Azure

Per usare Intelligent Insights con Archiviazione e configurare dati di log di Intelligent Insights da trasmettere ad Archiviazione, vedere [Streaming in Archiviazione di Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrazioni personalizzate del log di Intelligent Insights

Per usare Intelligent Insights con strumenti di terze parti o per la creazione di avvisi e monitoraggio personalizzati, vedere [Usare il log di diagnostica delle prestazioni di database di Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

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
- Dove possibile, consigli su come migliorare le prestazioni del database SQL monitorato.

## <a name="query-duration"></a>Durata delle query

Il modello di riduzione delle prestazioni basato sulla durata delle query analizza le singole query e rileva l'aumento del tempo necessario per compilare ed eseguire una query rispetto alla baseline delle prestazioni.

Quando le funzionalità di intelligence integrata del database SQL rilevano un aumento significativo nel tempo di compilazione o di esecuzione di una query con ripercussioni sulle prestazioni del carico di lavoro, questa query viene contrassegnata con l'indicazione di un problema di riduzione delle prestazioni basato sulla durata della query.

Il log di diagnostica di Intelligent Insights restituisce il codice hash della query per cui è stata rilevata una riduzione delle prestazioni. L'hash della query indica se la riduzione delle prestazioni è correlata a un aumento del tempo di compilazione o di esecuzione della query e l'aumento della durata della query.

## <a name="timeout-requests"></a>Richieste di timeout

Il modello di riduzione delle prestazioni basato sulle richieste di timeout analizza le singole query e rileva l'aumento dei timeout a livello di esecuzione della query e i timeout delle richieste complessivi a livello di database rispetto al periodo della baseline delle prestazioni.

Alcune delle query potrebbero raggiungere il timeout anche prima di raggiungere la fase di esecuzione. Le funzionalità di intelligence integrata del database SQL di Azure misurano e analizzano anche, tramite il confronto tra i processi di lavoro interrotti e le richieste effettuate, tutte le query che hanno raggiunto il database indipendentemente dal fatto che abbiano raggiunto o meno la fase di esecuzione.

Dopo che il numero di timeout per le query eseguite o il numero di processi di lavoro interrotti è aumentato fino a superare la soglia gestita dal sistema, un log di diagnostica viene popolato con analisi intelligenti.

Le informazioni generate contengono il numero di richieste che hanno raggiunto il timeout e il numero di query che hanno raggiunto il timeout. Viene fornita l'indicazione se la riduzione delle prestazioni è correlata all'aumento del timeout in fase di esecuzione o a livello complessivo di database. Quando l'aumento dei timeout viene considerato significativo per le prestazioni del database, queste query sono contrassegnate con l'indicazione di un problema di riduzione delle prestazioni basato sul timeout.

## <a name="excessive-wait-times"></a>Tempi di attesa eccessivi

Il modello basato sui tempi di attesa eccessivi monitora le singole query di database e rileva le statistiche di attesa delle query insolitamente elevate che superano le soglie assolute gestite dal sistema. Le metriche seguenti per i tempi di attesa eccessivi delle query vengono osservate usando la nuova funzionalità delle statistiche di attesa di Query Store di SQL Server (sys.query_store_wait_stats):

- Raggiungimento dei limiti delle risorse
- Raggiungimento dei limiti delle risorse del pool elastico
- Numero eccessivo di thread di sessioni o di processi di lavoro
- Blocco eccessivo del database
- Uso elevato della memoria
- Altre statistiche di attesa

Il raggiungimento dei limiti delle risorse o dei limiti delle risorse del pool elastico indica che il consumo di risorse disponibili in una sottoscrizione o nel pool elastico ha superato le soglie assolute, indicando una riduzione delle prestazioni del carico di lavoro. Un numero eccessivo di thread di sessioni o di processi di lavoro indica una condizione in cui il numero di thread di processi di lavoro o di sessioni avviate ha superato le soglie assolute, indicando una riduzione delle prestazioni del carico di lavoro.

Un blocco eccessivo del database indica una condizione in cui il numero di blocchi in un database ha superato una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro. Un uso elevato della memoria è una condizione in cui il numero di thread che richiedono concessioni di memoria ha superato una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro.

Il rilevamento di altre statistiche di attesa indica una condizione in cui le diverse metriche misurate tramite le statistiche di attesa di Query Store superano una soglia assoluta, indicando una riduzione delle prestazioni del carico di lavoro.

Dopo che sono stati rilevati tempi di attesa eccessivi, il log di diagnostica di Intelligent Insights restituisce, a seconda dei dati disponibili, i codici hash delle query responsabili e interessate dalla riduzione delle prestazioni, i dettagli delle metriche che comportano l'attesa delle query in esecuzione e i tempi di attesa misurati.

## <a name="errored-requests"></a>Richieste con errori

Il modello di riduzione delle prestazioni basato sulle richieste con errori monitora le singole query e rileva un aumento del numero di query che hanno generato errori rispetto al periodo della baseline. Questo modello monitora anche le eccezioni critiche che hanno raggiunto soglie assolute gestite dalla funzionalità di intelligence integrata del database SQL. Il sistema prende automaticamente in considerazione il numero di richieste di query effettuate nel database e tutte le modifiche del carico di lavoro nel periodo monitorato.

Quando l'aumento misurato nelle richieste con errori in relazione al numero complessivo delle richieste effettuate viene considerato significativo per le prestazioni del carico di lavoro, le query interessate sono contrassegnate con l'indicazione di un problema di riduzione delle prestazioni basato sulle richieste con errori.

Il log di Intelligent Insights restituisce il numero di richieste con errori. Indica se la riduzione delle prestazioni è correlata all'aumento delle richieste con errori o al raggiungimento di una delle soglie di eccezioni critiche monitorate e il tempo misurato della riduzione delle prestazioni.

Nel caso in cui una delle eccezioni critiche monitorate superi le soglie assolute gestite dal sistema, viene generata un'analisi intelligente con informazioni dettagliate sull'eccezione critica.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi di prestazioni del database SQL con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Usare il [log di diagnostica delle prestazioni del database SQL generato da Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Informazioni su come [monitorare un database SQL usando Analisi SQL](../azure-monitor/insights/azure-sql.md).
- Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
