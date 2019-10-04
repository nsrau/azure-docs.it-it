---
title: Monitorare le prestazioni delle applicazioni ospitate in VM di Azure e set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Application Performance Monitoring per VM di Azure e set di scalabilità di macchine virtuali di Azure. Tempo di caricamento e di risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597444"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Monitorare le prestazioni delle applicazioni ospitate in VM di Azure e set di scalabilità di macchine virtuali di Azure

L'abilitazione del monitoraggio sulle applicazioni Web basate su .NET in esecuzione su [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) e sui set di scalabilità di [macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) è ora più semplice che mai. Ottenere tutti i vantaggi derivanti dall'utilizzo di Application Insights senza modificare il codice.

Questo articolo illustra come abilitare il monitoraggio Application Insights usando l'estensione ApplicationMonitoringWindows e fornire indicazioni preliminari per l'automazione del processo per le distribuzioni su larga scala.

> [!IMPORTANT]
> L'estensione ApplicationMonitoringWindows di Azure è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Abilita Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure ospitate:

* **Monitoraggio dell'applicazione basata su agenti** (Estensione ApplicationMonitoringWindows).
    * Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "Runtime". Per le VM di Azure e i set di scalabilità di macchine virtuali di Azure, è consigliabile abilitare almeno questo livello di monitoraggio. In seguito, in base allo scenario specifico, è possibile valutare se la strumentazione manuale è necessaria.
    * Attualmente sono supportate solo le applicazioni .NET ospitate in IIS.

* **Strumentazione manuale dell'applicazione tramite il codice** tramite l'installazione di Application Insights SDK.

    * Questo approccio è molto più personalizzabile, ma richiede [l'aggiunta di una dipendenza dai pacchetti NuGet di Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Questo metodo consente inoltre di gestire gli aggiornamenti alla versione più recente dei pacchetti.

    * Se è necessario eseguire chiamate API personalizzate per tenere traccia degli eventi e delle dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario usare questo metodo. Per altre informazioni, vedere l' [articolo relativo all'API per eventi personalizzati e metriche](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Se vengono rilevati sia il monitoraggio basato su agente che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. Ciò consente di impedire l'invio di dati duplicati. Per ulteriori informazioni, vedere la [sezione relativa alla risoluzione dei problemi](#troubleshooting) di seguito.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Gestire il monitoraggio basato su agenti per le applicazioni .NET in una macchina virtuale con PowerShell

Installare o aggiornare l'estensione di monitoraggio dell'applicazione per la macchina virtuale
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

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Disinstallare l'estensione di monitoraggio dell'applicazione dalla macchina virtuale
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Stato dell'estensione di monitoraggio dell'applicazione query per la macchina virtuale
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Ottiene l'elenco delle estensioni installate per la macchina virtuale
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Gestire il monitoraggio basato su agenti per le applicazioni .NET nel set di scalabilità di macchine virtuali di Azure con PowerShell

Installare o aggiornare l'estensione di monitoraggio delle applicazioni per il set di scalabilità di macchine virtuali di Azure
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

Disinstallare l'estensione di monitoraggio dell'applicazione dal set di scalabilità di macchine virtuali di Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Stato dell'estensione di monitoraggio delle query per il set di scalabilità di macchine virtuali di Azure
```powershell
# Not supported by extensions framework
```

Ottiene l'elenco delle estensioni installate per il set di scalabilità di macchine virtuali di Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Di seguito è riportata la guida dettagliata alla risoluzione dei problemi per il monitoraggio basato su estensioni per le applicazioni .NET in esecuzione in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure.

> [!NOTE]
> Le applicazioni .NET Core, Java e node. js sono supportate solo nei set di scalabilità di macchine virtuali di Azure e di macchine virtuali di Azure tramite strumentazione manuale basata su SDK, quindi i passaggi seguenti non si applicano a questi scenari.

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nelle directory seguenti:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [distribuire un'applicazione in un set di scalabilità di macchine virtuali di Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configurare i test Web di disponibilità](monitor-web-app-availability.md) per ricevere un avviso se l'endpoint è inattivo.
