---
title: Strumento di diagnostica e risoluzione
description: Informazioni su come risolvere i problemi relativi all'app nel servizio app di Azure con lo strumento di diagnostica e risoluzione nel portale di Azure.Learn how you can troubleshoot issues with your app in Azure App Service with the diagnostics and solve tool in the Azure portal.
keywords: servizio app, servizio app di azure, diagnostica, supporto, app web, risoluzione dei problemi, self-help
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869937"
---
# <a name="azure-app-service-diagnostics-overview"></a>Panoramica della diagnostica del servizio app di Azure

Quando si esegue un'applicazione Web, si desidera essere pronti ad affrontare gli eventuali problemi che possono verificarsi, ad esempio gli errori di tipo 500 che indicano agli utenti che il sito è inattivo. La diagnostica del servizio app è un'esperienza intelligente e interattiva che consente di risolvere i problemi dell'app senza alcuna configurazione richiesta. Quando si verificano problemi con l'app, la diagnostica del servizio app indica cosa è sbagliato per guidare l'utente alle informazioni corrette per risolvere e risolvere il problema in modo più semplice e rapido.

Anche se questa esperienza è particolarmente utile quando si verificano problemi con l'app nelle ultime 24 ore, tutti i grafici di diagnostica sono sempre disponibili per l'analisi.

La diagnostica del servizio app funziona non solo per le app in Windows, ma anche per le app in [Linux e contenitori](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), nell'[ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro) e di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Aprire la diagnostica del servizio app

