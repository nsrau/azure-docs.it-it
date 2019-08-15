---
title: Panoramica della diagnostica del servizio app di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi all'app con la diagnostica del servizio app.
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
ms.openlocfilehash: 50caa666245b0401c2c584f0a357ca6bfa53230c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945742"
---
# <a name="azure-app-service-diagnostics-overview"></a>Panoramica della diagnostica del servizio app di Azure

Quando si esegue un'applicazione Web, si desidera essere pronti ad affrontare gli eventuali problemi che possono verificarsi, ad esempio gli errori di tipo 500 che indicano agli utenti che il sito è inattivo. La diagnostica del servizio app è un'esperienza interattiva e interattiva che consente di risolvere i problemi dell'app senza che sia necessaria alcuna configurazione. Quando si verificano problemi con l'app, la diagnostica del servizio app rileva gli errori che consentono di ottenere le informazioni appropriate per risolvere i problemi in modo più semplice e rapido e risolvere il problema.

Anche se questa esperienza è particolarmente utile quando si verificano problemi con l'app nelle ultime 24 ore, tutti i grafici di diagnostica sono sempre disponibili per l'analisi.

La diagnostica del servizio app funziona non solo per le app in Windows, ma anche per le app in [Linux e contenitori](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), nell'[ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro) e di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Aprire la diagnostica del servizio app

Per accedere alla diagnostica del servizio app, passare all'app Web del servizio app o ambiente del servizio app nel [portale di Azure](https://portal.azure.com). Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**.

Per funzioni di Azure, passare all'app per le funzioni e, nella parte superiore, fare clic su **funzionalità della piattaforma**e selezionare **diagnostica e Risolvi i problemi** dalla sezione **Gestione risorse** .

Nella Home page di diagnostica del servizio app è possibile scegliere la categoria che meglio descrive il problema con l'app usando le parole chiave in ogni riquadro della Home page. Inoltre, in questa pagina è possibile trovare **strumenti di diagnostica** per le app di Windows. Vedere [strumenti di diagnostica (solo per app di Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interfaccia interattiva

Dopo aver selezionato una categoria della Home page più adatta al problema dell'app, l'interfaccia interattiva di diagnostica del servizio app, Genie, può essere utile per la diagnosi e la risoluzione dei problemi con l'app. È possibile usare i collegamenti ai riquadri forniti da Genie per visualizzare il report di diagnostica completo della categoria di problemi a cui si è interessati. I collegamenti ai riquadri consentono di accedere direttamente alle metriche di diagnostica.

![Riquadro dei collegamenti](./media/app-service-diagnostics/tile-shortcuts-2.png)

Dopo aver fatto clic su questi riquadri, è possibile visualizzare un elenco di argomenti correlati al problema descritto nel riquadro. In questi argomenti vengono forniti frammenti di informazioni rilevanti dal report completo. È possibile fare clic su uno di questi argomenti per esaminare ulteriormente i problemi. È anche possibile fare clic su **Visualizza report completo** per esplorare tutti gli argomenti in una singola pagina.

