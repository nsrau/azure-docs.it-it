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
ms.openlocfilehash: ab30351bfff9c5bbf070a1e8a54a4919e4d2231a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226257"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilare le app Web in esecuzione in una macchina virtuale di Azure o un set di scalabilità di macchine virtuali usando Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È anche possibile distribuire Azure Application Insights Profiler in questi servizi:
* [Servizio app di Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuire Profiler in una macchina virtuale o un set di scalabilità di macchine virtuali
Questo articolo illustra come eseguire Application Insights Profiler nella macchina virtuale di Azure o nel set di scalabilità di macchine virtuali di Azure. Profiler viene installato con l'estensione Diagnostica di Azure per le macchine virtuali. Configurare l'estensione per l'esecuzione di Profiler e compilare Application Insights SDK nell'applicazione.

1. Aggiungere Application Insights SDK per le [applicazione ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

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

   I comandi PowerShell seguenti sono un approccio alternativo per le macchine virtuali esistenti che interessano solo l'estensione diagnostica di Azure. Aggiungere il ProfilerSink menzionati in precedenza per la configurazione che viene restituita dal comando Get-AzVMDiagnosticsExtension. Per il comando Set-AzVMDiagnosticsExtension quindi passare la configurazione aggiornata.

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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Set di Profiler Sink usando Esplora risorse di Azure
Non abbiamo ancora un modo per impostare il sink di Application Insights Profiler dal portale. Invece di usare powershell come descritto in precedenza, è possibile usare Esplora inventario risorse di Azure per impostare il sink. Ma si noti che se si distribuisce la macchina virtuale anche in questo caso, il sink andranno perso. È necessario aggiornare la configurazione che usano quando si distribuisce la macchina virtuale per mantenere questa impostazione.

1. Verificare che sia installato l'estensione diagnostica di Azure visualizzando le estensioni installate per la macchina virtuale.  

    ![Controllare se è installato l'estensione diagnostica di Microsoft AZURE][wadextension]

1. Trovare l'estensione di diagnostica della macchina virtuale per la macchina virtuale. Espandere il gruppo di risorse Microsoft. COMPUTE virtualMachines, nome della macchina virtuale e le estensioni.  

    ![Passare alla configurazione WAD in Esplora risorse di Azure][azureresourceexplorer]

1. Aggiungere il sink di Application Insights Profiler per il nodo SinksConfig WadCfg. Se non si ha già una sezione SinksConfig, si potrebbe essere necessario aggiungerne uno. Assicurarsi di specificare la chiave di strumentazione di Application Insights corretta nelle impostazioni. È necessario attivare la modalità di strumenti di esplorazione di lettura/scrittura in alto a destra e fare clic sul pulsante 'Modifica' blu.

    ![Aggiungere Application Insights Profiler Sink][resourceexplorersinksconfig]

1. Dopo aver completato la modifica la configurazione, premere 'Put'. Se il caricamento ha esito positivo, verrà visualizzato un segno di spunta verde al centro della schermata.

    ![Inviare richieste put per applicare le modifiche][resourceexplorerput]






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
