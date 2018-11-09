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
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 10cd05bd40262815e3b27c861982debc18e5b4f3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142785"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profilare le app Web in esecuzione in una macchina virtuale di Azure o un set di scalabilità di macchine virtuali con Application Insights Profiler
È anche possibile distribuire Application Insights Profiler in questi servizi:
* [App Web di Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Distribuire Profiler in una macchina virtuale o un set di scalabilità
In questa pagina sono illustrati tutti i passaggi necessari per l'esecuzione di Application Insights Profiler nella macchina virtuale di Azure o nel set di scalabilità di macchine virtuali. Application Insights Profiler viene installato con l'estensione Diagnostica di Microsoft Azure per macchine virtuali. È necessario configurare l'estensione per eseguire il profiler e incorporare l'SDK di App Insights nell'applicazione per ottenere profili per le app Web in esecuzione in una macchina virtuale.

1. Aggiungere l'SDK di Application Insights SDK all'[applicazione ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) o all'[applicazione .NET](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) normale. Se è necessario inviare la telemetria delle richieste ad Application Insights, vedere i profili per le richieste.
1. Installare l'estensione di Diagnostica di Microsoft Azure sulla macchina virtuale. Per gli esempi di modello di Resource Manager, vedere:  
    * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Set di scalabilità di macchine virtuali](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. Distribuire la definizione di distribuzione dell'ambiente modificata.  

   Per applicare le modifiche, in genere è necessaria una distribuzione completa del modello o una pubblicazione basata su servizi cloud tramite i cmdlet di PowerShell o Visual Studio.  

   I comandi Powershell seguenti costituiscono un approccio alternativo per le macchine virtuali esistenti che interessa solo l'estensione Diagnostica di Azure:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/downloads/platform.aspx), abilitare la funzionalità `IIS Http Tracing` di Windows come illustrato di seguito:  

   a. Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) oppure eseguire il comando seguente in PowerShell, come amministratore:  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se non si riesce a stabilire l'accesso remoto, è possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) per eseguire il comando seguente:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuire l'applicazione.

## <a name="enable-profiler-on-on-premises-servers"></a>Abilitare Profiler nei server locali

L'abilitazione di Profiler in un server locale è nota anche come esecuzione di Application Insights Profiler in modalità autonoma. Non c'è alcun legame con le modifiche dell'estensione Diagnostica di Azure.

Non è previsto il supporto ufficiale di Profiler per i server locali. Se si è interessati all'uso di questo scenario, è possibile [scaricare il codice di supporto](https://github.com/ramach-msft/AIProfiler-Standalone). Microsoft *non* è responsabile di provvedere alla manutenzione del codice o di rispondere ai problemi o alle richieste di funzionalità in relazione al codice.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