![Argomenti](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visualizza report completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Report di diagnostica

Dopo aver scelto di esaminare ulteriormente il problema facendo clic su un argomento, è possibile visualizzare altri dettagli sull'argomento spesso integrato con i grafici e ad esempio file. Il report di diagnostica può essere uno strumento potente per individuare il problema con l'app.

![Report di diagnostica](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Controllo di integrità

Se non si conosce il problema dell'app o non si conosce la posizione in cui iniziare a risolvere i problemi, il controllo dell'integrità è un punto di partenza valido. Il controllo dell'integrità analizza le applicazioni per offrire una panoramica rapida e interattiva che indichi cosa è integro e qual è il problema, indicando la posizione in cui esaminare il problema. L'interfaccia intelligente e interattiva fornisce tutti i passaggi necessari per eseguire il processo di risoluzione dei problemi. Il controllo dell'integrità è integrato con l'esperienza Genie per le app di Windows e il report di diagnostica per app Web per le app Linux.

### <a name="health-checkup-graphs"></a>Grafici di controllo dell'integrità

Nel controllo dell'integrità sono presenti quattro grafici diversi.

- **richieste ed errori:** Un grafico che mostra il numero di richieste effettuate nelle ultime 24 ore insieme agli errori del server HTTP.
- **prestazioni dell'app:** Grafico che mostra il tempo di risposta nelle ultime 24 ore per i diversi gruppi percentile.
- **Utilizzo CPU:** Un grafico che mostra l'utilizzo totale della CPU per ogni istanza nelle ultime 24 ore.  
- **utilizzo memoria:** Un grafico che mostra l'utilizzo totale della memoria fisica per ogni istanza nelle ultime 24 ore.

![Controllo di integrità](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Esaminare i problemi del codice dell'applicazione (solo per l'app Windows)

Poiché molti problemi di app sono associati a problemi nel codice dell'applicazione, la diagnostica del servizio app si integra con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) per evidenziare le eccezioni e i problemi di dipendenza da correlare con il tempo di inattività selezionato. Application Insights deve essere abilitato separatamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Per visualizzare le eccezioni e le dipendenze di Application Insights, selezionare le scelte rapide per l' **app Web** o per il riquadro **lento** .

### <a name="troubleshooting-steps-only-for-windows-app"></a>Passaggi per la risoluzione dei problemi (solo per app Windows)

Se viene rilevato un problema con una categoria di problemi specifica nelle ultime 24 ore, è possibile visualizzare il report di diagnostica completo e la diagnostica del servizio app potrebbe richiedere di visualizzare altri suggerimenti per la risoluzione dei problemi e i passaggi successivi per un'esperienza più guidata.

![Application Insights e risoluzione dei problemi e passaggi successivi](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Strumenti di diagnostica (solo per app Windows)

Gli strumenti di diagnostica includono strumenti di diagnostica più avanzati che consentono di analizzare i problemi del codice dell'applicazione, la lentezza, le stringhe di connessione e altro ancora. e strumenti proattivi che consentono di attenuare i problemi relativi all'utilizzo della CPU, alle richieste e alla memoria.

### <a name="proactive-cpu-monitoring"></a>Monitoraggio proattivo della CPU

Il monitoraggio proattivo della CPU fornisce un metodo semplice e proattivo per eseguire un'azione quando l'app o il processo figlio per l'app utilizza risorse di CPU elevate. È possibile impostare regole di soglia CPU personalizzate per attenuare temporaneamente un problema di CPU elevato fino a quando non viene rilevata la causa reale del problema imprevisto.

![Monitoraggio proattivo della CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Riparazione automatica e correzione automatica proattiva

La correzione automatica è un'azione di mitigazione che è possibile eseguire quando l'app presenta un comportamento imprevisto. È possibile impostare regole personalizzate in base al numero di richieste, alla richiesta lenta, al limite di memoria e al codice di stato HTTP per attivare le azioni di mitigazione. Utilizzare lo strumento per attenuare temporaneamente un comportamento imprevisto fino a individuare la causa radice.

![riparazione automatica](./media/app-service-diagnostics/auto-healing-10.png)

Come il monitoraggio proattivo della CPU, la correzione automatica proattiva è una soluzione chiave per attenuare il comportamento imprevisto dell'app. La correzione automatica proattiva riavvia l'app quando il servizio app determina che l'app è in uno stato irreversibile. Per altre informazioni, vedere [annunciare la nuova esperienza di correzione automatica nella diagnostica del servizio app](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Strumento di navigazione e analisi delle modifiche (solo per app di Windows)

In un team di grandi dimensioni con integrazione continua e in cui l'app presenta numerose dipendenze, può essere difficile individuare la modifica specifica che causa un comportamento non integro. Lo strumento di spostamento consente di ottenere visibilità sulla topologia dell'app eseguendo automaticamente il rendering di una mappa delle dipendenze dell'app e di tutte le risorse nella stessa sottoscrizione. Navigator consente di visualizzare un elenco consolidato delle modifiche apportate dall'app e delle relative dipendenze e di ridurle in base a una modifica che causa un comportamento non integro. È possibile accedervi tramite il navigatore affiancato della Home page e deve essere abilitato prima di usarlo per la prima volta. Per altre informazioni, vedere [ottenere visibilità sulle dipendenze dell'app con lo strumento di navigazione](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Pagina predefinita dello strumento di navigazione](./media/app-service-diagnostics/navigator-default-page-11.png)

![Visualizzazione diff](./media/app-service-diagnostics/diff-view-12.png)

L'analisi delle modifiche per le modifiche alle app è accessibile tramite collegamenti a sezioni, **modifiche dell'applicazione** e **arresti anomali dell'applicazione** in **disponibilità e prestazioni** , in modo da poterli usare simultaneamente con altre metriche. Prima di usare la funzionalità, è prima necessario abilitarla. Per altre informazioni, vedere [annunciare la nuova esperienza di analisi delle modifiche nella diagnostica del servizio app](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Pubblicare domande o commenti e suggerimenti in [UserVoice](https://feedback.azure.com/forums/169385-web-apps) aggiungendo "[diag]" nel titolo.
