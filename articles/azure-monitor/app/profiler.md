---
title: Profilare le app di Servizio app di Azure attive con Application Insights | Microsoft Docs
description: Profilare le app attive in Servizio app di Azure con Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d463732fc8e8f488851a57fe520f138b101eb6cf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899952"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilare le app di Servizio app di Azure attive con Application Insights

È possibile eseguire Profiler in ASP.NET e ASP.NET Core app in esecuzione nel servizio app Azure usando il livello di servizio Basic o superiore. L'abilitazione di Profiler in Linux è attualmente possibile solo tramite [questo metodo](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Abilitare Profiler per l'app
Per abilitare Profiler per un'app, seguire queste istruzioni. Se si esegue un tipo diverso di servizio di Azure, di seguito sono riportate le istruzioni per abilitare Profiler su altre piattaforme supportate:
* [Servizi cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler è preinstallato come parte del runtime di servizi app. La procedura seguente illustra come abilitarla per il servizio app. Seguire questa procedura anche se è stato incluso App Insights SDK nell'applicazione in fase di compilazione.

1. Abilitare l'impostazione "Always On" per il servizio app. È possibile aggiornare l'impostazione nella pagina Configurazione del servizio app in impostazioni generali.
1. Passare al riquadro **Servizi app** nel portale di Azure.
1. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/profiler/AppInsights-AppServices.png)

1. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che Profiler sia **attivo**. Se la risorsa Application Insights si trova in una sottoscrizione diversa del servizio app, non è possibile usare questa pagina per configurare Application Insights. È comunque possibile eseguire questa operazione manualmente creando manualmente le impostazioni dell'app necessarie. [La sezione successiva contiene le istruzioni per abilitare manualmente Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

1. Profiler viene ora abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Abilitare Profiler manualmente o con Azure Resource Manager
Application Insights Profiler possibile abilitare la creazione di impostazioni dell'app per il servizio app Azure. La pagina con le opzioni illustrate in precedenza crea automaticamente le impostazioni dell'app. Tuttavia, è possibile automatizzare la creazione di queste impostazioni usando un modello o altri metodi. Queste impostazioni funzioneranno anche se la risorsa Application Insights si trova in una sottoscrizione diversa dal servizio app Azure.
Di seguito sono riportate le impostazioni necessarie per abilitare il profiler:

|Impostazione app    | Value    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey per la risorsa Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


È possibile impostare questi valori usando [modelli di Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)e l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Abilitazione manuale di Profiler per altri cloud

Se si vuole abilitare il profiler per altri cloud, è possibile usare le impostazioni dell'app seguenti.

|Impostazione app    | Valori del governo degli Stati Uniti| Cloud Cina |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per una singola istanza dell'app, in **Processi Web** accedere alla risorsa dell'app. Per eliminare Profiler, passare a **Estensioni**.

![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app per individuare il prima possibile eventuali problemi di prestazioni.

I file del profiler possono essere eliminati quando si usa WebDeploy per distribuire le modifiche nell'applicazione Web. È possibile evitare l'eliminazione escludendo la cartella App_Data da eliminare durante la distribuzione. 


## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
