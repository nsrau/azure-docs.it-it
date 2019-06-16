---
title: Panoramica della diagnostica del servizio app di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi con l'app con diagnostica del servizio App.
keywords: servizio app, servizio app di azure, diagnostica, supporto, app web, risoluzione dei problemi, self-help
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539825"
---
# <a name="azure-app-service-diagnostics-overview"></a>Panoramica della diagnostica del servizio app di Azure

Quando si esegue un'applicazione Web, si desidera essere pronti ad affrontare gli eventuali problemi che possono verificarsi, ad esempio gli errori di tipo 500 che indicano agli utenti che il sito è inattivo. Diagnostica del servizio App offre un'esperienza intelligente e interattiva per risolvere i problemi all'app senza alcuna configurazione necessaria. Quando si verificano problemi con l'app, diagnostica del servizio App fa riferimento la causa del problema ottenere istruzioni utili per le informazioni corrette in modo rapido e più semplice risolvere i problemi e risolvere il problema.

Sebbene ciò risulti particolarmente utili quando si verificano problemi con l'app nelle ultime 24 ore, tutti i grafici di diagnostica sono sempre disponibili per l'utente da analizzare.

La diagnostica del servizio app funziona non solo per le app in Windows, ma anche per le app in [Linux e contenitori](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), nell'[ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro) e di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Aprire la diagnostica del servizio app

Per accedere a diagnostica del servizio App, esplorare l'app web del servizio App o un ambiente del servizio App nel [portale di Azure](https://portal.azure.com). Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**.

Per le funzioni di Azure, passare all'app di funzione e nel riquadro di spostamento superiore, fare clic su **funzionalità della piattaforma**e selezionare **diagnosticare e risolvere i problemi** dal **gestionedellerisorse** sezione.

Nella home page diagnostica del servizio App, è possibile scegliere la categoria che meglio descrive il problema con l'app usando le parole chiave in ogni riquadro home page. Inoltre, questa pagina è dove è possibile trovare **strumenti di diagnostica** per le app di Windows. Visualizzare [strumenti di diagnostica (solo per app Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interfaccia interattiva

Dopo aver selezionato una categoria home page che si allinea meglio alla problema dell'app, interfaccia interattiva degli diagnostica del servizio App, genio, Guida l'utente attraverso la diagnosi e risoluzione dei problemi con l'app. Per visualizzare il report di diagnostico completo della categoria del problema che si desidera, è possibile utilizzare il riquadro dei collegamenti fornita dal genio. Il riquadro dei collegamenti fornisce un metodo diretto per l'accesso alle metriche di diagnostica.

![Riquadro dei collegamenti](./media/app-service-diagnostics/tile-shortcuts-2.png)

Dopo aver fatto clic su questi riquadri, è possibile visualizzare un elenco di argomenti correlati al problema descritto nella sezione. Questi argomenti forniscono frammenti di codice di informazioni significative dal report completo. È possibile fare clic su uno degli argomenti seguenti per analizzare ulteriormente i problemi. Inoltre, è possibile fare clic su **visualizzazione Report completo** per esplorare tutti gli argomenti in una singola pagina.

