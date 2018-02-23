---
title: Abilitare Application Insights Profiler per le applicazioni di Calcolo di Azure | Microsoft Docs
description: Informazioni su come configurare Application Insights Profiler in un'applicazione in esecuzione in Calcolo di Azure.
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: d4559007aece8850b4c2d707686effd706ec468c
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Abilitare Application Insights Profiler sulle macchine virtuali di Azure, su Service Fabric e Servizi cloud

Questo articolo illustra come abilitare Azure Application Insights Profiler in un'applicazione ASP.NET ospitata da una risorsa di Calcolo di Azure.

Gli esempi in questo articolo includono il supporto per le macchine virtuali di Azure, i set di scalabilità delle macchine virtuali, Azure Service Fabric e Servizi cloud di Azure. Gli esempi si basano su modelli che supportano il modello di distribuzione [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Panoramica

La figura seguente illustra il funzionamento di Application Insights Profiler con le risorse di calcolo di Azure. Le risorse di calcolo di Azure includono le macchine virtuali, i set di scalabilità di macchine virtuali, i servizi cloud e i cluster Service Fabric. L'immagine usa come esempio una macchina virtuale di Azure.  

  ![Panoramica](./media/enable-profiler-compute/overview.png)

Per abilitare completamente il profiler, è necessario modificare la configurazione in tre posizioni:

* Nel pannello dell'istanza di Application Insights nel portale di Azure.
* Nel codice sorgente dell'applicazione, ad esempio in un'applicazione Web ASP.NET.
* Nel codice sorgente di definizione della distribuzione nell'ambiente, ad esempio un modello di Azure Resource Manager nel file con estensione json.


## <a name="set-up-the-application-insights-instance"></a>Configurare l'istanza di Application Insights

[Creare una nuova risorsa di Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource) o selezionarne una esistente.
Passare alla risorsa di Application Insights e copiare la chiave di strumentazione.

  ![Posizione della strumentazione della chiave](./media/enable-profiler-compute/CopyAIKey.png)

Completare quindi i passaggi descritti in [Abilitare il profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler) per completare la configurazione dell'istanza di Application Insights per il profiler. Non è necessario collegare le app web perché si tratta di passaggi specifici per la risorsa di Servizi app. Assicurarsi solo che il profiler sia abilitato nel pannello *Configura* di Profiler.


## <a name="set-up-the-application-source-code"></a>Configurare il codice sorgente dell'applicazione

### <a name="aspnet-web-applications-cloud-services-web-roles-or-service-fabric-aspnet-web-frontend"></a>Applicazioni Web ASP.NET, ruoli Web di Servizi cloud o front-end Web ASP.NET di Service Fabric
L'applicazione deve essere configurata per l'invio di dati di telemetria a un'istanza di Application Insights in ogni operazione `Request`:  

Aggiungere l'[SDK di Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) al progetto dell'applicazione. Verificare che le versioni del pacchetto NuGet siano le seguenti:  
  - Per le applicazioni ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) versione 2.3.0 o versione successiva.
  - Per le applicazioni ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o versione successiva.
  - Per altre applicazioni .NET e .NET Core, quali il servizio senza stato e il ruolo di lavoro del servizio cloud di Service Fabric: [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o versione successiva.  

### <a name="cloud-services-worker-roles-or-service-fabric-stateless-backend"></a>Ruoli di lavoro di Servizi cloud o back-end senza stato di Service Fabric
Se l'applicazione *non* è ASP.NET o ASP.NET Core, come i ruoli di lavoro di Servizi cloud o le API senza stato di Service Fabric, oltre al passaggio illustrato in precedenza è necessaria la configurazione della strumentazione aggiuntiva seguente:  

  1. Aggiungere il codice seguente in un punto iniziale della durata dell'applicazione:  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  Per altre informazioni sulla configurazione della chiave di strumentazione globale, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usare Service Fabric con Application Insights).  

  2. Per ogni parte del codice che si vuole instrumentare, aggiungere un'istruzione `StartOperation<RequestTelemetry>` **USING** intorno ad essa, come nell'esempio seguente:

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

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Macchine virtuali, set di scalabilità di macchine virtuali o Service Fabric

Esempi completi:  
  * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Set di scalabilità di macchine virtuali](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Per garantire che il [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versione successiva siano in uso, è sufficiente confermare che il sistema operativo distribuito sia `Windows Server 2012 R2` o una versione successiva.

2. Individuare l'estensione [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) nel file del modello di distribuzione e aggiungere la sezione `SinksConfig` seguente come elemento figlio della proprietà `WadCfg`, sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione di Application Insights:  
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


### <a name="cloud-services"></a>Servizi cloud

1. Per accertarsi che sia in uso [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva, è sufficiente verificare che i file ServiceConfiguration.\*.cscfg abbiano un valore `osFamily` su **"5"** o versione successiva.

2. Individuare il file diagnostics.wadcfgx di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) per il ruolo dell'applicazione:  
  ![Percorso del file di configurazione di diagnostica](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  Se non si trova il file, per informazioni su come abilitare l'estensione di Diagnostica nel progetto di Servizi cloud, vedere [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

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
> Se anche `diagnostics.wadcfgx` il file contiene un altro sink di tipo `ApplicationInsights`, tutte e tre le chiavi di strumentazione indicate di seguito devono corrispondere:  
>  * la chiave di strumentazione usata dall'applicazione.  
>  * la chiave di strumentazione usata dal sink `ApplicationInsights`.  
>  * la chiave di strumentazione usata dal sink `ApplicationInsightsProfiler`.  
>
> È possibile trovare il valore effettivo della chiave di strumentazione tramite il `ApplicationInsights` sink nel file ServiceConfiguration.\*.cscfg.  
> In seguito al rilascio dell'SDK di Azure 15.5 per Visual Studio, solo le chiavi di strumentazione usate dall'applicazione e dal sink `ApplicationInsightsProfiler` devono corrispondere tra loro.


## <a name="environment-deployment-and-runtime-configurations"></a>Configurazioni di distribuzione e runtime di ambiente

1. Distribuire la definizione di distribuzione dell'ambiente modificata.  

  Per poter applicare le modifiche, in genere è necessaria una distribuzione completa del modello o una pubblicazione di Servizi cloud tramite i cmdlet di PowerShell o Visual Studio.  

  Di seguito è riportato un approccio alternativo per macchine virtuali esistenti che interessa solo l'estensione di Diagnostica di Azure:  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/platform/server.aspx), abilitare la funzionalità di Windows `IIS Http Tracing`:  

  1. Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) oppure eseguire il comando seguente in PowerShell, come amministratore:  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Se non si riesce a stabilire l'accesso remoto, è possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) per eseguire il comando seguente:  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>Abilitare Profiler sui server locali

L'abilitazione di Profiler in un server locale è nota anche come esecuzione di Application Insights Profiler in modalità autonoma (non è associato alle modifiche dell'estensione di Diagnostica di Azure).

Non è disponibile alcun piano per il supporto ufficiale di Profiler sui i server locali. Se si è interessati all'uso di questo scenario, è possibile [scaricare il codice di supporto](https://github.com/ramach-msft/AIProfiler-Standalone). Microsoft *non* è responsabile della conservazione del codice o delle risposte ai problemi o alle richieste di funzioni correlate al codice.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler.md#troubleshooting).
- Per altre informazioni sul profiler, vedere [Application Insights Profiler](app-insights-profiler.md).
