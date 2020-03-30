---
title: Anteprima dei casi d'uso - Informazioni dettagliate sulle serie temporali di Azure Documenti Microsoft
description: Informazioni sui casi d'uso di Azure Time Series Insights Preview.Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087384"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casi d'uso di Anteprima di Azure Time Series Insights

Questo articolo riepiloga diversi casi d'uso comuni per Azure Time Series Insights Preview.This article summarizes several common use cases for Azure Time Series Insights Preview. I consigli riportati in questo articolo fungono da punto di partenza per sviluppare applicazioni e soluzioni con Time Series Insights.The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

In particolare, in questo articolo vengono fornite le risposte alle domande seguenti:Specifically, this article answers the following questions:

* Quali sono i casi d'uso comuni per Time Series Insights?
* Quali sono i vantaggi dell'utilizzo di Time Series Insights per l'esplorazione dei dati e il rilevamento delle [anomalie visive?](#data-exploration-and-visual-anomaly-detection)
* Quali sono i vantaggi dell'utilizzo di Time Series Insights per [l'analisi operativa e l'efficienza dei processi?](#operational-analysis-and-driving-process-efficiency)
* Quali sono i vantaggi dell'utilizzo di Time Series Insights per [l'analisi avanzata?](#advanced-analytics)

Una panoramica di questi scenari di utilizzo è descritta nelle sezioni seguenti.

## <a name="introduction"></a>Introduzione

Azure Time Series Insights è un'offerta end-to-end, platform-as-a-service. È utilizzata per raccogliere, elaborare, archiviare, analizzare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per le serie temporali. Time Series Insights è ideale per l'esplorazione di dati ad hoc e per l'analisi operativa. Time Series Insights è un'offerta di servizi personalizzata estensibile in modo univoco che soddisfa le grandi esigenze delle distribuzioni IoT industriali.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Esplorazione dei dati e rilevamento di anomalie degli oggetti visivi

Esplorare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati. Time Series Insights offre prestazioni near real-time per i carichi di lavoro di analisi di IoT e DevOps.

[![Esplora dati](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

La maggior parte dei clienti concorda sul fatto che la quantità minima di tempo necessaria per ottenere informazioni dettagliate è una delle caratteristiche distintive di Time Series Insights:

* Time Series Insights non richiede di preparare i dati in anticipo. 
* Funziona rapidamente per connetterti a miliardi di eventi nelle istanze dell'hub IoT di Azure o degli hub eventi di Azure in pochi minuti. 
* Dopo aver stabilito la connessione, è possibile visualizzare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati.

Time Series Insights è intuitivo e semplice da usare. È possibile interagire con i dati senza scrivere nemmeno una riga di codice. Non è inoltre necessario immettere una nuova lingua da imparare, anche se Time Series Insights fornisce un linguaggio di query granulare basato su testo per gli utenti avanzati che hanno familiarità con SQL. Offre anche funzionalità di esplorazione tramite selezione e clic per gli utenti non esperti.

I clienti possono sfruttare la velocità per diagnosticare rapidamente i problemi relativi agli asset. Possono eseguire l'analisi DevOps per ottenere la causa radice di un bug in una soluzione IoT.They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. Possono anche identificare le aree da segnalare per ulteriori indagini nell'ambito delle loro iniziative di data science. 

Esistono tre modi principali per interagire con i dati archiviati in Time Series Insights:

* Il primo e il più semplice per iniziare consiste nell'usare lo strumento di esplorazione di Anteprima di Time Series Insights. È possibile usarlo per visualizzare rapidamente tutti i dati IoT in un'unica posizione. Fornisce strumenti come la mappa termica per aiutarti a individuare le anomalie nei tuoi dati. Fornisce anche una visualizzazione in prospettiva. Usarla per confrontare fino a quattro visualizzazioni da uno o più ambienti Time Series Insights in un singolo dashboard. Il dashboard offre una visualizzazione dei dati della serie temporale in tutte le posizioni. Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md). Per pianificare l'ambiente Time Series Insights, vedere [Pianificazione di Time Series Insights](./time-series-insights-update-plan.md).

* Il secondo modo per iniziare consiste nell'utilizzare JavaScript SDK per incorporare rapidamente grafici e grafici potenti nell'applicazione Web. Con poche righe di codice, è possibile creare query avanzate. Usarli per popolare grafici a linee, grafici a torta, grafici a barre, mappe termiche, griglie di dati e altro ancora. Tutti questi elementi sono predefiniti se si usa l'SDK. L'SDK astrae anche le API di query di Time Series Insights. È possibile usarle per creare predicati simili a SQL per eseguire query sui dati da visualizzare in un dashboard. Per le soluzioni ibride a livello di presentazione, Time Series Insights offre URL con parametri. Forniscono punti di connessione ottimali con lo strumento di esplorazione di Anteprima di Time Series Insights per un'analisi dettagliata dei dati.

  * Per ulteriori informazioni su JavaScript SDK, vedere la [libreria client Time Series Insights JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e la documentazione del client Time Series [Insights.](https://github.com/Microsoft/tsiclient)

  * Per altre informazioni sulla condivisione degli URL e sulla nuova interfaccia utente, vedere [Visualizzare i dati in Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* Il terzo modo per iniziare consiste nell'usare le API avanzate per eseguire query sui dati archiviati in Time Series Insights. Time Series Insights include `from`operatori `to` `first`temporali `last`quali , , , e . Dispone di aggregazioni e `average`trasformazioni quali `order by`, `DateHistogram` `min`, `max`, `split by`, , e . Dispone inoltre di operatori `has` `in`di `and` `or`filtro `greater than`quali `REGEX`, , , , e . Tutti questi operatori consentono alle applicazioni downstream di trovare rapidamente tendenze e modelli interessanti nei dati. Usali per popolare le visualizzazioni casalinghe per individuare le anomalie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analisi operativa e miglioramento dell'efficienza dei processi

Usare Time Series Insights per monitorare l'integrità, l'utilizzo e le prestazioni delle apparecchiature su larga scala. Time Series Insights offre un modo semplice per misurare l'efficienza operativa. Time Series Insights consente di gestire carichi di lavoro IoT diversi e imprevedibili senza sacrificare l'inserimento o le prestazioni delle query.

[![Panoramica](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Lo streaming e l'elaborazione continua dei dati provenienti dai processi operativi possono trasformare con successo qualsiasi azienda se combinati con la soluzione o la tecnologia più adatta. Spesso queste soluzioni sono una combinazione di più sistemi. Consentono l'esplorazione e l'analisi dei dati che cambiano costantemente, in particolare nell'area di autenticazione IoT, e condividono un modello comune.

Questi modelli hanno spesso inizio con piattaforme abilitate per IoT, che inseriscono miliardi di eventi da dispositivi e sensori con diverse impostazioni locali. Questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni e azioni in tempo reale. I dati vengono in genere archiviati in cold store e Cold Store per analisi quasi in tempo reale e batch.

I dati raccolti vengono sottoposti a una serie di processi per pulirli e contestualizzarli per gli scenari di analisi e query downstream. Azure offre servizi avanzati che possono essere applicati a scenari IoT come la produzione e la manutenzione di asset. Questi servizi includono Time Series Insights, Hub IoT, Hub eventi, Analisi di flusso di Azure, Funzioni di Azure, App per la logica di Azure, Azure Databricks, Azure Machine Learning e Power BI.

L'architettura della soluzione può essere ottenuta nel modo seguente:

* Inserire i dati tramite l'hub IoT o Hub eventi per sicurezza, velocità effettiva e latenza ottimali.
* Eseguire l'elaborazione dati e i calcoli. Creare un grafico a imbuto con i dati inseriti tramite servizi come Analisi di flusso, App per la logica e Funzioni di Azure. Il servizio usato dipende dalle esigenze specifiche di elaborazione dati.
* Viene eseguito il push dei segnali elaborati dalla pipeline di elaborazione a Time Series Insights per l'archiviazione e l'analisi.

Time Series Insights offre l'esplorazione dei dati quasi in tempo reale e informazioni dettagliate basate sugli asset sui dati cronologici. A seconda delle esigenze aziendali, si possono eseguire processi MapReduce e Hive sui dati archiviati in Time Series Insights connettendo Time Series Insights ad Azure HDInsight. I dati archiviati in Time Series Insights sono disponibili per Power BI e altre applicazioni dei clienti tramite le API di query della superficie pubblica di Time Series Insights. Questi dati sono utilizzabili per scenari avanzati di intelligence operativa e di business intelligence.

## <a name="advanced-analytics"></a>Analisi avanzata

Ottenere l'integrazione con servizi di analisi avanzati come Machine Learning e Azure Databricks. Time Series Insights inserisce dati non elaborati da milioni di dispositivi. Aggiunge dati contestuali che possono essere utilizzati senza problemi da un gruppo di servizi di analisi di Azure.

[![Analitica](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

L'analisi avanzata e l'apprendimento automatico utilizzano ed elaborano grandi volumi di dati. Questi dati vengono usati per prendere decisioni basate sui dati ed eseguire analisi predittive. Nei casi d'uso delle soluzioni IoT, gli algoritmi di analisi avanzata apprendono dai dati raccolti da milioni di dispositivi. Questi dispositivi trasmettono i dati più volte al secondo. I dati raccolti dai dispositivi IoT non sono elaborati. Non includono informazioni contestuali, ad esempio la posizione del dispositivo e l'unità di misura del sensore che effettua la lettura. Di conseguenza, i dati non elaborati sono difficili da utilizzare direttamente per l'analisi avanzata.

Time Series Insights colma il divario tra i dati IoT e l'analisi avanzata in due modi semplici e convenienti:

* In primo luogo, Time Series Insights raccoglie dati di telemetria non elaborati da milioni di dispositivi usando l'hub IoT. Arricchisce i dati con informazioni contestuali e li converte in formato Parquet. Questo formato può integrarsi facilmente con altri servizi di analisi avanzata, ad esempio Machine Learning, Azure Databricks e applicazioni di terze parti.

    Time Series Insights può essere usato come origine di dati reali per tutti i dati in un'organizzazione. Crea un repository centrale che può essere utilizzato dai carichi di lavoro di analisi downstream. Poiché Time Series Insights è un servizio di archiviazione in tempo quasi reale, i modelli di analisi avanzata possono apprendere continuamente dai dati di telemetria IoT in ingresso. Di conseguenza, i modelli possono eseguire stime più accurate.

* In secondo luogo, l'output dei modelli di apprendimento automatico e previsione può essere inserito in Time Series Insights per visualizzare e archiviare i risultati. Questa procedura consente alle organizzazioni di ottimizzare e modificare i propri modelli. Time Series Insights rende più semplice visualizzare lo streaming di dati di telemetria sullo stesso piano degli output dei modelli con training. In questo modo, consente ai team di data science di individuare le anomalie e identificare gli schemi.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere lo [strumento di esplorazione di Anteprima di Time Series Insights](./time-series-insights-update-explorer.md).
* Leggere [Time Series Insights Preview che prevede](./time-series-insights-update-plan.md) di pianificare l'ambiente.
* Vedere la documentazione del [client Time Series Insights](https://github.com/Microsoft/tsiclient).
