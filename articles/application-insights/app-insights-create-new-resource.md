---
title: Creare una nuova risorsa di Application Insights | Microsoft Docs
description: Impostare il monitoraggio di Application Insights per una nuova applicazione live. Approccio basato sul Web.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 0157b796a09b5685386cd96d6969da04179279dc


---
# <a name="create-an-application-insights-resource"></a>Creare una risorsa Application Insights
Application Insights di Azure visualizza dati relativi all'applicazione in una *risorsa* di Microsoft Azure. La creazione di una nuova risorsa fa dunque parte della [configurazione di Application Insights per monitorare una nuova applicazione][start]. In molti casi questa operazione può essere eseguita automaticamente dall'IDE, ovvero la modalità consigliata in cui è disponibile. In alcuni casi è tuttavia necessario creare una risorsa manualmente.

Dopo aver creato la risorsa, si ottiene la relativa chiave di strumentazione, che consente di configurare l'SDK nell'applicazione. In tal modo, i dati di telemetria verranno inviati alla risorsa.

## <a name="sign-up-to-microsoft-azure"></a>Iscriversi a Microsoft Azure
Se non si ha ancora un [account Microsoft, è possibile ottenerne uno ora](http://live.com). (se si usano servizi come Outlook.com, OneDrive, Windows Phone o XBox Live, si ha già un account Microsoft).

È necessaria anche una sottoscrizione di [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando Windows Live ID.

In alternativa è possibile creare una nuova sottoscrizione. L'account gratuito consente di provare tutte le funzionalità in Azure. Alla scadenza del periodo di valutazione, l'utente potrebbe trovare appropriata la sottoscrizione con pagamento in base al consumo, perché non si incorre in alcun addebito per i servizi gratuiti. 

Dopo aver ottenuto una sottoscrizione, accedere ad Application Insights all'indirizzo [http://portal.azure.com](https://portal.azure.com)usando il proprio Live ID.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights
In [portal.azure.com](https://portal.azure.com)aggiungere una nuova risorsa di Application Insights:

![Fare clic su Nuovo, Application Insights](./media/app-insights-create-new-resource/01-new.png)

* Il **tipo di applicazione** influisce sul contenuto del pannello Panoramica e sulle proprietà disponibili in [Esplora metriche][metrics]. Se il tipo dell'app non è visualizzato, scegliere ASP.NET.
* **gruppo di risorse** è utile per gestire le proprietà come il controllo di accesso. Se sono già state create altre risorse di Azure, è possibile inserire questa nuova risorsa nello stesso gruppo.
* **sottoscrizione** è il proprio account di pagamento in Azure.
* Il **percorso** è la posizione in cui vengono conservati i dati.
* **Aggiungi a schermata iniziale** inserisce un riquadro di accesso rapido alla propria risorsa nella pagina iniziale di Azure. Consigliato.

Dopo aver creato l'app, verrà visualizzato un nuovo pannello che mostra i dati sulle prestazioni e l'utilizzo dell'app. 

Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, cercare il riquadro di avvio rapido dell'app nella schermata iniziale. In alternativa, fare clic su Sfoglia per cercarlo.

## <a name="copy-the-instrumentation-key"></a>Eseguire una copia della chiave di strumentazione
La chiave di strumentazione identifica la risorsa creata. Sarà necessario fornirla all'SDK.

![Fare clic su Informazioni di base, quindi sulla chiave di strumentazione e infine premere CTRL+C.](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Installare l’SDK nell'app
Installare Application Insights SDK nell'app. Questo passaggio dipende dal tipo di applicazione. 

Usare la chiave di strumentazione per configurare l'[SDK installato nell'applicazione][start].

L'SDK include i moduli standard che inviano dati di telemetria senza che occorra scrivere codice. Per rilevare le azioni degli utenti o diagnosticare i problemi in modo più dettagliato, [usare l'API][api] per inviare dati di telemetria personalizzati.

## <a name="a-namemonitorasee-telemetry-data"></a><a name="monitor"></a>Visualizzare i dati di telemetria
Chiudere il pannello di avvio rapido per tornare al pannello dell'applicazione nel portale di Azure.

Fare clic sul riquadro Cerca per vedere [Ricerca diagnostica][diagnostic], ovvero la finestra in cui verranno visualizzati i primi eventi. 

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

## <a name="creating-a-resource-automatically"></a>Creazione automatica di una risorsa
È possibile scrivere uno [script di PowerShell](app-insights-powershell-script-create-resource.md) per creare automaticamente una risorsa.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un dashboard](app-insights-dashboards.md)
* [Ricerca diagnostica](app-insights-diagnostic-search.md)
* [Esplorare le metriche](app-insights-metrics-explorer.md)
* [Scrivere query di Analisi](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md




<!--HONumber=Nov16_HO3-->


