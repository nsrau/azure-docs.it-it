---
title: Profilare le app di Servizio app di Azure attive con Application Insights | Microsoft Docs
description: Profilare le app attive in Servizio app di Azure con Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275776"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilare le app di Servizio app di Azure attive con Application Insights

È possibile eseguire Profiler in ASP.NET e ASP.NET app di base in esecuzione nel servizio app di Azure usando il livello di servizio Basic o versione successiva. L'abilitazione di Profiler in Linux è attualmente possibile solo tramite [questo metodo](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Abilitare Profiler per l'app
Per abilitare Profiler per un'app, seguire queste istruzioni. Se si esegue un tipo diverso di servizio di Azure, ecco le istruzioni per abilitare Profiler in altre piattaforme supportate:If you're running a different type of Azure service, here are instructions for enabling Profiler on other supported platforms:
* [Servizi cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler è preinstallato come parte del runtime di App Services. I passaggi seguenti ti mostreranno come abilitarlo per il tuo servizio app. Segui questi passaggi anche se hai incluso App Insights SDK nell'applicazione in fase di compilazione.

1. Abilitare l'impostazione "Sempre acceso" per il servizio app. È possibile aggiornare l'impostazione nella pagina Configurazione del servizio app in Impostazioni generali.
1. Passare al riquadro **Servizi app** nel portale di Azure.
1. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/profiler/AppInsights-AppServices.png)

1. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che Profiler sia **attivo**. Se la risorsa Application Insights si trova in una sottoscrizione diversa dal servizio app, non è possibile usare questa pagina per configurare Application Insights.If your Application Insights resource is in a different subscription from your App Service, you can't use this page to configure Application Insights. Puoi comunque farlo manualmente creando manualmente le impostazioni dell'app necessarie. [La sezione successiva contiene istruzioni per l'abilitazione manuale di Profiler.The next section contains instructions for manually enabling Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

1. Profiler viene ora abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Abilitare Profiler manualmente o con Azure Resource ManagerEnable Profiler manually or with Azure Resource Manager
Application Insights Profiler can be enabled by creating app settings for your Azure App Service. La pagina con le opzioni mostrate sopra crea queste impostazioni dell'app per te. Ma è possibile automatizzare la creazione di queste impostazioni utilizzando un modello o altri mezzi. Queste impostazioni funzioneranno anche se la risorsa Application Insights si trova in una sottoscrizione diversa dal servizio app di Azure.These settings will also work if your Application Insights resource is in a different subscription from your Azure App Service.
Di seguito sono riportate le impostazioni necessarie per abilitare il profiler:Here are the settings needed to enable the profiler:

|Impostazione app    | valore    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey per la risorsa di Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | 3 , 3 |


È possibile impostare questi valori usando i modelli di [Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), Azure [Powershell,](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)

### <a name="enabling-profiler-for-other-clouds-manually"></a>Abilitazione manuale di Profiler per altri cloud

Se si desidera abilitare il profiler per altri cloud, è possibile usare le impostazioni dell'app seguenti.

|Impostazione app    | Valori del governo degli Stati Uniti| Nuvola Cinese |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpointApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpointApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per una singola istanza dell'app, in **Processi Web** accedere alla risorsa dell'app. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app per individuare il prima possibile eventuali problemi di prestazioni.

I file del profiler possono essere eliminati quando si utilizza WebDeploy per distribuire le modifiche all'applicazione Web. È possibile impedire l'eliminazione escludendo che la cartella App_Data venga eliminata durante la distribuzione. 


## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
