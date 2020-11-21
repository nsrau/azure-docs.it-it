---
title: Casi d'uso di Gen2-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sui casi d'uso di Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b8f13a20232fab61dc082c1b12b7ddaa11807554
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016191"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Casi d'uso di Azure Time Series Insights Gen2

Questo articolo riepiloga diversi casi d'uso comuni per Azure Time Series Insights Gen2. I consigli forniti in questo articolo costituiscono un punto di partenza per sviluppare applicazioni e soluzioni con Azure Time Series Insights Gen2.

In particolare, questo articolo risponde alle domande seguenti:

* Quali sono i casi d'uso comuni per Azure Time Series Insights Gen2?
* Quali sono i vantaggi dell'uso di Azure Time Series Insights Gen2 per l' [esplorazione dei dati e il rilevamento delle anomalie visive](#data-exploration-and-visual-anomaly-detection)?
* Quali sono i vantaggi derivanti dall'utilizzo di Azure Time Series Insights Gen2 per [l'analisi operativa e l'efficienza dei processi](#operational-analysis-and-driving-process-efficiency)?
* Quali sono i vantaggi dell'uso di Azure Time Series Insights Gen2 per l' [analisi avanzata](#advanced-analytics)?

Le sezioni seguenti illustrano una panoramica di questi scenari di utilizzo.

## <a name="introduction"></a>Introduzione

Azure Time Series Insights Gen2 è un'offerta di piattaforma distribuita come servizio end-to-end. È utilizzata per raccogliere, elaborare, archiviare, analizzare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per le serie temporali. È ideale per l'analisi operativa e l'esplorazione dei dati ad hoc. Azure Time Series Insights Gen2 è un'offerta di servizio personalizzata e estendibile in modo univoco che soddisfi le esigenze generali delle distribuzioni di tutto il settore.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Esplorazione dei dati e rilevamento di anomalie degli oggetti visivi

Esplorare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati. Azure Time Series Insights Gen2 offre prestazioni quasi in tempo reale per i carichi di lavoro di analisi e DevOps.

[![Esplora dati](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

La maggior parte dei clienti accetta che la quantità minima di tempo necessaria per ottenere informazioni è una delle funzionalità principali di Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 non richiede alcuna preparazione iniziale dei dati.
* Funziona rapidamente per connettersi a miliardi di eventi nell'hub Azure Internet o nelle istanze di hub eventi di Azure in pochi minuti.
* Dopo aver stabilito la connessione, è possibile visualizzare e analizzare immediatamente miliardi di eventi per rilevare anomalie e individuare tendenze nascoste nei dati.

Azure Time Series Insights Gen2 è intuitivo e semplice da usare. È possibile interagire con i dati senza scrivere nemmeno una riga di codice. Anche se Azure Time Series Insights Gen2 fornisce un linguaggio di query granulare basato su testo per gli utenti avanzati che hanno familiarità con SQL, non è necessario conoscere alcun nuovo linguaggio. Offre anche funzionalità di esplorazione tramite selezione e clic per gli utenti non esperti.

I clienti possono sfruttare la velocità per diagnosticare rapidamente i problemi relativi agli asset. Possono eseguire l'analisi del DevOps per ottenere la causa radice di un bug in una soluzione Internet delle cose. Consentono inoltre di identificare le aree da contrassegnare per un'analisi più approfondita come parte delle iniziative data science.

Esistono tre modi principali per interagire con i dati archiviati in Azure Time Series Insights Gen2:

* Il primo e il modo più semplice per iniziare è quello di Azure Time Series Insights Gen2 Explorer. È possibile usarlo per visualizzare rapidamente tutti i dati IoT in un'unica posizione. Fornisce strumenti come la mappa termica che consentono di individuare le anomalie nei dati. Fornisce anche una visualizzazione in prospettiva. Usarlo per confrontare fino a quattro visualizzazioni da uno o più ambienti Azure Time Series Insights Gen2 in un unico dashboard. Il dashboard offre una visualizzazione dei dati della serie temporale in tutte le posizioni. Scopri di più sul [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md). Per pianificare l'ambiente, vedere [Azure Time Series Insights pianificazione Gen2](./how-to-plan-your-environment.md).

* Il secondo modo per iniziare consiste nell'usare JavaScript SDK per incorporare rapidamente grafici e grafici avanzati nell'applicazione Web. Con poche righe di codice, è possibile creare query avanzate. Utilizzarli per popolare grafici a linee, grafici a torta, grafici a barre, mappe termiche, griglie di dati e altro ancora. Tutti questi elementi sono predefiniti se si usa l'SDK. L'SDK astrae anche Azure Time Series Insights API di query Gen2. È possibile usarle per creare predicati simili a SQL per eseguire query sui dati da visualizzare in un dashboard. Per le soluzioni ibride a livello di presentazione, Azure Time Series Insights Gen2 offre URL con parametri. Forniscono punti di connessione senza problemi con il Azure Time Series Insights Esplora Gen2 per approfondire i dati.

  * Per ulteriori informazioni su JavaScript SDK, vedere la [libreria client JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e la documentazione del [client di esempio](https://github.com/Microsoft/tsiclient) .

  * Per altre informazioni su come condividere gli URL e la nuova interfaccia utente, vedere [visualizzare i dati nella Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* Il terzo modo per iniziare consiste nell'usare le API potenti per eseguire query sui dati archiviati in Azure Time Series Insights Gen2. Azure Time Series Insights Gen2 include operatori temporali, ad esempio `from` ,, `to` `first` e `last` . Dispone di aggregazioni e trasformazioni, ad esempio `average` ,, `sum` `min` , `max` , `time-weighted average` , `time-weighted sum` e così via. Consente inoltre l'applicazione di filtri, operatori aritmetici e booleani, funzioni scalari e così via. Tutti questi operatori consentono alle applicazioni downstream di trovare rapidamente tendenze e modelli interessanti nei dati. Usarli per popolare le visualizzazioni Homegrown per individuare le anomalie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analisi operativa e miglioramento dell'efficienza dei processi

Usare Azure Time Series Insights Gen2 per monitorare l'integrità, l'utilizzo e le prestazioni delle apparecchiature su larga scala e misurare l'efficienza operativa. Azure Time Series Insights Gen2 consente di gestire carichi di lavoro Internet diversi e imprevedibili senza sacrificare l'inserimento o le prestazioni delle query.

[![Screenshot mostra I dispositivi o i dati delle applicazioni, l'elaborazione del flusso, l'efficienza operativa, le informazioni di intelligence/Insights e le analisi avanzate in Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Lo streaming e l'elaborazione continua dei dati provenienti dai processi operativi possono trasformare con successo qualsiasi azienda se combinati con la soluzione o la tecnologia più adatta. Spesso queste soluzioni sono una combinazione di più sistemi. Consentono l'esplorazione e l'analisi dei dati che cambiano costantemente, specialmente nell'area di autenticazione e condividono uno schema comune.

Questi modelli hanno spesso inizio con piattaforme abilitate per IoT, che inseriscono miliardi di eventi da dispositivi e sensori con diverse impostazioni locali. Questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni e azioni in tempo reale. I dati vengono in genere archiviati in archivio caldo e freddo per l'analisi in tempo quasi reale e batch.

I dati raccolti vengono sottoposti a una serie di processi per pulirli e contestualizzarli per gli scenari di analisi e query downstream. Azure offre servizi avanzati che possono essere applicati a scenari IoT come la produzione e la manutenzione di asset. Questi servizi includono Azure Time Series Insights Gen2, hub degli eventi, Hub eventi, analisi di flusso di Azure, funzioni di Azure, app per la logica di Azure, Azure Databricks, Azure Machine Learning e Power BI.

L'architettura della soluzione può essere ottenuta nel modo seguente:

* Inserire i dati tramite l'hub IoT o Hub eventi per sicurezza, velocità effettiva e latenza ottimali.
* Eseguire l'elaborazione dati e i calcoli. Creare un grafico a imbuto con i dati inseriti tramite servizi come Analisi di flusso, App per la logica e Funzioni di Azure. Il servizio usato dipende dalle esigenze specifiche di elaborazione dati.
* Viene eseguito il push dei segnali calcolati dalla pipeline di elaborazione a Azure Time Series Insights Gen2 per l'archiviazione e l'analisi.

Azure Time Series Insights Gen2 offre l'esplorazione dei dati quasi in tempo reale e informazioni dettagliate basate sulle risorse sui dati cronologici. A seconda delle esigenze aziendali, i processi MapReduce e hive possono essere eseguiti sui dati archiviati in Azure Time Series Insights Gen2 connettendo Azure Time Series Insights Gen2 ad Azure HDInsight. I dati archiviati in Azure Time Series Insights Gen2 sono disponibili per Power BI e altre applicazioni dei clienti tramite le API di query della superficie pubblica Azure Time Series Insights Gen2. Questi dati sono utilizzabili per scenari avanzati di intelligence operativa e di business intelligence.

## <a name="advanced-analytics"></a>Analisi avanzata

Ottenere l'integrazione con servizi di analisi avanzati come Machine Learning e Azure Databricks. Azure Time Series Insights Gen2 ingresa dati non elaborati da milioni di dispositivi. Aggiunge dati contestuali che possono essere utilizzati senza problemi da un gruppo di servizi di analisi di Azure.

[![Analisi](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

L'analisi avanzata e l'apprendimento automatico utilizzano ed elaborano grandi volumi di dati. Questi dati vengono usati per prendere decisioni basate sui dati ed eseguire analisi predittive. Nei casi d'uso delle soluzioni IoT, gli algoritmi di analisi avanzata apprendono dai dati raccolti da milioni di dispositivi. Questi dispositivi trasmettono i dati più volte al secondo. I dati raccolti dai dispositivi IoT non sono elaborati. Non includono informazioni contestuali, ad esempio la posizione del dispositivo e l'unità di misura del sensore che effettua la lettura. Di conseguenza, i dati non elaborati sono difficili da utilizzare direttamente per l'analisi avanzata.

Azure Time Series Insights Gen2 colma il gap tra i dati e le analisi avanzate in due modi semplici ed economicamente convenienti:

* In primo luogo, Azure Time Series Insights Gen2 raccoglie dati di telemetria non elaborati da milioni di dispositivi usando l'hub Internet delle cose. Arricchisce i dati con informazioni contestuali e li converte in formato Parquet. Questo formato può integrarsi facilmente con altri servizi di analisi avanzata, ad esempio Machine Learning, Azure Databricks e applicazioni di terze parti.

    Azure Time Series Insights Gen2 possono fungere da origine della verità per tutti i dati in un'organizzazione. Crea un repository centrale che può essere utilizzato dai carichi di lavoro di analisi downstream. Dal momento che Azure Time Series Insights Gen2 è un servizio di archiviazione quasi in tempo reale, i modelli di analisi avanzata possono apprendere continuamente dai dati di telemetria delle cose in arrivo. Di conseguenza, i modelli possono eseguire stime più accurate.

* In secondo luogo, l'output dei modelli di machine learning e di stima può essere inserito Azure Time Series Insights Gen2 per visualizzare e archiviare i risultati. Questa procedura consente alle organizzazioni di ottimizzare e modificare i propri modelli. Azure Time Series Insights Gen2 semplifica la visualizzazione dei dati di telemetria di streaming sullo stesso piano degli output del modello sottoposto a training. In questo modo, consente ai team di data science di individuare le anomalie e identificare gli schemi.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sul [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).
* Leggere [Azure Time Series Insights procedure consigliate di Gen2](./how-to-plan-your-environment.md) per pianificare l'ambiente.
* Vedere la documentazione del [client di esempio](https://github.com/Microsoft/tsiclient) .