![Argomenti](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visualizza Report completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Report di diagnostica

Dopo aver scelto di analizzare ulteriormente il problema facendo clic su un argomento, è possibile visualizzare altri dettagli sull'argomento spesso con grafici e ad. Report di diagnostica può essere un potente strumento per individuare il problema con l'app.

![Report di diagnostica](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Controllo di integrità

Se si non sapere qual è il problema con l'app o non sai da dove partire per la risoluzione dei problemi, controllo di integrità è un buon punto di partenza. Controllo di integrità analizza le applicazioni per offrire una panoramica rapida e interattiva che fa notare che cos'è integro e qual è il problema, che indica dove cercare per analizzare il problema. L'interfaccia intelligente e interattiva fornisce tutti i passaggi necessari per eseguire il processo di risoluzione dei problemi. Controllo di integrità è integrato con l'esperienza genio per le app di Windows e app web verso il basso diagnostica report per le app Linux.

### <a name="health-checkup-graphs"></a>Grafici di controllo di integrità

Esistono quattro diversi grafici nel controllo di integrità.

- **richieste ed errori:** Un grafico che mostra il numero di richieste effettuate nelle ultime 24 ore con errori server HTTP.
- **prestazioni delle App:** Un grafico che mostra il tempo di risposta nelle ultime 24 ore per i vari gruppi di percentile.
- **Utilizzo della CPU:** Un grafico che mostra l'utilizzo della CPU complessivo percentuale per ogni istanza nelle ultime 24 ore.  
- **utilizzo della memoria:** Un grafico che mostra l'utilizzo complessivo percentuale di memoria fisica per ogni istanza nelle ultime 24 ore.

![Controllo di integrità](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Esaminare i problemi di codice dell'applicazione (solo per app Windows)

Poiché molti problemi di app sono associati a problemi nel codice dell'applicazione, la diagnostica del servizio app si integra con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) per evidenziare le eccezioni e i problemi di dipendenza da correlare con il tempo di inattività selezionato. Application Insights deve essere abilitato separatamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Per visualizzare le dipendenze e le eccezioni di Application Insights, selezionare la **app web verso il basso** oppure **app web lenta** riquadro dei collegamenti.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Risoluzione dei problemi (solo per app Windows)

Se viene rilevato un problema con una categoria di problema specifico nelle ultime 24 ore, è possibile visualizzare i report di diagnostica completo e diagnostica del servizio App può richiedere di visualizzare i consigli sulla risoluzione dei problemi più e passaggi successivi per un'esperienza più interattiva.

![Application Insights e sulla risoluzione dei problemi e procedure successive](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Strumenti di diagnostica (solo per app Windows)

Gli strumenti di diagnostica includono più avanzati strumenti di diagnostica che individuare l'applicazione help codice problemi, lentezza, le stringhe di connessione e altro ancora. e attivi strumenti che consentono di attenuare i problemi con l'utilizzo della CPU, richieste e memoria.

### <a name="proactive-cpu-monitoring"></a>Monitoraggio proattivo della CPU

Monitoraggio proattivo della CPU fornisce un modo semplice e proattivo per intraprendere un'azione quando il processo figlio o app per l'app utilizza elevata di risorse della CPU. È possibile impostare le regole di soglia della CPU per ridurre temporaneamente un problema di CPU elevato fino a trova la causa effettiva del problema imprevisto.

![Monitoraggio proattivo della CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Attiva la riparazione automatica

Come il monitoraggio proattivo della CPU, correzione automatica attiva offre un approccio semplice e proattivo alla riduzione del rischio un comportamento imprevisto dell'app. È possibile impostare regole personalizzate basate sul conteggio delle richieste, una richiesta lenta, limite di memoria e codice di stato HTTP per attivare le azioni di mitigazione dei rischi. Questo strumento è utilizzabile per ridurre temporaneamente un comportamento imprevisto fino a trova la causa effettiva del problema. Per altre informazioni su attiva la correzione automatica, visita [Announcing automatica nuova esperienza di diagnostica del servizio app di correzione del](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Attiva la riparazione automatica](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>Analisi dei cambiamenti

In un ambiente di sviluppo e veloce, a volte può risultare difficile tenere traccia di tutte le modifiche apportate all'App e consentire pinpoint solo in caso di modifica che ha causato un comportamento non integro. Analisi della modifica può aiutarti a circoscrivere sulle modifiche apportate all'App per facilitare l'esperienza di risoluzione. Analisi della modifica è incorporato nel report di diagnostica, ad esempio **blocchi applicazione** in modo che è possibile usarlo contemporaneamente ad altre metriche.

![Pagina di modifica analisi predefinita](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Visualizzazione delle differenze](./media/app-service-diagnostics/diff-view-12.png)

Deve essere attivato prima di usare la funzionalità di analisi della modifica. Per altre informazioni sull'analisi di modifica, visitare [annuncia la nuova esperienza di analisi di modifica nella diagnostica del servizio App](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).