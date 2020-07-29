---
title: Abilitare Snapshot Debugger per le app .NET nel servizio app Azure | Microsoft Docs
description: Abilitare Snapshot Debugger per le app .NET nel servizio app Azure
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6928da704236c4bb5492f99a4a5327bf297a323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676844"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Abilitare Snapshot Debugger per le app .NET nel servizio app Azure

Snapshot Debugger attualmente funziona per le app ASP.NET e ASP.NET Core in esecuzione nel servizio app Azure nei piani di servizio Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Abilita Snapshot Debugger
Per abilitare Snapshot Debugger per un'app, seguire le istruzioni riportate di seguito. Se si esegue un tipo diverso di servizio di Azure, di seguito sono riportate le istruzioni per abilitare Snapshot Debugger su altre piattaforme supportate:
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi Service Fabric di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Computer fisici o virtuali locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se si usa una versione di anteprima di .NET Core, seguire le istruzioni per [abilitare snapshot debugger per gli altri ambienti](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) per includere prima di tutto il pacchetto NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) con l'applicazione e quindi completare le altre istruzioni riportate di seguito. 

Application Insights Snapshot Debugger è preinstallato come parte del runtime dei servizi app, ma è necessario attivarlo per ottenere gli snapshot per l'app del servizio app. Dopo aver distribuito un'app, anche se è stata inclusa la Application Insights SDK nel codice sorgente, attenersi alla procedura seguente per abilitare snapshot debugger.

1. Passare al pannello di controllo di Azure per il servizio app.
2. Passare alla pagina **impostazioni > Application Insights** .

   ![Abilitare Application Insights nel portale dei servizi app](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Seguire le istruzioni nella pagina per creare una nuova risorsa o selezionare una risorsa di Application Insights esistente per monitorare l'app. Assicurarsi inoltre che entrambe le opzioni per Snapshot Debugger siano **attivate**.

   ![Aggiungere l'estensione del sito Application Insights][Enablement UI]

4. Snapshot Debugger ora è abilitata usando un'impostazione dell'app servizi app.

    ![Impostazione app per Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Disabilitare Snapshot Debugger

Seguire gli stessi passaggi di **Abilitazione di snapshot debugger**, ma impostare entrambe le opzioni per snapshot debugger su **disattivato**.
Si consiglia di Snapshot Debugger abilitata in tutte le app per semplificare la diagnostica delle eccezioni delle applicazioni.

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Per un servizio di app Azure, è possibile impostare le impostazioni dell'app in un modello di Azure Resource Manager per abilitare Snapshot Debugger e Profiler. Si aggiunge una risorsa di configurazione che contiene le impostazioni dell'app come una risorsa figlio del sito Web:

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

- Generare il traffico all'applicazione che può attivare un'eccezione. Attendere da 10 a 15 minuti per l'invio degli snapshot all'istanza di Application Insights.
- Vedere [snapshot](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi di Snapshot Debugger, vedere [snapshot debugger risoluzione dei](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)problemi.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

