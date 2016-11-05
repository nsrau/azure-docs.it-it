---
title: Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights | Microsoft Docs
description: Configurazione automatizzata del servizio Diagnostica di Azure per l'inoltro tramite pipe ad Application Insights.
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2015
ms.author: awills

---
# Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights
[Microsoft Azure](https://azure.com) può essere [configurato per l'invio dei dati del servizio Diagnostica di Azure](app-insights-azure-diagnostics.md) a [Visual Studio Application Insights](app-insights-overview.md). I dati della diagnostica sono correlati ai Servizi cloud di Azure e alle macchine virtuali di Azure. Completano i dati di telemetria inviati dall'interno dell'app con Application Insights SDK. Nell'ambito dell'automatizzazione del processo di creazione di nuove risorse in Azure, è possibile configurare la diagnostica usando PowerShell.

## Abilitare l'estensione delle funzionalità di diagnostica come parte della distribuzione di un servizio Cloud
Il cmdlet `New-AzureDeployment` ha un parametro `ExtensionConfiguration`, che accetta una matrice di configurazioni di diagnostica. Queste possono essere create con il cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. ad esempio:

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

## Abilitare l'estensione della diagnostica in un servizio Cloud esistente
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

## Ottenere la configurazione di estensione della diagnostica corrente
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Rimuovere l'estensione della diagnostica
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se è stata abilitata l'estensione della diagnostica usando `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` senza il parametro role, è possibile rimuovere l'estensione usando `Remove-AzureServiceDiagnosticsExtension` senza il parametro role. Se il parametro role è stato usato al momento di abilitare l'estensione, deve essere usato anche quando si rimuove l'estensione.

Per rimuovere l'estensione della diagnostica da ogni singolo ruolo:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Vedere anche
* [Monitorare le app dei Servizi cloud di Azure con Application Insights](app-insights-cloudservices.md)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-azure-diagnostics.md)
* [Automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0128_2016-->