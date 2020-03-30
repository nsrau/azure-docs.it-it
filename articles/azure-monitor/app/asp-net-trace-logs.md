---
title: Esplorare i log di traccia .NET in Application Insights
description: Cercare i log generati da Trace, NLog o Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276270"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Esplorare i log di traccia di .NET/.NET Core e Python in Application Insights

Inviare log di traccia diagnostica per l'applicazione ASP.NET/ASP.NET Core da ILogger, NLog, log4Net o System.Diagnostics.Trace ad [Azure Application Insights][start]. For Python applications, send diagnostic tracing logs using AzureLogHandler in OpenCensus Python for Azure Monitor. È quindi possibile esplorarli e cercarli. Tali log vengono uniti agli altri file di log dell'applicazione, pertanto è possibile identificare le tracce associate a ogni richiesta dell'utente e correlarle con altri eventi e report di eccezioni.

> [!NOTE]
> Hai bisogno del modulo log-capture? È un adattatore utile per i logger di terze parti. Tuttavia, se non si sta già utilizzando NLog, log4Net o System.Diagnostics.Trace, è sufficiente chiamare direttamente [**Application Insights TrackTrace().**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
>
>
## <a name="install-logging-on-your-app"></a>Installare la registrazione nell'applicazione
Installare il framework di registrazione scelto nel progetto, che dovrebbe generare una voce in app.config o web.config.Install your chosen logging framework in your project, which should result in an entry in app.config or web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurare Application Insights per la raccolta dei log
Se non è ancora stato fatto, [aggiungere Application Insights al progetto](../../azure-monitor/app/asp-net.md). Verrà visualizzata un'opzione per includere la raccolta dei log.

In alternativa, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni per **configurare Application Insights**. Selezionare l'opzione **Configura raccolta tracce.**

> [!NOTE]
> Il menu di Application Insights o  l'opzione di raccolta non viene visualizzata? Vedere [Risoluzione dei problemi](#troubleshooting).

## <a name="manual-installation"></a>Installazione manuale
Usare questo metodo se il tipo di progetto non è supportato dal programma di installazione di Application Insights, ad esempio un progetto Desktop di Windows.

1. Se si intende usare log4Net o NLog, installarlo nel progetto.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e **scegliere Gestisci pacchetti NuGet**.
3. Cercare "Application Insights".
4. Selezionare uno dei pacchetti seguenti:

   - Per ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Per NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Per Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Per System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Il pacchetto NuGet installa gli assembly necessari e modifica web.config o app.config, se applicabile.

## <a name="ilogger"></a>ILogger

Per esempi di utilizzo dell'implementazione di Application Insights ILogger con le applicazioni console e ASP.NET Core, vedere [ApplicationInsightsLoggerProvider per i log iLogger](ilogger.md)di .NET Core .

## <a name="insert-diagnostic-log-calls"></a>Inserire chiamate di log di diagnostica
Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog, utilizzare:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Utilizzare gli eventi EventSourceUse EventSource events
È possibile configurare eventi [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **Name** specifica il nome dell'oggetto EventSource da raccogliere.
 * **Level** specifica il livello di registrazione da raccogliere: *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose*o *Warning*.
 * **Le parole chiave** (facoltativo) specificano il valore intero delle combinazioni di parole chiave da utilizzare.

## <a name="use-diagnosticsource-events"></a>Usare gli eventi DiagnosticSourceUse DiagnosticSource events
È possibile configurare eventi [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) da inviare ad Application Insights come tracce. Installare innanzitutto [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) il pacchetto NuGet.First, install the NuGet package. Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Per ogni DiagnosticSource che si desidera tracciare, aggiungere una voce con l'attributo **Name** impostato sul nome di DiagnosticSource.

## <a name="use-etw-events"></a>Utilizzare gli eventi ETW
È possibile configurare gli eventi ETW (Event Tracing for Windows) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EtwCollector`. Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Gli eventi ETW possono essere raccolti solo se il processo che ospita l'SDK viene eseguito con un'identità membro degli utenti o degli amministratori dei registri di prestazioni.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **ProviderName** è il nome del provider ETW da raccogliere.
 * **ProviderGuid** specifica il GUID del provider ETW da raccogliere. Può essere utilizzato `ProviderName`al posto di .
 * **Level** imposta il livello di registrazione da raccogliere. Può essere *Critico*, *Errore*, *Informativo*, *LogAlways*, *Dettagliato*o *Avviso*.
 * **Le parole chiave** (facoltativo) impostano il valore intero delle combinazioni di parole chiave da utilizzare.

## <a name="use-the-trace-api-directly"></a>Usare direttamente l'API TraceUse the Trace API directly
È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio è possibile codificare dati POST.

È inoltre possibile aggiungere un livello di gravità al messaggio. Inoltre, come altri dati di telemetria, è possibile aggiungere valori di proprietà per filtrare o cercare set di tracce diversi. Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

In questo modo è possibile filtrare facilmente in [Cerca][diagnostic] tutti i messaggi di un determinato livello di gravità correlati a un determinato database.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler per OpenCensus Python
Il gestore dei log di Monitoraggio di Azure consente di esportare i log Python in Monitoraggio di Azure.The Azure Monitor Log Handler allows you to export Python logs to Azure Monitor.

Instrumentare l'applicazione con [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) per Azure Monitor.

Questo esempio mostra come inviare un log di livello di avviso ad Monitoraggio di Azure.This example shows how to send a warning level log to Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Esplorare i log
Esegui l'app in modalità di debug o distribuiscila in tempo reale.

Nel riquadro Panoramica dell'app nel [portale di Application Insights][portal]selezionare [Cerca.][diagnostic]

Ad esempio, è possibile:

* Filtrare le tracce di log o gli elementi con proprietà specifiche.
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri dati del log di sistema correlati alla stessa richiesta dell'utente (ha lo stesso OperationId).
* Salvare la configurazione di una pagina come preferita.

> [!NOTE]
>Se l'applicazione invia molti dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o successiva, la funzionalità di *campionamento adattivo* potrebbe funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="how-do-i-do-this-for-java"></a>Come procedere per Java?
Usare gli [adattatori log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Non è disponibile alcuna opzione di Application Insights nel menu di scelta rapida del progetto
* Assicurati che Developer Analytics Tools sia installato nel computer di sviluppo. In**Estensioni e aggiornamenti**di Visual Studio **Tools** > cercare Developer **Analytics Tools**. Se non è presente nella scheda **Installato,** aprire la scheda **Online** e installarla.
* Potrebbe trattarsi di un tipo di progetto non supportato da Devloper Analytics Tools. Usare l' [installazione manuale](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Non è disponibile alcuna opzione di scheda di log nello strumento di configurazione
* Installare prima il framework di registrazione.
* Se si utilizza System.Diagnostics.Trace, assicurarsi di averlo [configurato in *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Assicurarsi di disporre della versione più recente di Application Insights. In Visual Studio passare a**Estensioni e aggiornamenti**degli **strumenti** > e aprire la scheda **Aggiornamenti.** Se **Developer Analytics Tools** è presente, selezionalo per aggiornarlo.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Viene visualizzato il messaggio di errore "Impossibile svuotare la chiave di strumentazione"
Probabilmente è stato installato il pacchetto Nuget dell'adapter di registrazione senza installare Application Insights. In Esplora soluzioni fare clic con il pulsante destro del mouse su *ApplicationInsights.config*e scegliere **Aggiorna Application Insights**. Verrà richiesto di accedere ad Azure e creare una risorsa di Application Insights o riutilizzarne una esistente. Questo dovrebbe risolvere il problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>È possibile visualizzare le tracce ma non altri eventi nella ricerca diagnostica
L'esecuzione di tutti gli eventi e le richieste attraverso la pipeline può richiedere un po' di tempo.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Quanti dati vengono conservati?
Diversi fattori influiscono sulla quantità di dati conservati. Per ulteriori informazioni, consulta la sezione [relativa ai limiti](../../azure-monitor/app/api-custom-events-metrics.md#limits) della pagina metriche degli eventi dei clienti.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Non vedo alcune voci di registro che mi aspettavo
Se l'applicazione invia quantità voluminose di dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o successiva, la funzionalità di campionamento adattivo potrebbe funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Passaggi successivi

* [Diagnosticare errori ed eccezioni in ASP.NET][exceptions]
* [Ulteriori informazioni sulla ricerca][diagnostic]
* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
