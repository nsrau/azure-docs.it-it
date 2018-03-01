---
title: Raccogliere dati con i contatori delle prestazioni in Servizi cloud di Azure | Microsoft Docs
description: Informazioni su come individuare, usare e creare contatori delle prestazioni in Servizi cloud con Diagnostica di Azure e Application Insights.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Raccogliere dati con i contatori delle prestazioni per il servizio cloud di Azure

I contatori delle prestazioni consentono di rilevare le prestazioni di applicazioni e host. Windows Server include molti contatori delle prestazioni diversi relativi a hardware, applicazioni, sistema operativo e altri aspetti. Con la raccolta e l'invio di dati dei contatori delle prestazioni ad Azure, è possibile analizzare queste informazioni a supporto di un migliore processo decisionale. 

## <a name="discover-available-counters"></a>Individuare i contatori disponibili

Un contatore delle prestazioni è costituito da due parti, il nome di un set (noto anche come categoria) e uno o più contatori. È possibile usare PowerShell per ottenere un elenco dei contatori delle prestazioni disponibili:

```PowerShell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

La proprietà `CounterSetName` rappresenta un set (o categoria) ed è un buon indicatore degli oggetti a cui sono correlati i contatori delle prestazioni. La proprietà `Paths` rappresenta una raccolta di contatori per un set. È anche possibile ottenere la proprietà `Description` per recuperare altre informazioni sul set di contatori.

Per ottenere tutti i contatori per un set, usare il valore `CounterSetName` ed espandere la raccolta `Paths`. Ogni elemento del percorso è un contatore su cui è possibile eseguire una query. Ad esempio, per ottenere i contatori disponibili correlati al set `Processor`, espandere la raccolta `Paths`:

```PowerShell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Questi singoli percorsi di contatori possono essere aggiunti al framework di diagnostica usato dal servizio cloud. Per altre informazioni su come viene costruito un percorso di contatore delle prestazioni, vedere [Specifying a Counter Path](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)) (Specifica di un percorso di contatore).

## <a name="collect-a-performance-counter"></a>Raccogliere dati per un contatore delle prestazioni

