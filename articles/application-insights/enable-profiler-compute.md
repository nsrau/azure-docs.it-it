---
title: Abilitare Application Insights Profiler per le applicazioni ospitate nelle risorse di Servizi cloud di Azure | Microsoft Docs
description: Informazioni su come configurare Application Insights Profiler in un'applicazione in esecuzione in Servizi cloud di Azure.
services: application-insights
documentationcenter: ''
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: a24695f7bbb5fb0546e27c934319a60a3418b9e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Abilitare Application Insights Profiler per macchine virtuali di Azure, Service Fabric e Servizi cloud di Azure

Questo articolo illustra come abilitare Azure Application Insights Profiler in un'applicazione ASP.NET ospitata da una risorsa di Servizi cloud di Azure.

Gli esempi in questo articolo includono il supporto per le macchine virtuali di Azure, i set di scalabilità delle macchine virtuali, Azure Service Fabric e Servizi cloud di Azure. Gli esempi si basano su modelli che supportano il modello di distribuzione [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Panoramica

L'immagine seguente mostra il funzionamento di Application Insights Profiler con le applicazioni ospitate nelle risorse di Servizi cloud di Azure. Le risorse di Servizi cloud di Azure includono le macchine virtuali, i set di scalabilità, i servizi cloud e i cluster di Service Fabric. L'immagine usa come esempio una macchina virtuale di Azure.  

  ![Diagramma che mostra il funzionamento di Application Insights Profiler con le risorse di Servizi cloud di Azure](./media/enable-profiler-compute/overview.png)

Per abilitare completamente Profiler, è necessario modificare la configurazione in tre posizioni:

* nel riquadro dell'istanza di Application Insights nel portale di Azure.
* nel codice sorgente dell'applicazione, ad esempio in un'applicazione Web ASP.NET.
* Nel codice sorgente di definizione della distribuzione nell'ambiente, ad esempio un modello di Azure Resource Manager nel file JSON.


## <a name="set-up-the-application-insights-instance"></a>Configurare l'istanza di Application Insights

1. [Creare una nuova risorsa di Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource) o selezionarne una esistente. 

2. Passare alla risorsa di Application Insights e quindi copiare la chiave di strumentazione.

   ![Posizione della chiave di strumentazione](./media/enable-profiler-compute/CopyAIKey.png)

3. Per completare la configurazione dell'istanza di Application Insights per Profiler, completare la procedura descritta in [Abilitare Profiler. Non è necessario collegare le app Web perché si tratta di passaggi specifici per la risorsa di Servizi app. Assicurarsi che Profiler sia abilitato nel riquadro **Configure Profiler** (Configura Profiler).


## <a name="set-up-the-application-source-code"></a>Configurare il codice sorgente dell'applicazione

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Applicazioni Web ASP.NET, ruoli Web di Servizi cloud di Azure o front-end Web ASP.NET di Service Fabric
Configurare l'applicazione per l'invio di dati di telemetria a un'istanza di Application Insights in ogni operazione `Request`.  

Aggiungere l'[SDK di Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) al progetto dell'applicazione. Verificare che le versioni del pacchetto NuGet siano le seguenti:  
  - Per le applicazioni ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) versione 2.3.0 o versione successiva.
  - Per le applicazioni ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o versione successiva.
  - Per altre applicazioni .NET e .NET Core, quali il servizio senza stato e il ruolo di lavoro del servizio cloud di Service Fabric: [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o versione successiva.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Ruoli di lavoro di Servizi cloud di Azure o back-end senza stato di Service Fabric
Oltre a completare il passaggio precedente, se l'applicazione *non* è un'applicazione ASP.NET o ASP.NET Core, come nel caso dei ruoli di lavoro di Servizi cloud di Azure o delle API senza stato di Service Fabric, procedere come segue:  

  1. Aggiungere il codice seguente in un punto iniziale della durata dell'applicazione:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Per altre informazioni sulla configurazione della chiave di strumentazione globale, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usare Service Fabric con Application Insights).  

  2. Aggiungere un'istruzione `StartOperation<RequestTelemetry>` **USING** intorno a ogni parte del codice che si vuole instrumentare, come nell'esempio seguente:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        La chiamata a `StartOperation<RequestTelemetry>` all'interno di un altro ambito `StartOperation<RequestTelemetry>` non è supportata. È possibile invece usare `StartOperation<DependencyTelemetry>` nell'ambito nidificato. Ad esempio:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

L'ambiente in cui Profiler e l'applicazione vengono eseguiti può essere una macchina virtuale, un set di scalabilità di macchine virtuali, un cluster di Service Fabric o un'istanza di Servizi cloud.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Macchine virtuali, set di scalabilità o Service Fabric

Per esempi completi, vedere:  
  * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Set di scalabilità di macchine virtuali](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Per configurare l'ambiente, procedere come segue:
1. Per verificare che sia in uso [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva, è sufficiente controllare che il sistema operativo distribuito sia `Windows Server 2012 R2` o versione successiva.

2. Cercare l'estensione [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) nel file del modello di distribuzione e quindi aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`. sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione di Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Per informazioni sull'aggiunta dell'estensione Diagnostica al modello di distribuzione, vedere [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Per le macchine virtuali, come alternativa alla procedura precedente basata su json, passare al portale di Azure e in **Macchine virtuali** > **Impostazioni di diagnostica** > **Sinks** > impostare l'invio di dati di diagnostica ad Application Insights su **Abilitato**, quindi selezionare un account di Application Insights o un ikey specifico.

### <a name="azure-cloud-services"></a>Servizi cloud di Azure

1. Per verificare che sia in uso [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva, è sufficiente controllare che i file *ServiceConfiguration.\*.cscfg* abbiano un valore di `osFamily` pari a "5" o maggiore.

2. Individuare il file di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* per il ruolo dell'applicazione, come illustrato di seguito:  

   ![Percorso del file di configurazione di diagnostica](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Se non si trova il file, per informazioni su come abilitare l'estensione Diagnostica nel progetto di Servizi cloud di Azure, vedere [Configurare la diagnostica per i servizi cloud e le macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

> [!NOTE]  
> Se il file *diagnostics.wadcfgx* contiene anche un atro sink di tipo `ApplicationInsights`, tutte e tre le chiavi di strumentazione seguenti devono corrispondere:  
>  * Chiave usata dall'applicazione.  
>  * Chiave usata dal sink `ApplicationInsights`.  
>  * Chiave usata dal sink `ApplicationInsightsProfiler`.  
>
> È possibile trovare il valore effettivo della chiave di strumentazione usata dal sink `ApplicationInsights` nei file *ServiceConfiguration.\*.cscfg*.  
> In seguito al rilascio di Visual Studio 15.5 Azure SDK, solo le chiavi di strumentazione usate dall'applicazione e dal sink `ApplicationInsightsProfiler` devono corrispondere.


## <a name="configure-environment-deployment-and-runtime"></a>Configurare la distribuzione dell'ambiente e il runtime

1. Distribuire la definizione di distribuzione dell'ambiente modificata.  

   Per applicare le modifiche, in genere è necessaria una distribuzione completa del modello o una pubblicazione basata su servizi cloud tramite i cmdlet di PowerShell o Visual Studio.  

   Di seguito è illustrato un approccio alternativo per le macchine virtuali esistenti che interessa solo l'estensione Diagnostica di Azure:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/platform/server.aspx), abilitare la funzionalità `IIS Http Tracing` di Windows come illustrato di seguito:  

   a. Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) oppure eseguire il comando seguente in PowerShell, come amministratore:  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se non si riesce a stabilire l'accesso remoto, è possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) per eseguire il comando seguente:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Abilitare Profiler nei server locali

L'abilitazione di Profiler in un server locale è nota anche come esecuzione di Application Insights Profiler in modalità autonoma. Non c'è alcun legame con le modifiche dell'estensione Diagnostica di Azure.

Non è previsto il supporto ufficiale di Profiler per i server locali. Se si è interessati all'uso di questo scenario, è possibile [scaricare il codice di supporto](https://github.com/ramach-msft/AIProfiler-Standalone). Microsoft *non* è responsabile di provvedere alla manutenzione del codice o di rispondere ai problemi o alle richieste di funzionalità in relazione al codice.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler.md#troubleshooting).
- Leggere altre informazioni su Profiler in [Application Insights Profiler](app-insights-profiler.md).
