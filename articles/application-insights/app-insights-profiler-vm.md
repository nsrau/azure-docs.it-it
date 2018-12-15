---
title: Profilare le app Web attive in esecuzione in una macchina virtuale di Azure con Application Insights Profiler | Microsoft Docs
description: Profilare le app Web attive in una macchina virtuale di Azure con Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b98c75c1fb42c6eec2473dee1d6661d86138bd7a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725676"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profilare le app Web in esecuzione in una macchina virtuale di Azure o un set di scalabilità di macchine virtuali con Application Insights Profiler
È anche possibile distribuire Application Insights Profiler in questi servizi:
* [App Web di Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Distribuire Profiler in una macchina virtuale o un set di scalabilità
In questa pagina sono illustrati tutti i passaggi necessari per l'esecuzione di Application Insights Profiler nella macchina virtuale di Azure o nel set di scalabilità di macchine virtuali. Application Insights Profiler viene installato con l'estensione Diagnostica di Microsoft Azure per macchine virtuali. L'estensione deve essere configurata per eseguire il profiler e App Insights SDK deve essere integrato nell'applicazione.

1. Aggiungere l'SDK di Application Insights SDK all'[applicazione ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) o all'[applicazione .NET](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) normale. Se è necessario inviare la telemetria delle richieste ad Application Insights, vedere i profili per le richieste.
1. Installare l'estensione di Diagnostica di Microsoft Azure sulla macchina virtuale. Per gli esempi di modello di Resource Manager, vedere:  
    * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Set di scalabilità di macchine virtuali](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    La parte principale è ApplicationInsightsProfilerSink in WadCfg. Aggiungere un altro sink a questa sezione per indicare a Diagnostica di Microsoft Azure di abilitare il profiler per l'invio dei dati alla chiave di strumentazione.
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

   I comandi PowerShell seguenti rappresentano un approccio alternativo per le macchine virtuali esistenti che interessa solo l'estensione Diagnostica di Azure. È sufficiente aggiungere ProfilerSink come indicato in precedenza alla configurazione restituita dal comando Get-AzureRmVMDiagnosticsExtension. Passare quindi la configurazione aggiornata al comando Set-AzureRmVMDiagnosticsExcension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/downloads/platform.aspx), abilitare la funzionalità di Windows `IIS Http Tracing`.

   a. Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) oppure eseguire il comando seguente in PowerShell, come amministratore:  

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

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
