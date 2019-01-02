---
title: Casi d'uso di Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sui casi d'uso di Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: a6ee03197a78a75ca57d0ed049cdd6a8df8b2149
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272967"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casi d'uso di Anteprima di Azure Time Series Insights

Questo articolo fornisce una panoramica di diversi casi d'uso comuni per Anteprima di Azure Time Series Insights. I consigli forniti sono un punto di partenza per sviluppare applicazioni e soluzioni con Time Series Insights.

Dopo aver letto questo articolo, è possibile rispondere alle domande seguenti:

* Quali sono i casi d'uso comuni per Time Series Insights?
* Quali sono i vantaggi derivanti dall'uso di Time Series Insights per il rilevamento anomalie degli oggetti visivi e l'esplorazione dei dati?
* Quali sono i vantaggi derivanti dall'uso di Time Series Insights per l'analisi operativa e l'efficienza dei processi?
* Quali sono i vantaggi derivanti dall'uso di Time Series Insights per l'analisi avanzata?

Questo documento offre una panoramica dei casi d'uso per cui è progettata Anteprima di Azure Time Series Insights.

## <a name="introduction"></a>Introduzione

Time Series Insights è una piattaforma end-to-end distribuita come servizio. È utilizzata per inserire, elaborare, archiviare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per le serie temporali. Time Series Insights è ideale per l'esplorazione di dati ad hoc e per l'analisi operativa. Time Series Insights è un servizio estendibile personalizzato in modo unico in grado di soddisfare le esigenze di vario tipo delle distribuzioni IoT industriali.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Esplorazione dei dati e rilevamento di anomalie degli oggetti visivi

Esplorare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati. Time Series Insights offre prestazioni near real-time per i carichi di lavoro di analisi di IoT e DevOps.

![Esplora dati][1]

La maggior parte dei clienti concorda sul fatto che il tempo di analisi è tra gli asset più efficaci di Time Series Insights. Time Series Insights non richiede di preparare i dati in anticipo. Essendo molto veloce, connette gli utenti a miliardi di eventi nell'hub IoT di Azure o in Hub eventi di Azure in pochi minuti. Dopo aver stabilito la connessione, è possibile visualizzare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati. 

Time Series Insights è intuitivo e semplice da usare. È possibile interagire con i dati senza scrivere nemmeno una riga di codice. Non è necessario apprendere nuovi linguaggi. Time Series Insights offre query granulari basate su testo per utenti avanzati esperti di SQL. Offre anche funzionalità di esplorazione tramite selezione e clic per gli utenti non esperti.

I clienti sfruttano la velocità per diagnosticare rapidamente i problemi relativi agli asset. Possono eseguire DevOps per trovare la causa radice di un bug in una soluzione IoT. Possono anche identificare le aree da esaminare per le iniziative di data science.  

Esistono tre modi principali per interagire con i dati archiviati in Time Series Insights:

- Il primo e il più semplice per iniziare consiste nell'usare lo strumento di esplorazione di Anteprima di Time Series Insights. È possibile usarlo per visualizzare rapidamente tutti i dati IoT in un'unica posizione. Fornisce strumenti, ad esempio la mappa termica, che consentono di individuare le anomalie nei dati. Fornisce anche una visualizzazione in prospettiva. Usarla per confrontare fino a quattro visualizzazioni da uno o più ambienti Time Series Insights in un singolo dashboard. Il dashboard offre una visualizzazione dei dati della serie temporale in tutte le posizioni. Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md). Per pianificare l'ambiente Time Series Insights, vedere [Pianificazione di Time Series Insights](./time-series-insights-update-plan.md).

