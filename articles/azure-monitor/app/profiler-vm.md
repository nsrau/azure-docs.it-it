---
title: Profilare le app Web in esecuzione in una macchina virtuale di Azure con Application Insights Profiler | Microsoft Docs
description: Profilare le app Web in esecuzione in una macchina virtuale di Azure con Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1179e55a4826450d30351be0a905efb059780b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031022"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilare le app Web in esecuzione in una macchina virtuale di Azure o un set di scalabilità di macchine virtuali usando Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È anche possibile distribuire Azure Application Insights Profiler in questi servizi:
* [servizio app di Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuire Profiler in una macchina virtuale o un set di scalabilità di macchine virtuali
Questo articolo illustra come eseguire Application Insights Profiler nella macchina virtuale di Azure o nel set di scalabilità di macchine virtuali di Azure. Profiler viene installato con l'estensione Diagnostica di Azure per le macchine virtuali. Configurare l'estensione per l'esecuzione di Profiler e compilare Application Insights SDK nell'applicazione.

1. Aggiungere il Application Insights SDK all' [applicazione ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

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

   I comandi di PowerShell seguenti rappresentano un approccio alternativo per le macchine virtuali esistenti che toccano solo l'estensione Diagnostica di Azure. Aggiungere il ProfilerSink indicato in precedenza alla configurazione restituita dal comando Get-AzVMDiagnosticsExtension. Passare quindi la configurazione aggiornata al comando set-AzVMDiagnosticsExtension.

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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Imposta sink Profiler con Azure Resource Explorer
Non è ancora possibile impostare il sink Application Insights Profiler dal portale. Invece di usare PowerShell come descritto in precedenza, è possibile usare Azure Resource Explorer per impostare il sink. Si noti tuttavia che se si distribuisce di nuovo la macchina virtuale, il sink andrà perso. È necessario aggiornare la configurazione usata quando si distribuisce la macchina virtuale per mantenere questa impostazione.

1. Verificare che l'estensione Windows Diagnostica di Azure sia installata visualizzando le estensioni installate per la macchina virtuale.  

    ![Controllare se l'estensione WAD è installata][wadextension]

2. Trovare l'estensione diagnostica VM per la VM. Passare a [https://resources.azure.com](https://resources.azure.com). Espandere il gruppo di risorse Microsoft. Compute virtualMachines, il nome della macchina virtuale e le estensioni.  

    ![Passare alla configurazione di WAD in Azure Resource Explorer][azureresourceexplorer]

3. Aggiungere il sink Application Insights Profiler al nodo SinksConfig in WadCfg. Se non si dispone già di una sezione SinksConfig, potrebbe essere necessario aggiungerne una. Assicurarsi di specificare il Application Insights corretto iKey nelle impostazioni. È necessario impostare la modalità esploratori su lettura/scrittura nell'angolo superiore destro e premere il pulsante blu "modifica".

    ![Aggiungi Application Insights Profiler sink][resourceexplorersinksconfig]

4. Al termine della modifica della configurazione, premere ' Put '. Se il put ha esito positivo, al centro dello schermo verrà visualizzato un segno di spunta verde.

    ![Inviare una richiesta PUT per applicare le modifiche][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>È possibile eseguire Profiler nei server locali?
Non è previsto il supporto di Application Insights Profiler per i server locali.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi di Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
