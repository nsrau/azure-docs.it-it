---
title: Abilitare il Debugger di Snapshot per le app .NET in Azure Service Fabric, servizio Cloud e macchine virtuali | Microsoft Docs
description: Abilitare il Debugger di Snapshot per le app .NET in Azure Service Fabric, servizio Cloud e macchine virtuali
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783501"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Abilitare il Debugger di Snapshot per le app .NET in Azure Service Fabric, servizio Cloud e macchine virtuali

Se l'ASP.NET o ASP.NET core dell'esecuzione dell'applicazione nel servizio App di Azure, le istruzioni riportate di seguito sono anche utilizzabile. A meno che l'applicazione richiede una configurazione di Snapshot Debugger personalizzato, è altamente consigliabile [abilitare il Debugger di Snapshot tramite la pagina del portale di Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Se l'applicazione viene eseguita in Azure Service Fabric, servizio Cloud, macchine virtuali o computer locali, usare le istruzioni seguenti. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET

1. [Abilitare Application Insights nell'app Web](../../azure-monitor/app/asp-net.md) se non è ancora stato fatto.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Se necessario, personalizzare la configurazione del Debugger di Snapshot aggiunto alla [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). La configurazione di Snapshot Debugger predefinita è pressoché vuota e tutte le impostazioni sono facoltative. Ecco un esempio che illustra una configurazione equivalente per la configurazione predefinita:

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


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET Core 2.0

1. [Abilitare Application Insights nell'app Web ASP.NET Core](../../azure-monitor/app/asp-net-core.md) se non è ancora stato fatto.

    > [!NOTE]
    > Verificare che l'applicazione faccia riferimento alla versione 2.1.1 o più recente del pacchetto Microsoft.ApplicationInsights.AspNetCore.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Modificare la classe `Startup` dell'applicazione per aggiungere e configurare il processore di telemetria dell'agente di raccolta snapshot.

    Aggiungere le istruzioni using seguenti a `Startup.cs`

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

4. Se necessario, personalizzare la configurazione del Debugger di Snapshot tramite l'aggiunta di una sezione SnapshotCollectorConfiguration per appSettings. JSON. Tutte le impostazioni nella configurazione del Debugger di Snapshot sono facoltative. Ecco un esempio che illustra una configurazione equivalente per la configurazione predefinita:

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

- Generare traffico verso l'applicazione che può generare un'eccezione. Attendere quindi 10-15 minuti per gli snapshot da inviare all'istanza di Application Insights.
- Visualizzare [snapshot](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni sulla risoluzione dei problemi del Profiler, vedere [risoluzione dei problemi di Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
