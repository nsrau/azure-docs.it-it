---
title: Abilitare Application Insights Profiler per le applicazioni di Calcolo di Azure | Microsoft Docs
description: Informazioni su come configurare Profiler in un'applicazione in esecuzione in Calcolo di Azure.
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Abilitare Application Insights Profiler sulle macchine virtuali di Azure, su Service Fabric e Servizi cloud

Questa procedura dettagliata illustra come abilitare Azure Application Insights Profiler in un'applicazione ASP.NET ospitata da una risorsa di Calcolo di Azure.  
Gli esempi includono il supporto per le macchine virtuali di Azure, i set di scalabilità delle macchine virtuali, Azure Service Fabric e Servizi cloud di Azure.  
Gli esempi si basano su modelli che supportano il modello di distribuzione [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Panoramica

Il diagramma seguente illustra come funziona Profiler per le risorse di Azure. Come esempio viene usata una macchina virtuale di Azure.

![Panoramica](./media/enable-profiler-compute/overview.png)


Per abilitare completamente Profiler, la configurazione deve essere eseguita in tre percorsi:

1. nel pannello del portale per l'istanza di Application Insights;
2. nel codice sorgente dell'applicazione, ad esempio in un'applicazione web ASP.NET;
3. nel codice sorgente di definizione della distribuzione nell'ambiente, ad esempio un file JSON del modello di distribuzione della macchina virtuale.


## <a name="configure-the-application-insights-instance"></a>Configurare l'istanza di Application Insights

Creare o visitare il pannello del portale di Azure per l'istanza di Application Insights che si desidera usare e prendere nota della relativa chiave di strumentazione. Sarà utile per gli altri passaggi di configurazione:

  ![Posizione della chiave](./media/enable-profiler-compute/CopyAIKey.png)

Questa istanza deve essere la stessa configurata per l'invio di dati di telemetria dell'applicazione per ogni richiesta.
In aggiunta, i risultati di Profiler saranno disponibili in questa istanza.  

Nel portale di Azure seguire [Abilitare il profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) per completare la configurazione dell'istanza AI di Profiler. Non è necessario collegare le app Web ai fini di questa procedura dettagliata, è sufficiente assicurarsi che il profiler sia abilitato nel portale.


## <a name="configure-the-application-source-code"></a>Configurare il codice sorgente dell'applicazione

L'applicazione deve essere configurata per l'invio di dati di telemetria a un'istanza di Application Insights in ogni operazione `Request`.  

1. Aggiungere l'[SDK di Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) al progetto dell'applicazione. Verificare che le versioni del pacchetto NuGet siano le seguenti:  
  - Per le applicazioni ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) versione 2.3.0 o versione successiva.
  - Per le applicazioni ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o versione successiva.
  - Per altre applicazioni .NET e .NET Core , quali il servizio senza stato e il ruolo di lavoro del servizio cloud di Service Fabric: [Microsoft. applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o versione successiva.  

2. Se l'applicazione *non* è ASP.NET o ASP.NET Core, come i ruoli di lavoro di Servizi cloud, le API senza stato di Service Fabric, è necessaria la configurazione della strumentazione aggiuntiva seguente:  

  2.1. Aggiungere il codice seguente in un punto iniziale della durata dell'applicazione.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  Per altre informazioni sulla configurazione della chiave di strumentazione globale, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usare Service Fabric con Application Insights).  

  2.2. Per ogni parte del codice che si vuole instrumentare, aggiungere un'istruzione `StartOperation<RequestTelemetry>` `using`, come nell'esempio seguente:

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

> [!NOTE]  
> La chiamata a `StartOperation<RequestTelemetry>` all'interno di un altro ambito `StartOperation<RequestTelemetry>` non è supportata. È possibile invece usare `StartOperation<DependencyTelemetry>` nell'ambito nidificato. Esempio:  

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


## <a name="configure-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

L'ambiente in cui Profiler e l'applicazione vengono eseguiti può essere una macchina virtuale, il set di scalabilità di una macchina virtuale, il cluster di Service Fabric o un servizio cloud.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Macchina virtuale, set di scalabilità della macchina virtuale o Service Fabric

Esempi completi:  
  * [Macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Set di scalabilità della macchina virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Per garantire che il [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versione successiva siano in uso, è sufficiente confermare che il sistema operativo distribuito sia `Windows Server 2012 R2` o una versione successiva.

2. Individuare l'estensione [Diagnostica di Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) nel file del modello di distribuzione e aggiungere la sezione `SinksConfig` seguente come elemento figlio della proprietà `WadCfg`, sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione AI:  
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

Fare riferimento agli esempi e a [questa guida](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per informazioni su come aggiungere l'estensione Diagnostica al modello di distribuzione.


### <a name="cloud-services"></a>Servizi cloud

1. Per garantire che il [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versione successiva siano in uso, è sufficiente confermare che il file `ServiceConfiguration.*.cscfg` contenga `osFamily` come `"5"` o una versione successiva.

2. Individuare il file [Diagnostica di Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) `diagnostics.wadcfgx` per il ruolo dell'applicazione:  
![Percorso del file di configurazione di diagnostica](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
Se non si trova il file, fare riferimento a [questa guida](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them) per informazioni su come abilitare l'estensione Diagnostica nel progetto di Servizi cloud di Azure.

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
> Se anche il file `diagnostics.wadcfgx` contiene un altro sink di tipo `ApplicationInsights`, tutte e tre le chiavi di strumentazione indicate di seguito devono corrispondere:  
>  * la chiave di strumentazione usata dall'applicazione  
>  * la chiave di strumentazione usata dal sink `ApplicationInsights`  
>  * la chiave di strumentazione usata dal sink `ApplicationInsightsProfiler`  
>
>Il valore effettivo della chiave di strumentazione usato dal sink `ApplicationInsights` è reperibile nei file `ServiceConfiguration.*.cscfg`.  
>In seguito al rilascio dell'SDK di Azure 15.5 per Visual Studio, solo le chiavi di strumentazione usate dall'applicazione e dal sink `ApplicationInsightsProfiler` dovranno corrispondere tra loro.


## <a name="environment-deployment-and-runtime-configurations"></a>Configurazioni di distribuzione e runtime di ambiente

1. Distribuire la definizione di distribuzione dell'ambiente modificata.  
In genere, per poter applicare le modifiche, è necessaria una distribuzione completa del modello o una pubblicazione di Servizi cloud tramite i cmdlet di PowerShell o Visual Studio.  
Di seguito è riportato un approccio alternativo per `Virtual Machines` esistente che interessa solo l'estensione Diagnostica:  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. Se l'applicazione interessata è in esecuzione tramite [IIS](https://www.microsoft.com/web/platform/server.aspx), è necessario abilitare la funzionalità di Windows `IIS Http Tracing`:  
  Stabilire l'accesso remoto all'ambiente e quindi usare la finestra [Aggiungi funzionalità Windows]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) oppure eseguire le operazioni seguenti in PowerShell, come amministratore:  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  Se non si riesce a stabilire l'accesso remoto, è possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) per eseguire le operazioni seguenti:  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>Come abilitare Profiler sui server locali

Noto anche come esecuzione di AI Profiler in modalità autonoma, non collegato alle modifiche dell'estensione Diagnostica.  
Non è disponibile alcun piano per supportare ufficialmente Profiler sui i server locali.
Se si è interessati all'uso di questo scenario, è possibile scaricare il codice di supporto da [qui](https://github.com/ramach-msft/AIProfiler-Standalone).  
Microsoft *non* è responsabile della conservazione del codice o delle risposte ai problemi o alle richieste di funzioni correlate a questo.


## <a name="next-steps"></a>Passaggi successivi

- Generare un traffico nell'applicazione, ad esempio avviare un [test di disponibilità](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability) e consentire dopo 10-15 minuti l'avvio di tracce da inviare all'istanza di Application Insights.

- Esaminare le [tracce di Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) nel portale di Azure.

- Per informazioni su come risolvere i problemi relativi al profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler.md#troubleshooting).

- Per altre informazioni sul profiler, vedere [Application Insights Profiler](app-insights-profiler.md).
