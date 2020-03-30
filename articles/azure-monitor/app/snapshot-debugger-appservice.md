---
title: Abilitare il debugger snapshot per le app .NET nel servizio app di Azure. Documenti Microsoft
description: Abilitare il debugger snapshot per le app .NET nel servizio app di AzureEnable Snapshot Debugger for .NET apps in Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298267"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Abilitare il debugger snapshot per le app .NET nel servizio app di AzureEnable Snapshot Debugger for .NET apps in Azure App Service

Il debugger snapshot funziona attualmente per le app ASP.NET e ASP.NET Core in esecuzione nel servizio app di Azure nei piani di servizio di Windows.Snapshot Debugger currently works for ASP.NET and ASP.NET Core apps that are running on Azure App Service on Windows service plans.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Abilita debugger snapshot
Per abilitare Snapshot Debugger per un'app, seguire le istruzioni riportate di seguito. Se si esegue un tipo diverso di servizio di Azure, ecco le istruzioni per abilitare Il debugger snapshot in altre piattaforme supportate:If you are running a different type of Azure service, here are instructions for enabling Snapshot Debugger on other supported platforms:
* [Servizi cloud di AzureAzure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi di Azure Service FabricAzure Service Fabric services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali o fisiche locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se si utilizza una versione di anteprima di .NET Core, seguire le istruzioni per [Abilitare il debugger snapshot per altri ambienti](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) prima di includere il pacchetto [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet con l'applicazione e quindi completare le altre istruzioni riportate di seguito. 

Il debugger snapshot di Application Insights è preinstallato come parte del runtime di Servizi app, ma è necessario attivarlo per ottenere snapshot per l'app del servizio app. Dopo aver distribuito un'app, anche se Application Insights SDK è stato incluso nel codice sorgente, seguire i passaggi seguenti per abilitare il debugger snapshot.

1. Passare al riquadro **Servizi app** nel portale di Azure.
2. Passare al riquadro **Impostazioni > Application Insights**.

   ![Abilitare Application Insights nel portale dei servizi app](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Seguire le istruzioni nel riquadro per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che entrambe le opzioni per Snapshot Debugger siano **attivate.**

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Il debugger snapshot è ora abilitato tramite un'impostazione dell'app di Servizi app.

    ![Impostazione dell'app per il debugger snapshot][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Disattiva debugger snapshot

Seguire la stessa procedura descritta per **Abilita debugger snapshot**, ma impostare entrambe le opzioni per Il debugger snapshot su **Disattivato**.
È consigliabile abilitare Snapshot Debugger in tutte le app per semplificare la diagnostica delle eccezioni dell'applicazione.

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Per un servizio app di Azure, è possibile configurare le impostazioni dell'app in un modello di Azure Resource Manager per abilitare il debugger snapshot e il profiler. Aggiungere una risorsa di configurazione che contiene le impostazioni dell'app come risorsa figlio del sito Web:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione che può generare un'eccezione. Attendere quindi da 10 a 15 minuti per l'invio di snapshot all'istanza di Application Insights.Then, wait 10 to 15 minutes for snapshots to be sent to the Application Insights instance.
- Vedere gli snapshot nel portale di Azure.See [snapshots](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in the Azure portal.
- Per informazioni sulla risoluzione dei problemi relativi a Snapshot Debugger, vedere [Risoluzione dei problemi relativi](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)a Snapshot Debugger .

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

