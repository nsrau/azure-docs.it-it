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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730191"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilare le app di Servizio app di Azure attive con Application Insights

È possibile eseguire Profiler ASP.NET e ASP.NET Core App in esecuzione nel servizio App di Azure con livello di servizio Basic o versione successiva. L'abilitazione di Profiler in Linux è attualmente possibile solo tramite [questo metodo](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Abilitare Profiler per l'app
Per abilitare Profiler per un'app, seguire queste istruzioni. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per abilitare Profiler su altre piattaforme supportate:
* [Servizi cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler è pre-installato come parte del runtime di servizi App. La procedura seguente illustrerà come abilitarlo per il servizio App. Seguire questi passaggi anche se è stata inserita l'App Insights SDK nell'applicazione in fase di compilazione.

1. Abilitare l'impostazione "Always On" per il servizio app. È possibile aggiornare l'impostazione nella pagina di configurazione del servizio App in impostazioni generali.
1. Passare al riquadro **Servizi app** nel portale di Azure.
1. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/profiler/AppInsights-AppServices.png)

1. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che Profiler sia **attivo**. Se la risorsa di Application Insights è in una sottoscrizione diversa dal servizio App, è Impossibile utilizzare questa pagina per configurare Application Insights. È comunque possibile farlo manualmente dalla creazione di impostazioni dell'app necessarie manualmente. [La sezione successiva contiene istruzioni per l'attivazione manuale del Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

1. Profiler viene ora abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Abilitare Profiler manualmente o con Azure Resource Manager
Application Insights Profiler può essere abilitata mediante la creazione di impostazioni dell'app per il servizio App di Azure. La pagina con le opzioni illustrato in precedenza crea queste impostazioni delle app per l'utente. Ma è possibile automatizzare la creazione di queste impostazioni usando un modello o altri mezzi. Queste impostazioni funzionerà anche se la risorsa di Application Insights è in una sottoscrizione diversa dal servizio App di Azure.
Ecco le impostazioni necessarie per abilitare il profiler:

|Impostazione app    | Value    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | chiave di strumentazione per la risorsa di Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


È possibile impostare tali valori usando [Azure Resource Manager Templates](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per una singola istanza dell'app, in **Processi Web** accedere alla risorsa dell'app. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app per individuare il prima possibile eventuali problemi di prestazioni.

File del Profiler possono essere eliminati quando si usa WebDeploy per distribuire le modifiche all'applicazione web. È possibile impedire l'eliminazione escludendo la cartella App_Data dall'eliminazione durante la distribuzione. 


## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
