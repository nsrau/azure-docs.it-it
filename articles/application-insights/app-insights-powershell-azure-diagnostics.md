---
title: Uso di PowerShell per la configurazione di Application Insights in Azure | Microsoft Docs
description: Configurazione automatizzata del servizio Diagnostica di Azure per l'inoltro tramite pipe ad Application Insights.
services: application-insights
documentationcenter: .net
author: sbtron
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: bwren
ms.openlocfilehash: 3b6da89cc33cda713b483a2af3cbb493a03d6bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Uso di PowerShell per la configurazione di Application Insights per un'app Web di Azure
[Microsoft Azure](https://azure.com) può essere [configurato per l'invio dei dati del servizio Diagnostica di Azure](app-insights-azure-diagnostics.md) ad [Azure Application Insights](app-insights-overview.md). I dati della diagnostica sono correlati ai Servizi cloud di Azure e alle macchine virtuali di Azure. Completano i dati di telemetria inviati dall'interno dell'app con Application Insights SDK. Nell'ambito dell'automatizzazione del processo di creazione di nuove risorse in Azure, è possibile configurare la diagnostica usando PowerShell.

## <a name="azure-template"></a>Modello di Azure
Se l'app Web è in Azure e si creano le risorse usando un modello di Azure Resource Manager, è possibile configurare Application Insights aggiungendo quanto segue al nodo risorse:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` : nome della risorsa di Application Insights.
* `myWebAppName` : ID dell'app Web.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Abilitare l'estensione delle funzionalità di diagnostica come parte della distribuzione di un servizio Cloud
Il cmdlet `New-AzureDeployment` ha un parametro `ExtensionConfiguration`, che accetta una matrice di configurazioni di diagnostica. Queste possono essere create con il cmdlet `New-AzureServiceDiagnosticsExtensionConfig` . ad esempio:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Abilitare l'estensione della diagnostica in un servizio Cloud esistente
In un servizio esistente, usare `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Ottenere la configurazione di estensione della diagnostica corrente
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Rimuovere l'estensione della diagnostica
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se è stata abilitata l'estensione della diagnostica usando `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` senza il parametro role, è possibile rimuovere l'estensione usando `Remove-AzureServiceDiagnosticsExtension` senza il parametro role. Se il parametro role è stato usato al momento di abilitare l'estensione, deve essere usato anche quando si rimuove l'estensione.

Per rimuovere l'estensione della diagnostica da ogni singolo ruolo:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Vedere anche
* [Monitorare le app dei Servizi cloud di Azure con Application Insights](app-insights-cloudservices.md)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-azure-diagnostics.md)
* [Automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)