Un contatore delle prestazioni può essere aggiunto al servizio cloud sia per Diagnostica di Azure che per Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights per Servizi cloud consente di specificare i contatori delle prestazioni per i quali raccogliere dati. Dopo aver [aggiunto Application Insights al progetto](../application-insights/app-insights-cloudservices.md#sdk), al progetto di Visual Studio viene aggiunto un file di configurazione denominato **ApplicationInsights.config**. Questo file di configurazione definisce il tipo di informazioni che Application Insights raccoglie e invia ad Azure.

Aprire il file **ApplicationInsights.config** e individuare l'elemento **ApplicationInsights** > **TelemetryModules**. Ogni elemento figlio `<Add>` definisce un tipo di dati di telemetria da raccogliere, insieme alla relativa configurazione. Il tipo di modulo di telemetria del contatore delle prestazioni è `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Se questo elemento è già definito, non aggiungerlo una seconda volta. Ogni contatore delle prestazioni da includere nella raccolta viene definito in un nodo denominato `<Counters>`. L'esempio seguente raccoglie dati per i contatori delle prestazioni relativi alle unità:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Ogni contatore delle prestazioni è rappresentato come elemento `<Add>` in `<Counters>`. L'attributo `PerformanceCounter` definisce il contatore delle prestazioni da includere nella raccolta. L'attributo `ReportAs` è il titolo da visualizzare nel portale di Azure per il contatore delle prestazioni. Qualsiasi contatore delle prestazioni selezionato per la raccolta di dati viene inserito in una categoria denominata **Personalizzato** nel portale. Diversamente da Diagnostica di Azure, non è possibile impostare l'intervallo di raccolta e invio dei dati per questi contatori delle prestazioni ad Azure. Con Application Insights, i dati dei contatori delle prestazioni vengono raccolti e inviati ogni minuto. 

Application Insights raccoglie automaticamente i dati per i contatori delle prestazioni seguenti:

* \Process(??APP_WIN32_PROC??)\% Processor Time
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Per altre informazioni, vedere [Contatori delle prestazioni di sistema in Application Insights](../application-insights/app-insights-performance-counters.md) e [Application Insights per Servizi cloud di Azure](../application-insights/app-insights-cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostica Azure

> [!IMPORTANT]
> Tutti questi dati vengono aggregati nell'account di archiviazione, ma il portale **non** fornisce un modo nativo per rappresentarli graficamente. È consigliabile integrare un altro servizio di diagnostica, come Application Insights, nell'applicazione.

L'estensione Diagnostica di Azure per Servizi cloud consente di specificare quali contatori delle prestazioni includere nella raccolta di dati. Per configurare Diagnostica di Azure, vedere [Presentazione del monitoraggio del servizio cloud](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

I contatori delle prestazioni da includere nella raccolta vengono definiti nel file **diagnostics.wadcfgx**. Aprire questo file (è definito per ogni ruolo) in Visual Studio e individuare l'elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Aggiungere un nuovo elemento **PerformanceCounterConfiguration** come elemento figlio. Questo elemento ha due attributi: `counterSpecifier` e `sampleRate`. L'attributo `counterSpecifier` definisce il set di contatori delle prestazioni di sistema (descritto nella sezione precedente) da includere nella raccolta. Il valore `sampleRate` indica la frequenza di polling di tale valore. Nel suo complesso, tutti i contatori delle prestazioni vengono trasferiti in Azure in base al valore dell'attributo `scheduledTransferPeriod` dell'elemento padre `PerformanceCounters`.

Per altre informazioni sull'elemento dello schema `PerformanceCounters`, vedere [Schema di configurazione di Diagnostica di Azure](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element).

Il periodo definito dall'attributo `sampleRate` usa il tipo di dati duration XML per indicare la frequenza di polling del contatore delle prestazioni. Nell'esempio seguente la frequenza è impostata su `PT3M`, ovvero `[P]eriod[T]ime[3][M]inutes`, cioè ogni tre minuti.

Per altre informazioni su come definire `sampleRate` e `scheduledTransferPeriod`, vedere la sezione **Duration Data Type** (Tipo di dati duration) nell'esercitazione [W3 XML Date and Time Data Types](https://www.w3schools.com/XML/schema_dtypes_date.asp) (Tipi di dati di data e ora XML).

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Creare un nuovo contatore delle prestazioni

Un nuovo contatore delle prestazioni può essere creato e usato dal codice. Il codice che crea un nuovo contatore delle prestazioni deve essere in esecuzione con privilegi elevati, in caso contrario non funzionerà. Il contatore delle prestazioni può essere creato dal codice di avvio `OnStart` del servizio cloud e ciò richiede l'esecuzione del ruolo in un contesto con privilegi elevati. Oppure è possibile creare un'attività di avvio che viene eseguita con privilegi elevati e crea il contatore delle prestazioni. Per altre informazioni sulle attività di avvio, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](cloud-services-startup-tasks.md).

Per configurare il ruolo per l'esecuzione con privilegi elevati, aggiungere un elemento `<Runtime>` al file con estensione [csdef](cloud-services-model-and-package.md#servicedefinitioncsdef).

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

È possibile creare e registrare un nuovo contatore delle prestazioni con poche righe di codice. Usare l'overload del metodo `System.Diagnostics.PerformanceCounterCategory.Create` che crea sia la categoria che il contatore. Il codice seguente controlla prima di tutto se la categoria esiste e, se manca, crea sia la categoria che il contatore.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the cateogry and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Quando si vuole usare il contatore, chiamare il metodo `Increment` o `IncrementBy`.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Ora che l'applicazione usa il contatore personalizzato, è necessario configurare Diagnostica di Azure o Application Insights per tenere traccia del contatore.


### <a name="application-insights"></a>Application Insights

Come già indicato, i contatori delle prestazioni per Application Insights vengono definiti nel file **ApplicationInsights.config**. Aprire il file **ApplicationInsights.config** e individuare l'elemento **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters**. Creare un elemento figlio `<Add>` e impostare l'attributo `PerformanceCounter` sulla categoria e il nome del contatore delle prestazioni creato nel codice. Impostare l'attributo `ReportAs` su un nome descrittivo da visualizzare nel portale.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Diagnostica Azure

Come indicato in precedenza, i contatori delle prestazioni da includere nella raccolta sono definiti nel file **diagnostics.wadcfgx**. Aprire questo file (è definito per ogni ruolo) in Visual Studio e individuare l'elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Aggiungere un nuovo elemento **PerformanceCounterConfiguration** come elemento figlio. Impostare l'attributo `counterSpecifier` sulla categoria e il nome del contatore delle prestazioni creato nel codice. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Altre informazioni

- [Application Insights per Servizi cloud di Azure](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Contatori delle prestazioni di sistema in Application Insights](../application-insights/app-insights-performance-counters.md)
- [Specifying a Counter Path](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)) (Specifica di un percorso di contatore)
- [Schema di Diagnostica di Azure - Contatori delle prestazioni](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)