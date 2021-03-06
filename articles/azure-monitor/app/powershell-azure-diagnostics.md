---
title: Uso di PowerShell per la configurazione di Application Insights in Azure | Microsoft Docs
description: Automatizzare la configurazione di Diagnostica di Azure per inviare i dati al Application Insights.
ms.topic: conceptual
ms.date: 08/06/2019
ms.openlocfilehash: 0fd69b90ce6329041f96b8e3173f1f17270f68ee
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699730"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Uso di PowerShell per configurare Application Insights per i servizi cloud di Azure

[Microsoft Azure](https://azure.com) può essere [configurato per l'invio dei dati del servizio Diagnostica di Azure](../platform/diagnostics-extension-to-application-insights.md) ad [Azure Application Insights](./app-insights-overview.md). I dati della diagnostica sono correlati ai Servizi cloud di Azure e alle macchine virtuali di Azure. Completano i dati di telemetria inviati dall'interno dell'app con Application Insights SDK. Nell'ambito dell'automatizzazione del processo di creazione di nuove risorse in Azure, è possibile configurare la diagnostica usando PowerShell.

## <a name="azure-template"></a>Modello di Azure
Se l'app Web è in Azure e si creano le risorse usando un modello di Azure Resource Manager, è possibile configurare Application Insights aggiungendo quanto segue al nodo risorse:

```json
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
``` 

* `nameOfAIAppResource` : nome della risorsa di Application Insights.
* `myWebAppName`: ID dell'app Web

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Abilitare l'estensione delle funzionalità di diagnostica come parte della distribuzione di un servizio Cloud
Il cmdlet `New-AzureDeployment` ha un parametro `ExtensionConfiguration`, che accetta una matrice di configurazioni di diagnostica. Queste possono essere create con il cmdlet `New-AzureServiceDiagnosticsExtensionConfig` . Ad esempio:

```azurepowershell
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$diagnostics_storagename = "myservicediagnostics"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$primary_storagekey = (Get-AzStorageKey `
  -StorageAccountName "$diagnostics_storagename").Primary
$storage_context = New-AzStorageContext `
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

```azurepowershell
$service_name = "MyService"
$diagnostics_storagename = "myservicediagnostics"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
$primary_storagekey = (Get-AzStorageKey `
  -StorageAccountName "$diagnostics_storagename").Primary
$storage_context = New-AzStorageContext `
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

```azurepowershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Rimuovere l'estensione della diagnostica

```azurepowershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se è stata abilitata l'estensione della diagnostica usando `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` senza il parametro role, è possibile rimuovere l'estensione usando `Remove-AzureServiceDiagnosticsExtension` senza il parametro role. Se il parametro role è stato usato al momento di abilitare l'estensione, deve essere usato anche quando si rimuove l'estensione.

Per rimuovere l'estensione della diagnostica da ogni singolo ruolo:

```azurepowershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Vedere anche
* [Monitorare le app dei Servizi cloud di Azure con Application Insights](./cloudservices.md)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](../platform/diagnostics-extension-to-application-insights.md)


