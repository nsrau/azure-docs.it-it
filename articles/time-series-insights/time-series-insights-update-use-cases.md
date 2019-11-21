---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227739"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casi d'uso di Anteprima di Azure Time Series Insights

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* Quali sono i casi d'uso comuni per Time Series Insights?
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>Introduzione

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. È utilizzata per raccogliere, elaborare, archiviare, analizzare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per le serie temporali. Time Series Insights è ideale per l'esplorazione di dati ad hoc e per l'analisi operativa. Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Esplorazione dei dati e rilevamento di anomalie degli oggetti visivi

Esplora e analizza immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei tuoi dati. Time Series Insights offre prestazioni near real-time per i tuoi carichi di lavoro di analisi di IoT e DevOps.

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* Time Series Insights non richiede di preparare i dati in anticipo. 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* Dopo aver stabilito la connessione, è possibile visualizzare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati.

Time Series Insights è intuitivo e semplice da usare. È possibile interagire con i dati senza scrivere nemmeno una riga di codice. There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. Offre anche funzionalità di esplorazione tramite selezione e clic per gli utenti non esperti.

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

Esistono tre modi principali per interagire con i dati archiviati in Time Series Insights:

* Il primo e il più semplice per iniziare consiste nell'usare lo strumento di esplorazione di Anteprima di Time Series Insights. È possibile usarlo per visualizzare rapidamente tutti i dati IoT in un'unica posizione. It provides tools like the heat map to help you spot anomalies in your data. Fornisce anche una visualizzazione in prospettiva. Usarla per confrontare fino a quattro visualizzazioni da uno o più ambienti Time Series Insights in un singolo dashboard. Il dashboard offre una visualizzazione dei dati della serie temporale in tutte le posizioni. Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md). Per pianificare l'ambiente Time Series Insights, vedere [Pianificazione di Time Series Insights](./time-series-insights-update-plan.md).

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. Con poche righe di codice, è possibile creare query avanzate. Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. Tutti questi elementi sono predefiniti se si usa l'SDK. L'SDK astrae anche le API di query di Time Series Insights. È possibile usarle per creare predicati simili a SQL per eseguire query sui dati da visualizzare in un dashboard. Per le soluzioni ibride a livello di presentazione, Time Series Insights offre URL con parametri. Forniscono punti di connessione ottimali con lo strumento di esplorazione di Anteprima di Time Series Insights per un'analisi dettagliata dei dati.

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* Il terzo modo per iniziare consiste nell'usare le API avanzate per eseguire query sui dati archiviati in Time Series Insights. Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. Tutti questi operatori consentono alle applicazioni downstream di trovare rapidamente tendenze e modelli interessanti nei dati. Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analisi operativa e miglioramento dell'efficienza dei processi

Usare Time Series Insights per monitorare l'integrità, l'utilizzo e le prestazioni delle apparecchiature su larga scala. Time Series Insights offre un modo semplice per misurare l'efficienza operativa. Time Series Insights consente di gestire carichi di lavoro IoT diversi e imprevedibili senza sacrificare l'inserimento o le prestazioni delle query.

[![Panoramica](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Lo streaming e l'elaborazione continua dei dati provenienti dai processi operativi possono trasformare con successo qualsiasi azienda se combinati con la soluzione o la tecnologia più adatta. Spesso queste soluzioni sono una combinazione di più sistemi. They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

Questi modelli hanno spesso inizio con piattaforme abilitate per IoT, che inseriscono miliardi di eventi da dispositivi e sensori con diverse impostazioni locali. Questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni e azioni in tempo reale. Data is typically archived to warm and cold store for near real-time and batch analytics.

I dati raccolti vengono sottoposti a una serie di processi per pulirli e contestualizzarli per gli scenari di analisi e query downstream. Azure offre servizi avanzati che possono essere applicati a scenari IoT come la produzione e la manutenzione di asset. Questi servizi includono Time Series Insights, Hub IoT, Hub eventi, Analisi di flusso di Azure, Funzioni di Azure, App per la logica di Azure, Azure Databricks, Azure Machine Learning e Power BI.

L'architettura della soluzione può essere ottenuta nel modo seguente:

* Inserire i dati tramite l'hub IoT o Hub eventi per sicurezza, velocità effettiva e latenza ottimali.
* Eseguire l'elaborazione dati e i calcoli. Creare un grafico a imbuto con i dati inseriti tramite servizi come Analisi di flusso, App per la logica e Funzioni di Azure. Il servizio usato dipende dalle esigenze specifiche di elaborazione dati.
* Viene eseguito il push dei segnali elaborati dalla pipeline di elaborazione a Time Series Insights per l'archiviazione e l'analisi.

Time Series Insights offre l'esplorazione dei dati quasi in tempo reale e informazioni dettagliate basate sugli asset sui dati cronologici. A seconda delle esigenze aziendali, si possono eseguire processi MapReduce e Hive sui dati archiviati in Time Series Insights connettendo Time Series Insights ad Azure HDInsight. I dati archiviati in Time Series Insights sono disponibili per Power BI e altre applicazioni dei clienti tramite le API di query della superficie pubblica di Time Series Insights. Questi dati sono utilizzabili per scenari avanzati di intelligence operativa e di business intelligence.

## <a name="advanced-analytics"></a>Advanced Analytics

Ottenere l'integrazione con servizi di analisi avanzati come Machine Learning e Azure Databricks. Time Series Insights inserisce dati non elaborati da milioni di dispositivi. Aggiunge dati contestuali che possono essere utilizzati senza problemi da un gruppo di servizi di analisi di Azure.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

L'analisi avanzata e l'apprendimento automatico utilizzano ed elaborano grandi volumi di dati. Questi dati vengono usati per prendere decisioni basate sui dati ed eseguire analisi predittive. Nei casi d'uso delle soluzioni IoT, gli algoritmi di analisi avanzata apprendono dai dati raccolti da milioni di dispositivi. Questi dispositivi trasmettono i dati più volte al secondo. I dati raccolti dai dispositivi IoT non sono elaborati. Non includono informazioni contestuali, ad esempio la posizione del dispositivo e l'unità di misura del sensore che effettua la lettura. Di conseguenza, i dati non elaborati sono difficili da utilizzare direttamente per l'analisi avanzata.

Time Series Insights colma il divario tra i dati IoT e l'analisi avanzata in due modi semplici e convenienti:

* In primo luogo, Time Series Insights raccoglie dati di telemetria non elaborati da milioni di dispositivi usando l'hub IoT. Arricchisce i dati con informazioni contestuali e li converte in formato Parquet. Questo formato può integrarsi facilmente con altri servizi di analisi avanzata, ad esempio Machine Learning, Azure Databricks e applicazioni di terze parti.

    Time Series Insights può essere usato come origine di dati reali per tutti i dati in un'organizzazione. Crea un repository centrale che può essere utilizzato dai carichi di lavoro di analisi downstream. Poiché Time Series Insights è un servizio di archiviazione in tempo quasi reale, i modelli di analisi avanzata possono apprendere continuamente dai dati di telemetria IoT in ingresso. Di conseguenza, i modelli possono eseguire stime più accurate.

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. Questa procedura consente alle organizzazioni di ottimizzare e modificare i propri modelli. Time Series Insights rende più semplice visualizzare lo streaming di dati di telemetria sullo stesso piano degli output dei modelli con training. In questo modo, consente ai team di data science di individuare le anomalie e identificare gli schemi.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md).
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* Vedere la documentazione del [client Time Series Insights](https://github.com/Microsoft/tsiclient).
