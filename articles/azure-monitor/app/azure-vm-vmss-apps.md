---
title: Monitorare le prestazioni delle applicazioni ospitate in VM di Azure e set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Application Performance Monitoring per VM di Azure e set di scalabilità di macchine virtuali di Azure. Tempo di caricamento e di risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: 4eb515d63d746e2f1f0b96431848a8b450d09358
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678225"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Distribuire l'agente di Application Insights di monitoraggio di Azure in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure

L'abilitazione del monitoraggio sulle applicazioni Web basate su .NET in esecuzione su [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) e sui [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) è ora più semplice che mai. Ottenere tutti i vantaggi derivanti dall'utilizzo di Application Insights senza modificare il codice.

Questo articolo illustra come abilitare il monitoraggio Application Insights usando l'agente Application Insights e fornisce indicazioni preliminari per l'automazione del processo per le distribuzioni su larga scala.

> [!IMPORTANT]
> L'agente applicazione Azure Insights per .NET è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Abilitare Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure ospitate:

* Senza **codice** tramite agente Application Insights
    * Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "Runtime".

    * Per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure, è consigliabile abilitare almeno questo livello di monitoraggio. In seguito, in base allo scenario specifico, è possibile valutare se la strumentazione manuale è necessaria.

    * L'agente di Application Insights raccoglie automaticamente gli stessi segnali di dipendenza predefiniti come .NET SDK. Per altre informazioni, vedere [raccolta automatica delle dipendenze](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) .
        > [!NOTE]
        > Attualmente sono supportate solo le applicazioni .NET ospitate in IIS. Usare un SDK per instrumentare applicazioni ASP.NET Core, Java e node. js ospitate in macchine virtuali di Azure e set di scalabilità di macchine virtuali.

* **Basato sul codice** tramite SDK

    * Questo approccio è molto più personalizzabile, ma richiede [l'aggiunta di una dipendenza dai pacchetti NuGet di Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Questo metodo consente inoltre di gestire gli aggiornamenti alla versione più recente dei pacchetti.

    * Se è necessario eseguire chiamate API personalizzate per tenere traccia degli eventi e delle dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario usare questo metodo. Per altre informazioni, vedere l' [articolo relativo all'API per eventi personalizzati e metriche](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Se vengono rilevati sia il monitoraggio basato su agente che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. Ciò consente di impedire l'invio di dati duplicati. Per ulteriori informazioni, vedere la [sezione relativa alla risoluzione dei problemi](#troubleshooting) di seguito.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gestire Application Insights agente per le applicazioni .NET in macchine virtuali di Azure con PowerShell

> [!NOTE]
> Prima di installare l'agente di Application Insights, è necessaria una chiave di strumentazione. [Creare una nuova risorsa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) o copiare la chiave di strumentazione da una risorsa di Application Insights esistente.

> [!NOTE]
> Non hai familiarità con PowerShell? Vedere la [Guida introduttiva](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Installare o aggiornare l'agente di Application Insights come estensione per le macchine virtuali di Azure
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> È possibile installare o aggiornare l'agente di Application Insights come estensione in più macchine virtuali su larga scala usando un ciclo di PowerShell.

Disinstalla l'estensione dell'agente Application Insights dalla macchina virtuale di Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Stato dell'estensione agente Application Insights query per la macchina virtuale di Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Ottiene l'elenco delle estensioni installate per la macchina virtuale di Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
È anche possibile visualizzare le estensioni installate nel pannello della [macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) nel portale.

> [!NOTE]
> Verificare l'installazione facendo clic su Live Metrics Stream all'interno della risorsa Application Insights associata alla chiave di strumentazione usata per distribuire l'estensione dell'agente di Application Insights. Se si inviano dati da più macchine virtuali, selezionare le macchine virtuali di Azure di destinazione in nome server. Il flusso dei dati potrebbe richiedere fino a un minuto.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gestire Application Insights agente per le applicazioni .NET nei set di scalabilità di macchine virtuali di Azure con PowerShell

Installare o aggiornare l'agente di Application Insights come estensione per il set di scalabilità di macchine virtuali di Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Disinstallare l'estensione di monitoraggio dell'applicazione dai set di scalabilità di macchine virtuali di Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Eseguire query sullo stato dell'estensione di monitoraggio applicazioni per i set di scalabilità di macchine virtuali
```powershell
# Not supported by extensions framework
```

Ottiene l'elenco delle estensioni installate per i set di scalabilità di macchine virtuali di Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Suggerimenti per la risoluzione dei problemi relativi all'estensione Application Insights Monitoring Agent per le applicazioni .NET in esecuzione in macchine virtuali di Azure e set di scalabilità di macchine virtuali.

> [!NOTE]
> Le applicazioni .NET Core, Java e node. js sono supportate solo in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure tramite strumentazione manuale basata su SDK e pertanto i passaggi seguenti non si applicano a questi scenari.

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nelle directory seguenti:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [distribuire un'applicazione in un set di scalabilità di macchine virtuali di Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configurare i test Web di disponibilità](monitor-web-app-availability.md) per ricevere un avviso se l'endpoint è inattivo.
