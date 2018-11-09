---
title: Profilare le app Web attive in Azure con Application Insights | Microsoft Docs
description: Profilare le app Web attive in Azure con Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 9aae08aa5906f341a890ac15e30d2863109d83a2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140009"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilare le app Web di Azure attive con Application Insights

Lo strumento Profiler è attualmente applicabile alle app Web ASP.NET e ASP.NET Core in esecuzione su App Web. Per usare Profiler è necessario il livello di servizio Basic o superiore.

## <a id="installation"></a> Abilitare Profiler per App Web
Per abilitare Profiler per un'app Web, seguire le istruzioni riportate di seguito. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per abilitare Profiler su altre piattaforme supportate:
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler viene installato con l'estensione del sito Application Insights. È necessario installare l'estensione del sito e configurarlo per ottenere i profili per le app Web di Azure. Una volta distribuita un'app Web, anche se è stato incluso l'SDK di App Insights nel codice sorgente, seguire i passaggi riportati di seguito per abilitare il profiler utilizzato.

1. Passare al riquadro **Servizi app** nel portale di Azure.
1. Passare al riquadro **Impostazioni > Monitoraggio**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app Web. Accettare tutte le opzioni predefinite. L'opzione **Diagnostica a livello di codice** è attivata per impostazione predefinita e consente di abilitare Profiler.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

1. Profiler è ora installato con l'estensione del sito Application Insights e viene abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per una singola istanza di App Web, in **Processi Web** accedere alla risorsa di App Web. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app Web per individuare il prima possibile eventuali problemi di prestazioni.

Se si usa WebDeploy per distribuire le modifiche all'applicazione Web, assicurarsi di escludere la cartella App_Data dall'eliminazione durante la distribuzione. In caso contrario, i file dell'estensione Profiler verranno eliminati alla successiva distribuzione dell'applicazione Web in Azure.



## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

