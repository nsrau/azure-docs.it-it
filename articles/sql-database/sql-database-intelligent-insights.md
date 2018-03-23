---
title: Monitorare l'utilizzo del database con Intelligent Insights - Database SQL di Azure | Microsoft Docs
description: Intelligent Insights per il database SQL di Azure usa le funzionalità di intelligence integrata per monitorare in modo continuativo l'utilizzo del database grazie all'intelligenza artificiale e di rilevare gli eventi che possono causare interruzioni del funzionamento e una riduzione delle prestazioni.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 8654c35bb9c6e46a47f52dee6ab7969f996b42f4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="intelligent-insights"></a>Intelligent Insights

Intelligent Insights per il database SQL di Azure assicura un maggior controllo sulle prestazioni del database.

Intelligent Insights usa le funzionalità di intelligence integrata per monitorare in modo continuativo l'utilizzo del database grazie all'intelligenza artificiale e rilevare gli eventi che possono causare interruzioni del funzionamento e una riduzione delle prestazioni. Dopo aver rilevato questo tipo di eventi, viene eseguita un'analisi dettagliata che genera un log di diagnostica con una valutazione intelligente del problema. Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni. 

## <a name="what-can-intelligent-insights-do-for-you"></a>Quali sono i vantaggi di Intelligent Insights?

Intelligent Insights è una funzionalità unica dell'intelligence integrata di Azure che offre i vantaggi seguenti:

- Monitoraggio proattivo
- Analisi delle prestazioni personalizzate
- Rilevamento tempestivo della riduzione delle prestazioni del database
- Analisi della causa radice dei problemi rilevati
- Raccomandazioni per il miglioramento delle prestazioni
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="how-does-intelligent-insights-work"></a>Come funziona Intelligent Insights?

Intelligent Insights analizza le prestazioni del database SQL confrontando il carico di lavoro del database dell'ultima ora con la baseline del carico di lavoro degli ultimi sette giorni. Il carico di lavoro del database è costituito dalle query identificate come maggiormente significative per le prestazioni del database, ad esempio quelle più ripetute e di dimensioni maggiori. Ogni database è unico per struttura, dati, uso e applicazione, motivo per cui ogni baseline del carico di lavoro generata è specifica e unica per una singola istanza. Intelligent Insights, indipendentemente dalla baseline del carico di lavoro, consente inoltre di monitorare le soglie operative assolute e di rilevare problemi sulla base di tempi di attesa eccessivi ed eccezioni critiche e problemi con parametrizzazioni delle query che potrebbero influire sulle prestazioni.

Dopo aver rilevato un problema di riduzione delle prestazioni in base a più metriche osservate con l'ausilio dell'intelligenza artificiale, viene eseguita l'analisi. Viene generato un log di diagnostica con un'analisi intelligente della situazione del database. Con Intelligent Insights è semplice tenere traccia del problema di prestazioni del database dalla sua comparsa fino alla risoluzione. Ogni problema individuato viene monitorato per tutto il suo ciclo di vita, dal rilevamento del problema iniziale alla verifica del miglioramento delle prestazioni, fino al suo completamento. Gli aggiornamenti vengono forniti ogni 15 minuti nel log di diagnostica. 

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

I problemi di prestazioni registrati nel log di diagnostica sono contrassegnati con uno dei tre stati di un ciclo di vita del problema: "Attivo", "Verifica in corso" e "Completato". Quando viene rilevato un problema di prestazioni e finché viene ritenuto presente dalle funzionalità di intelligence integrata del database SQL, il problema è contrassegnato come "Attivo". Quando il problema è considerato attenuato, viene verificato e lo stato del problema è modificato in "Verifica in corso". Dopo che il problema è considerato risolto dalle funzionalità di intelligence integrata del database SQL, lo stato del problema è contrassegnato come "Completato".

## <a name="use-intelligent-insights"></a>Usare Intelligent Insights

Intelligent Insights è un log di diagnostica delle prestazioni intelligenti. Può essere integrato con altri prodotti d'uso e applicazioni specifiche quali Azure Log Analytics, Hub eventi di Azure e Archiviazione di Azure, o prodotti di terze parti. 

Intelligent Insights insieme a Azure Log Analytics viene in genere usato per visualizzare le informazioni dettagliate tramite un Web browser ed è probabilmente uno dei modi più semplici per iniziare a usare il prodotto. Intelligent Insights con Hub eventi di Azure viene in genere usato per configurare gli scenari di monitoraggio e avvisi personalizzati. Intelligent Insights con Archiviazione di Azure viene generalmente usato per lo sviluppo di applicazioni personalizzate, ad esempio per la creazione di report personalizzati o anche per l'archiviazione e il recupero dei dati.

L'integrazione di Intelligent Insights con altri prodotti quali Azure Log Analytics, Hub eventi di Azure, Archiviazione di Azure o prodotti di terze parti per l'uso viene eseguita innanzitutto abilitando la registrazione di Intelligent Insights (log SQLInsights) e configurando i dati di log di Intelligent Insights da trasmettere a uno di questi prodotti. Per altre informazioni su come abilitare la registrazione di Intelligent Insights e configurare i dati di log da trasmettere a un prodotto che li usi, vedere [Metriche del database SQL di Azure e registrazione diagnostica](sql-database-metrics-diag-logging.md). 

Per una panoramica pratica sull'uso di Intelligent Insights con Azure Log Analytics e per scenari di uso tipici, vedere il video incorporato:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights si distingue per l'individuazione e la risoluzione dei problemi di prestazioni del database SQL. Per usare Intelligent Insights per risolvere i problemi di prestazioni di database SQL, vedere [Risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="set-up-intelligent-insights-with-log-analytics"></a>Configurare Intelligent Insights con Log Analytics 

La soluzione Log Analytics fornisce funzionalità per la creazione di report e avvisi basate sui dati del log di diagnostica di Intelligent Insights.

Per usare Intelligent Insights con Log Analytics e configurare i dati di log di Intelligent Insights da trasmettere a Log Analytics, vedere [Metriche del database SQL di Azure e registrazione diagnostica](sql-database-metrics-diag-logging.md). 

Di seguito è riportato un esempio del report di Intelligent Insights in Analisi SQL di Azure:

![Report di Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Dopo che il log di diagnostica di Intelligent Insights è stato configurato per trasmettere i dati ad Analisi SQL, è possibile [monitorare il database SQL di Azure tramite Analisi SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Configurare Intelligent Insights con Hub eventi

Per usare Intelligent Insights con Hub eventi e configurare i dati di log di Intelligent Insights da trasmettere all'Hub eventi, vedere [Trasmettere i log di diagnostica di Azure agli hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Per usare Hub di eventi per configurare gli avvisi e il monitoraggio personalizzati, vedere [Cosa fare con le metriche e i log di diagnostica in Hub eventi](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Configurare Intelligent Insights con Archiviazione

Per usare Intelligent Insights con Archiviazione e configurare dati di log di Intelligent Insights da trasmettere ad Archiviazione, vedere [Streaming in Archiviazione di Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integrazioni personalizzate del log di Intelligent Insights

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

* Dettagli dei problemi di prestazioni rilevati. 
* Analisi della causa radice dei problemi rilevati. 
* Dove possibile, consigli su come migliorare le prestazioni del database SQL monitorato.

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
* Informazioni su come [risolvere i problemi di prestazioni del database SQL con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Usare il [log di diagnostica delle prestazioni del database SQL generato da Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
* Informazioni su come [monitorare un database SQL usando Analisi SQL](../log-analytics/log-analytics-azure-sql.md).
* Informazioni su come [raccogliere e usare i dati dei log dalle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


