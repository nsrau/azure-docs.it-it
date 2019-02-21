---
title: Profilare le app di Servizio app di Azure attive con Application Insights | Microsoft Docs
description: Profilare le app attive in Servizio app di Azure con Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414168"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilare le app di Servizio app di Azure attive con Application Insights

Profiler è attualmente valido per le app ASP.NET e ASP.NET Core in esecuzione in Servizio app di Azure. Per usare Profiler è necessario il livello di servizio Basic o superiore. L'abilitazione di Profiler in Linux è attualmente possibile solo tramite [questo metodo](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Abilitare Profiler per l'app
Per abilitare Profiler per un'app, seguire queste istruzioni. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per abilitare Profiler su altre piattaforme supportate:
* [Servizi cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler è preinstallato come parte del runtime di Servizi app, ma è necessario attivarlo per ottenere i profili per le app di Servizio app. Una volta distribuita un'app, anche se è stato incluso l'SDK di App Insights nel codice sorgente, seguire i passaggi riportati di seguito per abilitare il profiler usato.

1. Passare al riquadro **Servizi app** nel portale di Azure.
2. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/profiler/AppInsights-AppServices.png)

3. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che Profiler sia **attivo**.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Profiler viene ora abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per una singola istanza dell'app, in **Processi Web** accedere alla risorsa dell'app. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app per individuare il prima possibile eventuali problemi di prestazioni.

Se si usa WebDeploy per distribuire le modifiche all'applicazione Web, assicurarsi di escludere la cartella App_Data dall'eliminazione durante la distribuzione. In caso contrario, i file dell'estensione Profiler verranno eliminati alla successiva distribuzione dell'applicazione Web in Azure.



## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
