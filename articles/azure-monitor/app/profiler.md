---
title: Profilare le app di Servizio app di Azure attive con Application Insights | Microsoft Docs
description: Profilare le app attive in Servizio app di Azure con Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4f5328bbe21bb5f4a7947d5a495f4a0c8759c8da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87315638"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilare le app di Servizio app di Azure attive con Application Insights

È possibile eseguire Profiler in app ASP.NET e ASP.NET Core in esecuzione in Servizio app di Azure usando il livello di servizio Basic o superiore. L'abilitazione di Profiler in Linux è attualmente possibile solo tramite [questo metodo](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Abilitare Profiler per l'app
Per abilitare Profiler per un'app, seguire queste istruzioni. Se si esegue un tipo diverso di servizi di Azure, ecco le istruzioni per abilitare Profiler in altre piattaforme supportate:
* [Servizi cloud](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Applicazioni di Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Macchine virtuali](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler è preinstallato come parte del runtime di Servizi app. La procedura seguente illustra come abilitarlo per il servizio app. Seguire questa procedura anche se App Insights SDK è stato incluso nell'applicazione in fase di compilazione.

1. Passare al pannello di controllo di Azure per il servizio app.
1. Abilitare l'impostazione "Always On" per il servizio app. È possibile trovare questa impostazione nella pagina **Impostazioni**, **configurazione** (vedere screenshot nel passaggio successivo) e fare clic sulla scheda **Impostazioni generali** .
1. Passare a **impostazioni > pagina Application Insights** .

   ![Abilitare Application Insights nel portale dei servizi app](./media/profiler/AppInsights-AppServices.png)

1. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che Profiler sia **attivo**. Se la risorsa di Application Insights si trova in una sottoscrizione diversa da Servizio app di Azure, non è possibile usare questa pagina per configurare Application Insights. È comunque possibile eseguire questa operazione manualmente creando manualmente le impostazioni dell'app necessarie. [La sezione successiva contiene le istruzioni per abilitare manualmente Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

1. Profiler viene ora abilitato tramite un'impostazione di app di Servizi app.

    ![Impostazione di app per Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Abilitare Profiler manualmente o con Azure Resource Manager
È possibile abilitare Application Insights Profiler creando impostazioni dell'app per Servizio app di Azure. Queste impostazioni dell'app vengono create automaticamente nella pagina specificando le opzioni illustrate in precedenza. È tuttavia possibile automatizzare la creazione di queste impostazioni usando un modello o altri metodi. Queste impostazioni funzioneranno anche se la risorsa di Application Insights si trova in una sottoscrizione diversa dal Servizio app di Azure.
Ecco le impostazioni necessarie per abilitare Profiler:

|Impostazione app    | valore    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey della risorsa di Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


È possibile impostare questi valori usando [modelli di Azure Resource Manager](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp), l'interfaccia della riga di comando di  [Azure](/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Abilitazione manuale di Profiler per altri cloud

Se si vuole abilitare Profiler per altri cloud, è possibile usare le impostazioni dell'app seguenti.

|Impostazione app    | Valori per US Government| Cloud per la Cina |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://agent.serviceprofiler.azure.us`    | `https://profiler.applicationinsights.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Disabilitare Profiler

Per arrestare o riavviare Profiler per un'istanza dell'app singola, in **Processi Web** arrestare il processo Web denominato ApplicationInsightsProfiler3. Anche se il Profiler è disabilitato usando l'opzione nella pagina Application Insights come descritto in precedenza, il processo del Profiler continuerà l'esecuzione. Il profiler verificherà se è abilitato. Se è disabilitato, verrà sospeso per un certo periodo di tempo prima di effettuare un nuovo controllo. Se disabilitato, non esegue alcuna profilatura. Se si disabilita questo processo Web, il processo del profiler non viene eseguito, neanche per verificare se è abilitato.

  ![Disabilitare Profiler per un processo Web][disable-profiler-webjob]

È consigliabile abilitare Profiler su tutte le app per individuare il prima possibile eventuali problemi di prestazioni.

È possibile che i file di Profiler vengano eliminati quando si usa WebDeploy per distribuire le modifiche nell'applicazione Web. Per impedirne l'eliminazione, disabilitare l'eliminazione della App_Data durante la distribuzione. 


## <a name="next-steps"></a>Passaggi successivi

* [Uso di Application Insights in Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

