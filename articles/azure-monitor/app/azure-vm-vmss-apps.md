---
title: Monitorare le prestazioni nelle macchine virtuali di Azure - Azure Application InsightsMonitor performance on Azure VMs - Azure Application Insights
description: Monitoraggio delle prestazioni delle applicazioni per le macchine virtuali di Azure e i set di scalabilità delle macchine virtuali di Azure.Application performance monitoring for Azure VM and Azure virtual machine scale sets. Grafico del tempo di caricamento e di risposta, delle informazioni sulle dipendenze e dell'impostazione di avvisi sulle prestazioni.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661329"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Distribuire l'agente di Azure Monitor Application Insights nelle macchine virtuali di Azure e nei set di scalabilità delle macchine virtuali di AzureDeploy the Azure Monitor Application Insights Agent on Azure virtual machines and Azure virtual machine scale sets

L'abilitazione del monitoraggio nelle applicazioni Web basate su .NET in esecuzione nelle [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) di Azure e nei set di [scalabilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) delle macchine virtuali di Azure è ora più semplice che mai. Ottieni tutti i vantaggi dell'uso di Application Insights senza modificare il codice.

Questo articolo illustra come abilitare il monitoraggio di Application Insights tramite l'agente Application Insights e fornisce indicazioni preliminari per l'automazione del processo per distribuzioni su larga scala.

> [!IMPORTANT]
> Azure Application Insights Agent per .NET è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Abilitare Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le macchine virtuali di Azure e i set di scalabilità delle macchine virtuali di Azure nelle applicazioni ospitate:There are two ways to enable application monitoring for Azure virtual machines and Azure virtual machine scale sets hosted applications:

* **Senza codice** tramite Application Insights AgentCodeless via Application Insights Agent
    * Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "di runtime".

    * Per le macchine virtuali di Azure e i set di scalabilità delle macchine virtuali di Azure è consigliabile abilitare almeno questo livello di monitoraggio. Successivamente, in base allo scenario specifico, è possibile valutare se è necessaria la strumentazione manuale.

    * L'agente Application Insights raccoglie automaticamente gli stessi segnali di dipendenza out-of-the-box di .NET SDK. Per altre informazioni, vedere [Raccolta automatica delle dipendenze.](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)
        > [!NOTE]
        > Attualmente sono supportate solo le applicazioni ospitate da .Net IIS. Usare un SDK per instrumentare ASP.NET applicazioni Core, Java e Node.js ospitate in macchine virtuali di Azure e set di scalabilità di macchine virtuali.

* **Basato su codice** tramite SDKCode-based via SDK

    * Questo approccio è molto più personalizzabile, ma richiede [l'aggiunta](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)di una dipendenza dai pacchetti NuGet di Application Insights SDK. Questo metodo, significa anche che è necessario gestire gli aggiornamenti alla versione più recente dei pacchetti da soli.

    * Se è necessario effettuare chiamate API personalizzate per tenere traccia di eventi/dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario utilizzare questo metodo. Per altre informazioni, consulta [l'articolo API per eventi e metriche personalizzati.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)

> [!NOTE]
> Se vengono rilevati sia il monitoraggio basato su agenti che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. In questo modo si evita l'invio di dati duplicati. Per ulteriori informazioni su questo, consulta la [sezione sulla risoluzione dei problemi](#troubleshooting) di seguito.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gestire le applicazioni Application Insights Agent for .NET nelle macchine virtuali di Azure usando PowerShellManage Application Insights Agent for .NET applications on Azure virtual machines using PowerShell

> [!NOTE]
> Prima di installare application Insights Agent, è necessaria una stringa di connessione. [Creare una nuova risorsa di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) o copiare la stringa di connessione da una risorsa di business insights esistente.

> [!NOTE]
> Sei nuovo di Powershell? Consulta la [Guida introduttiva](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Installare o aggiornare application Insights Agent come estensione per le macchine virtuali di AzureInstall or update the Application Insights Agent as an extension for Azure virtual machines
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
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
> È possibile installare o aggiornare l'agente di Application Insights come estensione in più macchine virtuali su larga scala usando un ciclo di PowerShell.You may install or update the Application Insights Agent as an extension across multiple Virtual Machines at-scale using a Powershell loop.

Disinstallare l'estensione di Application Insights Agent dalla macchina virtuale di AzureUninstall Application Insights Agent extension from Azure virtual machine
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Eseguire query sullo stato dell'estensione di Application Insights Agent per la macchina virtuale di AzureQuery Application Insights Agent extension status for Azure virtual machine
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Ottenere l'elenco delle estensioni installate per la macchina virtuale di AzureGet list of installed extensions for Azure virtual machine
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
È anche possibile visualizzare le estensioni installate nel [pannello Macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) di Azure nel portale.

> [!NOTE]
> Verificare l'installazione facendo clic su Live Metrics Stream all'interno della risorsa di Application Insights associata alla stringa di connessione utilizzata per distribuire l'estensione dell'agente di Application Insights.Verify installation by clicking on Live Metrics Stream within the Application Insights Resource associated with the connection string you used to deploy the Application Insights Agent Extension. Se si inviano dati da più macchine virtuali, selezionare le macchine virtuali di Azure di destinazione in Nome server. L'inizio del flusso dei dati potrebbe richiedere fino a un minuto.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gestire Application Insights Agent per le applicazioni .NET nei set di scalabilità delle macchine virtuali di Azure usando PowerShellManage Application Insights Agent for .NET applications on Azure virtual machine scale sets using powershell

Installare o aggiornare application Insights Agent come estensione per il set di scalabilità delle macchine virtuali di AzureInstall or update the Application Insights Agent as an extension for Azure virtual machine scale set
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Disinstallare l'estensione di monitoraggio delle applicazioni dai set di scalabilità delle macchine virtuali di AzureUninstall application monitoring extension from Azure virtual machine scale sets
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Stato dell'estensione per il monitoraggio delle applicazioni di query per i set di scalabilità delle macchine virtuali di AzureQuery application monitoring extension status for Azure virtual machine scale sets
```powershell
# Not supported by extensions framework
```

Ottenere l'elenco delle estensioni installate per i set di scalabilità delle macchine virtuali di AzureGet list of installed extensions for Azure virtual machine scale sets
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Trovare suggerimenti per la risoluzione dei problemi per l'estensione dell'agente di monitoraggio di Application Insights per le applicazioni .NET in esecuzione nelle macchine virtuali di Azure e nei set di scalabilità delle macchine virtuali.

> [!NOTE]
> Le applicazioni .NET Core, Java e Node.js sono supportate solo nelle macchine virtuali di Azure e nei set di scalabilità delle macchine virtuali di Azure tramite la strumentazione manuale basata su SDK e pertanto i passaggi seguenti non si applicano a questi scenari.

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nelle directory seguenti:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [distribuire un'applicazione in un set](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)di scalabilità di macchine virtuali di Azure.
* [Configurare i test Web](monitor-web-app-availability.md) di disponibilità per l'avviso se l'endpoint è inattivo.
