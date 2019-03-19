---
title: Profilare le app Web in esecuzione in una macchina virtuale di Azure con Application Insights Profiler | Microsoft Docs
description: Profilare le app Web in esecuzione in una macchina virtuale di Azure con Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b72966ebc73953e6a89ca1bb2fd4f7ce15f70fee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111379"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilare le app Web in esecuzione in una macchina virtuale di Azure o un set di scalabilità di macchine virtuali usando Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È anche possibile distribuire Azure Application Insights Profiler in questi servizi:
* [Servizio app di Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuire Profiler in una macchina virtuale o un set di scalabilità di macchine virtuali
Questo articolo illustra come eseguire Application Insights Profiler nella macchina virtuale di Azure o nel set di scalabilità di macchine virtuali di Azure. Profiler viene installato con l'estensione Diagnostica di Azure per le macchine virtuali. Configurare l'estensione per l'esecuzione di Profiler e compilare Application Insights SDK nell'applicazione.

1. Aggiungere Application Insights SDK nell'[applicazione ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) o nell'[applicazione .NET](windows-services.md?toc=/azure/azure-monitor/toc.json) normale.  
   Per visualizzare i profili per le richieste, è necessario inviare i dati di telemetria delle richieste ad Application Insights.

1. Installare l'estensione Diagnostica di Azure nella macchina virtuale. Per gli esempi di modello di Resource Manager, vedere:  
   * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Set di scalabilità di macchine virtuali](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     La parte principale è ApplicationInsightsProfilerSink in WadCfg. Per consentire a Diagnostica di Azure di abilitare Profiler per l'invio di dati alla chiave di strumentazione, aggiungere un altro sink in questa sezione.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Distribuire la definizione di distribuzione dell'ambiente modificata.  

   Per applicare le modifiche, in genere è necessaria una distribuzione completa del modello o una pubblicazione basata su servizi cloud tramite i cmdlet di PowerShell o Visual Studio.  

   I comandi di PowerShell seguenti rappresentano un approccio alternativo per le macchine virtuali esistenti che interessa solo l'estensione Diagnostica di Azure. Aggiungere il ProfilerSink menzionati in precedenza per la configurazione che viene restituita dal comando Get-AzVMDiagnosticsExtension e quindi passare la configurazione aggiornata al comando Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/downloads/platform.aspx), abilitare la funzionalità di Windows `IIS Http Tracing`.

   a. Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/). In alternativa, eseguire il comando seguente in PowerShell (come amministratore):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se non si riesce a stabilire l'accesso remoto, è possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) per eseguire il comando seguente:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuire l'applicazione.

## <a name="can-profiler-run-on-on-premises-servers"></a>È possibile eseguire Profiler nei server locali?
Non è previsto il supporto di Application Insights Profiler per i server locali.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi di Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