Per accedere alla diagnostica del servizio app, passare all'app Web del servizio app o all'ambiente del servizio app nel portale di [Azure.](https://portal.azure.com) Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**.

Per Funzioni di Azure passare all'app per le funzioni e nella barra di spostamento superiore fare clic su **Funzionalità della piattaforma**e selezionare Diagnostica e risolvi **problemi** nella sezione **Gestione risorse.**

Nella home page di diagnostica del servizio app è possibile scegliere la categoria che meglio descrive il problema con l'app usando le parole chiave in ogni riquadro della home page. Inoltre, questa pagina è dove è possibile trovare Strumenti di **diagnostica** per le app di Windows. Vedere [Strumenti di diagnostica (solo per l'app Windows).](#diagnostic-tools-only-for-windows-app)

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Se l'app è inattivo o ha prestazioni lente, puoi raccogliere una traccia di [profilatura](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) per identificare la causa principale del problema. La profilazione è leggera ed è progettata per scenari di produzione.
>

## <a name="interactive-interface"></a>Interfaccia interattiva

Dopo aver selezionato una categoria di home page che meglio si allinea con il problema dell'app, l'interfaccia interattiva di App Service Diagnostics, Genie, può guidarti attraverso la diagnosi e la risoluzione dei problemi con l'app. È possibile utilizzare i tasti di scelta rapida dei riquadri forniti da Genie per visualizzare il rapporto diagnostico completo della categoria di problemi che si è interessati. I collegamenti ai riquadri offrono un modo diretto per accedere alle metriche di diagnostica.

![Riquadro dei collegamenti](./media/app-service-diagnostics/tile-shortcuts-2.png)

Dopo aver fatto clic su questi riquadri, è possibile visualizzare un elenco di argomenti correlati al problema descritto nel riquadro. Questi argomenti forniscono frammenti di informazioni importanti dal report completo. È possibile fare clic su uno di questi argomenti per analizzare ulteriormente i problemi. Inoltre, è possibile fare clic su **Visualizza rapporto completo** per esplorare tutti gli argomenti in una singola pagina.

![Argomenti](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visualizza rapporto completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Report di diagnostica

Dopo aver scelto di analizzare ulteriormente il problema facendo clic su un argomento, è possibile visualizzare ulteriori dettagli sull'argomento spesso integrato con grafici e markdown. Il rapporto di diagnostica può essere un potente strumento per individuare il problema con l'app.

![Report di diagnostica](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Controllo di integrità

Se non sai cosa c'è che non va nella tua app o non sai dove iniziare a risolvere i problemi, il controllo dell'integrità è un buon punto di partenza. Il controllo dell'integrità analizza le applicazioni per offrire una panoramica rapida e interattiva che indica cosa è integro e cosa è sbagliato, indicando dove cercare di analizzare il problema. L'interfaccia intelligente e interattiva fornisce tutti i passaggi necessari per eseguire il processo di risoluzione dei problemi. Il controllo dell'integrità è integrato con l'esperienza Genie per le app Windows e il report di diagnostica per le app Linux.

### <a name="health-checkup-graphs"></a>Grafici di controllo dello stato

Ci sono quattro diversi grafici nel controllo dello stato.

- **richieste ed errori:** Grafico che mostra il numero di richieste effettuate nelle ultime 24 ore insieme agli errori del server HTTP.
- **prestazioni dell'app:** Grafico che mostra il tempo di risposta nelle ultime 24 ore per vari gruppi percentili.
- **Utilizzo CPU:** Grafico che mostra la percentuale complessiva di utilizzo della CPU per istanza nelle ultime 24 ore.  
- **utilizzo della memoria:** Grafico che mostra la percentuale di utilizzo percentuale di memoria fisica per istanza nelle ultime 24 ore.

![Controllo di integrità](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Esaminare i problemi relativi al codice dell'applicazione (solo per l'app Windows)Investigate application code issues (only for Windows app)

Poiché molti problemi di app sono associati a problemi nel codice dell'applicazione, la diagnostica del servizio app si integra con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) per evidenziare le eccezioni e i problemi di dipendenza da correlare con il tempo di inattività selezionato. Application Insights deve essere abilitato separatamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Per visualizzare le eccezioni e le dipendenze di Application Insights, selezionare i collegamenti ai riquadri lenti **dell'app Web** o **dell'app Web.**

### <a name="troubleshooting-steps-only-for-windows-app"></a>Passaggi per la risoluzione dei problemi (solo per l'app Windows)

Se viene rilevato un problema con una categoria di problemi specifica nelle ultime 24 ore, è possibile visualizzare il report di diagnostica completo e la diagnostica del servizio app potrebbe richiedere di visualizzare ulteriori consigli per la risoluzione dei problemi e i passaggi successivi per un'esperienza più guidata.

![Application Insights e risoluzione dei problemi e passaggi successivi](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Strumenti di diagnostica (solo per app di Windows)

Gli strumenti di diagnostica includono strumenti di diagnostica più avanzati che consentono di analizzare i problemi di codice dell'applicazione, lentezza, stringhe di connessione e altro ancora. e strumenti proattivi che consentono di ridurre i problemi relativi all'utilizzo della CPU, alle richieste e alla memoria.

### <a name="proactive-cpu-monitoring"></a>Monitoraggio proattivo della CPU

Il monitoraggio proattivo della CPU offre un modo semplice e proattivo per eseguire un'azione quando l'app o il processo figlio per l'app utilizza risorse di CPU elevate. È possibile impostare le proprie regole di soglia della CPU per ridurre temporaneamente un problema elevato della CPU fino a quando non viene rilevata la vera causa del problema imprevisto. Per ulteriori informazioni, vedere [Ridurre i problemi della CPU prima che si verifichino](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Monitoraggio proattivo della CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Correzione automatica e correzione automatica proattiva

La correzione automatica è un'azione di attenuazione che puoi eseguire quando l'app ha un comportamento imprevisto. È possibile impostare regole personalizzate in base al numero di richieste, alla richiesta lenta, al limite di memoria e al codice di stato HTTP per attivare le azioni di attenuazione. Utilizzare lo strumento per attenuare temporaneamente un comportamento imprevisto fino a individuare la causa principale. Per altre informazioni, vedere Annuncio della nuova esperienza di correzione automatica nella diagnostica del [servizio app.](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)

![Correzione automatica](./media/app-service-diagnostics/auto-healing-10.png)

Come il monitoraggio proattivo della CPU, la correzione automatica proattiva è una soluzione chiavi in funzione per mitigare il comportamento imprevisto dell'app. La correzione automatica proattiva riavvia l'app quando il servizio app determina che l'app è in uno stato irreversibile. Per ulteriori informazioni, vedere [Introduzione alla correzione automatica proattiva](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigatore e analisi delle modifiche (solo per l'app Windows)

In un team di grandi dimensioni con integrazione continua e in cui l'app ha molte dipendenze, può essere difficile individuare la modifica specifica che causa un comportamento non integro. Navigator consente di ottenere visibilità sulla topologia dell'app eseguendo automaticamente il rendering di una mappa delle dipendenze dell'app e di tutte le risorse nella stessa sottoscrizione. Navigator consente di visualizzare un elenco consolidato delle modifiche apportate dall'app e dalle relative dipendenze e di restringere una modifica che causa un comportamento non integro. È possibile accedervi tramite il riquadro della home page **Navigator** e deve essere abilitato prima di utilizzarlo la prima volta. Per altre informazioni, vedere [Ottenere visibilità nelle dipendenze dell'app con Navigator.](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)

![Pagina predefinita del navigatore](./media/app-service-diagnostics/navigator-default-page-11.png)

![Visualizzazione Diff](./media/app-service-diagnostics/diff-view-12.png)

È possibile accedere all'analisi delle modifiche delle modifiche alle app tramite collegamenti ai riquadri, **modifiche alle** applicazioni e **arresti anomali delle** applicazioni in Disponibilità e **prestazioni,** in modo da poterla usare contemporaneamente ad altre metriche. Prima di utilizzare la funzionalità, è necessario attivarla. Per altre informazioni, vedere Annuncio della nuova esperienza di analisi delle [modifiche in Diagnostica servizio app](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Pubblicare le domande o il feedback su [UserVoice](https://feedback.azure.com/forums/169385-web-apps) aggiungendo "[Diag]" nel titolo.