- Il secondo modo per iniziare è usare JavaScript SDK per incorporare rapidamente diagrammi e grafici avanzati nell'applicazione Web. Con poche righe di codice, è possibile creare query avanzate. Usarle per popolare grafici a linee, grafici a torta, grafici a barre, mappe termiche, griglie dati e altro ancora. Tutti questi elementi sono predefiniti se si usa l'SDK. L'SDK astrae anche le API di query di Time Series Insights. È possibile usarle per creare predicati simili a SQL per eseguire query sui dati da visualizzare in un dashboard. Per le soluzioni ibride a livello di presentazione, Time Series Insights offre URL con parametri. Forniscono punti di connessione ottimali con lo strumento di esplorazione di Anteprima di Time Series Insights per un'analisi dettagliata dei dati. Per altre informazioni su JavaScript SDK, vedere la [libreria client JS per Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) e la documentazione sul [client di Time Series Insights](https://github.com/Microsoft/tsiclient). Per altre informazioni sugli URL con parametri, vedere [URL con parametri](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- Il terzo modo per iniziare consiste nell'usare le API avanzate per eseguire query sui dati archiviati in Time Series Insights. Time Series Insights ha operatori temporali, ad esempio from, to, first e last. Ha aggregazioni e trasformazioni, ad esempio average, min, max, split by, order by e DateHistogram. Ha anche operatori di filtro, ad esempio has, in and, or, greater than e REGEX. Tutti questi operatori consentono alle applicazioni downstream di trovare rapidamente tendenze e modelli interessanti nei dati. Usarli per popolare le visualizzazioni personalizzate e poter individuare le anomalie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analisi operativa e miglioramento dell'efficienza dei processi

Usare Time Series Insights per monitorare l'integrità, l'utilizzo e le prestazioni delle apparecchiature su larga scala. Time Series Insights offre un modo semplice per misurare l'efficienza operativa. Time Series Insights consente di gestire carichi di lavoro IoT diversi e imprevedibili senza sacrificare l'inserimento o le prestazioni delle query.

![Panoramica][2]

Lo streaming e l'elaborazione continua dei dati provenienti dai processi operativi possono trasformare con successo qualsiasi azienda se combinati con la soluzione o la tecnologia più adatta. Spesso queste soluzioni sono una combinazione di più sistemi. Consentono l'esplorazione e l'analisi dei dati che cambiano continuamente, in particolare nell'area di autenticazione IoT, e condividono un modello comune.

Questi modelli hanno spesso inizio con piattaforme abilitate per IoT, che inseriscono miliardi di eventi da dispositivi e sensori con diverse impostazioni locali. Questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni e azioni in tempo reale. I dati vengono in genere archiviati in risorse di archiviazione offline sicura e ad accesso frequente per l'analisi in tempo quasi reale e in batch. 

I dati raccolti vengono sottoposti a una serie di processi per pulirli e contestualizzarli per gli scenari di analisi e query downstream. Azure offre servizi avanzati che possono essere applicati a scenari IoT come la produzione e la manutenzione di asset. Questi servizi includono Time Series Insights, Hub IoT, Hub eventi, Analisi di flusso di Azure, Funzioni di Azure, App per la logica di Azure, Azure Databricks, Azure Machine Learning e Power BI.

L'architettura della soluzione può essere ottenuta nel modo seguente:

- Inserire i dati tramite l'hub IoT o Hub eventi per sicurezza, velocità effettiva e latenza ottimali. 
- Eseguire l'elaborazione dati e i calcoli. Creare un grafico a imbuto con i dati inseriti tramite servizi come Analisi di flusso, App per la logica e Funzioni di Azure. Il servizio usato dipende dalle esigenze specifiche di elaborazione dati. 
- Viene eseguito il push dei segnali elaborati dalla pipeline di elaborazione a Time Series Insights per l'archiviazione e l'analisi. 

Time Series Insights offre l'esplorazione dei dati quasi in tempo reale e informazioni dettagliate basate sugli asset sui dati cronologici. A seconda delle esigenze aziendali, si possono eseguire processi MapReduce e Hive sui dati archiviati in Time Series Insights connettendo Time Series Insights ad Azure HDInsight. I dati archiviati in Time Series Insights sono disponibili per Power BI e altre applicazioni dei clienti tramite le API di query della superficie pubblica di Time Series Insights. Questi dati sono utilizzabili per scenari avanzati di intelligence operativa e di business intelligence.

## <a name="advanced-analytics"></a>Analisi avanzata

Ottenere l'integrazione con servizi di analisi avanzati come Machine Learning e Azure Databricks. Time Series Insights inserisce dati non elaborati da milioni di dispositivi. Aggiunge dati contestuali che possono essere utilizzati senza problemi da un gruppo di servizi di analisi di Azure.

![Analytics][3]

L'analisi avanzata e l'apprendimento automatico utilizzano ed elaborano grandi volumi di dati. Questi dati vengono usati per prendere decisioni basate sui dati ed eseguire analisi predittive. Nei casi d'uso delle soluzioni IoT, gli algoritmi di analisi avanzata apprendono dai dati raccolti da milioni di dispositivi. Questi dispositivi trasmettono i dati più volte al secondo. I dati raccolti dai dispositivi IoT non sono elaborati. Non includono informazioni contestuali, ad esempio la posizione del dispositivo e l'unità di misura del sensore che effettua la lettura. Di conseguenza, i dati non elaborati sono difficili da utilizzare direttamente per l'analisi avanzata.

Time Series Insights colma il divario tra i dati IoT e l'analisi avanzata in due modi semplici e convenienti: 

- In primo luogo, Time Series Insights raccoglie dati di telemetria non elaborati da milioni di dispositivi usando l'hub IoT. Arricchisce i dati con informazioni contestuali e li converte in formato Parquet. Questo formato può integrarsi facilmente con altri servizi di analisi avanzata, ad esempio Machine Learning, Azure Databricks e applicazioni di terze parti. 

    Time Series Insights può essere usato come origine di dati reali per tutti i dati in un'organizzazione. Crea un repository centrale che può essere utilizzato dai carichi di lavoro di analisi downstream. Poiché Time Series Insights è un servizio di archiviazione in tempo quasi reale, i modelli di analisi avanzata possono apprendere continuamente dai dati di telemetria IoT in ingresso. Di conseguenza, i modelli possono eseguire stime più accurate.

- In secondo luogo, in Time Series Insights può essere inserito l'output dei modelli di apprendimento automatico e di stima per visualizzarne e archiviarne i risultati. Questa procedura consente alle organizzazioni di ottimizzare e modificare i propri modelli. Time Series Insights rende più semplice visualizzare lo streaming di dati di telemetria sullo stesso piano degli output dei modelli con training. In questo modo, consente ai team di data science di individuare le anomalie e identificare gli schemi.  

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md).
- Per pianificare l'ambiente, vedere [Pianificazione di Anteprima di Time Series Insights](./time-series-insights-update-plan.md).
- Vedere la documentazione del [client Time Series Insights](https://github.com/Microsoft/tsiclient).

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
