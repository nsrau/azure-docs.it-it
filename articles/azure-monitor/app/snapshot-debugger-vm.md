---
title: Abilitare il debugger snapshot per le app .NET in Azure Service Fabric, servizio cloud e macchine virtuali. Documenti Microsoft
description: Abilitare il debugger snapshot per le app .NET in Azure Service Fabric, Servizio cloud e Macchine virtualiEnable Snapshot Debugger for .NET apps in Azure Service Fabric, Cloud Service, and Virtual Machines
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671343"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Abilitare il debugger snapshot per le app .NET in Azure Service Fabric, Servizio cloud e Macchine virtualiEnable Snapshot Debugger for .NET apps in Azure Service Fabric, Cloud Service, and Virtual Machines

Se l'applicazione di base ASP.NET o ASP.NET viene eseguita nel servizio app di Azure, è consigliabile abilitare il [debugger snapshot tramite la pagina del portale](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)di Application Insights. Tuttavia, se l'applicazione richiede una configurazione personalizzata del debugger snapshot o una versione di anteprima del core .NET, è necessario seguire questa istruzione ***oltre*** alle istruzioni per [l'abilitazione tramite la pagina del portale](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)di Application Insights .

Se l'applicazione viene eseguita in Azure Service Fabric, Servizio cloud, Macchine virtuali o macchine locali, è necessario usare le istruzioni seguenti. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET

1. [Abilitare Application Insights nell'app Web](../../azure-monitor/app/asp-net.md) se non è ancora stato fatto.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Se necessario, è stata personalizzata la configurazione del debugger snapshot aggiunta a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). La configurazione predefinita del debugger snapshot è per lo più vuota e tutte le impostazioni sono facoltative. Di seguito è riportato un esempio che mostra una configurazione equivalente alla configurazione predefinita:Here is an example showing a configuration equivalent to the default configuration:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Gli snapshot vengono raccolti solo per le eccezioni segnalate ad Application Insights. In alcuni casi (ad esempio, versioni precedenti della piattaforma .NET), potrebbe essere necessario [configurare la raccolta di eccezioni](../../azure-monitor/app/asp-net-exceptions.md#exceptions) per visualizzare le eccezioni con gli snapshot nel portale.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Configurare la raccolta di snapshot per le applicazioni usando ASP.NET Core 2.0 o versioni successiveConfigure snapshot collection for applications using ASP.NET Core 2.0 or above

1. [Abilitare Application Insights nell'app Web ASP.NET Core](../../azure-monitor/app/asp-net-core.md) se non è ancora stato fatto.

    > [!NOTE]
    > Verificare che l'applicazione faccia riferimento alla versione 2.1.1 o più recente del pacchetto Microsoft.ApplicationInsights.AspNetCore.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Modificare la classe `Startup` dell'applicazione per aggiungere e configurare il processore di telemetria dell'agente di raccolta snapshot.
    1. Se viene utilizzato [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet package versione 1.3.5 o versione `Startup.cs`successiva, aggiungere le seguenti istruzioni using a .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Aggiungere quanto segue alla fine del metodo `Startup` ConfigureServices nella classe in `Startup.cs`.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Se viene utilizzato [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet package versione 1.3.4 o versione `Startup.cs`successiva, aggiungere le seguenti istruzioni using a .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Aggiungere la classe `SnapshotCollectorTelemetryProcessorFactory` seguente a `Startup`.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Aggiungere i servizi `SnapshotCollectorConfiguration` e `SnapshotCollectorTelemetryProcessorFactory` alla pipeline di avvio:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Se necessario, è stata personalizzata la configurazione del debugger snapshot aggiungendo una sezione SnapshotCollectorConfiguration a appsettings.json. Tutte le impostazioni nella configurazione di Snapshot Debugger sono facoltative. Di seguito è riportato un esempio che mostra una configurazione equivalente alla configurazione predefinita:Here is an example showing a configuration equivalent to the default configuration:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurare la raccolta di snapshot per le altre applicazioni .NET

1. Se l'applicazione non è già instrumentata con Application Insights, iniziare [abilitando Application Insights e impostando la chiave di strumentazione](../../azure-monitor/app/windows-desktop.md).

2. Aggiungere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Gli snapshot vengono raccolti solo per le eccezioni segnalate ad Application Insights. Potrebbe essere necessario modificare il codice per segnalarle. Anche se il codice di gestione delle eccezioni dipende dalla struttura dell'applicazione, di seguito è riportato un esempio:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione che può generare un'eccezione. Attendere quindi da 10 a 15 minuti per l'invio di snapshot all'istanza di Application Insights.Then, wait 10 to 15 minutes for snapshots to be sent to the Application Insights instance.
- Vedere gli snapshot nel portale di Azure.See [snapshots](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in the Azure portal.
- Per informazioni sulla risoluzione dei problemi relativi a Snapshot Debugger, vedere [Risoluzione dei problemi relativi](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)a Snapshot Debugger .
