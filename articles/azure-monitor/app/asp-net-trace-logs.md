---
title: Esplorare i log di traccia .NET in Application Insights
description: Eseguire ricerche nei log generati da Trace, NLog o Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: bcd21286a547e0b0a6b5b93e8b05921e8e8cc1e2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647914"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Esplorare i log di traccia .NET/.NET Core e Python in Application Insights

Inviare i log di tracce diagnostiche per l'applicazione ASP.NET/ASP.NET Core da ILogger, NLog, log4Net o System.Diagnostics.Trace ad [Azure Application Insights][start]. Per le applicazioni Python, inviare i log di tracce diagnostiche usando AzureLogHandler in OpenCensus Python per Monitoraggio di Azure. Sarà quindi possibile esplorarli ed eseguirvi ricerche. Questi log vengono uniti agli altri file di log dell'applicazione, pertanto è possibile identificare le tracce associate a ogni richiesta utente e metterle in correlazione con altri eventi e report di eccezioni.

> [!NOTE]
> Può essere necessario un modulo di acquisizione dei log. Si tratta di un adattatore utile per i logger di terze parti. Se tuttavia non è già in uso NLog, log4Net o System.Diagnostics.Trace, è opportuno chiamare direttamente [**TrackTrace() di Application Insights**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace).
>
>
## <a name="install-logging-on-your-app"></a>Installare la registrazione nell'applicazione
Installare il framework di registrazione scelto nel progetto. Viene generata una voce corrispondente in app.config o web.config.

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

In alternativa, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Configura Application Insights**. Selezionare l'opzione **Configure trace collection** (Configura raccolta delle tracce).

> [!NOTE]
> È possibile che il menu di Application Insights o l'opzione di raccolta dei log non sia presente. Vedere [Risoluzione dei problemi](#troubleshooting).

## <a name="manual-installation"></a>Installazione manuale
Usare questo metodo se il tipo di progetto non è supportato dal programma di installazione di Application Insights, ad esempio un progetto Desktop di Windows.

1. Se si intende usare log4Net o NLog, installarlo nel progetto.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
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

Il pacchetto NuGet installa gli assembly necessari e modifica web.config o app.config se applicabile.

## <a name="ilogger"></a>ILogger

Per alcuni esempi d'uso dell'implementazione ILogger di Application Insights con applicazioni console e ASP.NET Core, vedere [ApplicationInsightsLoggerProvider per i log ILogger di .NET Core](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserire chiamate di log di diagnostica
Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog, usare:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Usare gli eventi di EventSource
È possibile configurare eventi [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **Name** specifica il nome di EventSource da raccogliere.
 * **Level** specifica il livello di registrazione da raccogliere: *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* o *Warning*.
 * **Keywords** (facoltativo) specifica il valore intero di combinazioni di parole chiave da usare.

## <a name="use-diagnosticsource-events"></a>Usare gli eventi di DiagnosticSource
È possibile configurare eventi [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Per ogni DiagnosticSource che si vuole tracciare, aggiungere una voce con l'attributo **Name** impostato sul nome di DiagnosticSource.

## <a name="use-etw-events"></a>Usare gli eventi ETW
È possibile configurare gli eventi di Event Trace for Windows (ETW) in modo che vengano inviati ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EtwCollector`. Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Gli eventi ETW possono essere raccolti solo se il processo che ospita l'SDK viene eseguito in un'identità che è membro del gruppo Performance Log Users o Administrators.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **ProviderName** è il nome del provider ETW da raccogliere.
 * **ProviderGuid** specifica il GUID del provider ETW da raccogliere. Può essere usato in sostituzione di `ProviderName`.
 * **Level** imposta il livello di registrazione da raccogliere. Può essere impostato su *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* o *Warning*.
 * **Keywords** (facoltativo) imposta il valore intero di combinazioni di parole chiave da usare.

## <a name="use-the-trace-api-directly"></a>Usare l'API di traccia direttamente
È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio è possibile codificare dati POST.

È anche possibile aggiungere al messaggio un livello di gravità. Inoltre, come per altri tipi di dati di telemetria, è possibile aggiungere valori di proprietà utili per filtrare o cercare set diversi di tracce. Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

In questo modo, è possibile filtrare facilmente in [Ricerca][diagnostic] tutti i messaggi con un determinato livello di gravità relativi a un database specifico.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler per OpenCensus Python
Il gestore log di Monitoraggio di Azure consente di esportare i log di Python in Monitoraggio di Azure.

Instrumentare l'applicazione con l'[SDK OpenCensus Python](../../azure-monitor/app/opencensus-python.md) per Monitoraggio di Azure.

Questo esempio illustra come inviare un log del livello di avviso a Monitoraggio di Azure.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Esplorare i log
Eseguire l'app in modalità debug o distribuirla in modalità live.

Nel riquadro di panoramica dell'app nel [portale di Application Insights][portal] selezionare [Cerca][diagnostic].

Ad esempio, è possibile:

* Filtrare in base a tracce di log o elementi con proprietà specifiche.
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri dati del registro di sistema relativi alla stessa richiesta utente (con OperationId identico).
* Salvare la configurazione di una pagina come preferita.

> [!NOTE]
>Se l'applicazione invia una grande quantità di dati e si sta usando l'SDK di Application Insights per ASP.NET versione 2.0.0-beta3 o successive, la funzionalità di *campionamento adattivo* può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="how-do-i-do-this-for-java"></a>Come procedere per Java?
Nella strumentazione Java senza codice (scelta consigliata) i log vengono raccolti per impostazione predefinita. Usare l'[agente Java 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

Se si usa Java SDK, usare gli [adattatori di log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Non è disponibile alcuna opzione di Application Insights nel menu di scelta rapida del progetto
* Verificare che Developer Analytics Tools sia installato nel computer di sviluppo. In **Strumenti** > **Estensioni e aggiornamenti** di Visual Studio cercare **Developer Analytics Tools**. Se non è presente nella scheda **Installati**, aprire la scheda **Online** e installarlo.
* Può trattarsi di un tipo di progetto che Developer Analytics Tools non supporta. Usare l' [installazione manuale](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Nello strumento di configurazione non è presente alcuna opzione per l'adattatore di log
* Installare prima il framework di registrazione.
* Se si usa System.Diagnostics.Trace, verificare di averlo [configurato in *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Assicurarsi di avere la versione più recente di Application Insights. In Visual Studio passare a **Strumenti** > **Estensioni e aggiornamenti** e aprire la scheda **Aggiornamenti**. Se **Developer Analytics Tools** è presente, selezionarlo per eseguire l'aggiornamento.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Viene visualizzato il messaggio di errore indicante che la chiave di strumentazione non può essere vuota
L'adattatore di registrazione è stato probabilmente installato senza Application Insights. In Esplora soluzioni fare clic con il pulsante destro del mouse su *ApplicationInsights.config* e scegliere **Aggiorna Application Insights**. Verrà richiesto di accedere ad Azure e creare una risorsa di Application Insights oppure riusarne una esistente. In questo modo il problema dovrebbe essere risolto.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Nella ricerca diagnostica è possibile visualizzare le tracce ma non altri eventi
Può essere necessario qualche minuto prima che tutti gli eventi e le richieste abbiano superato la pipeline.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Quanti dati vengono conservati?
La quantità di dati mantenuti dipende da diversi fattori. Per altre informazioni, vedere la sezione [Limiti](../../azure-monitor/app/api-custom-events-metrics.md#limits) della pagina relativa alle metriche degli eventi dei clienti.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Alcune voci di log previste non vengono visualizzate
Se l'applicazione invia notevoli quantità di dati e si sta usando l'SDK di Application Insights per ASP.NET versione 2.0.0-beta3 o successive, la funzionalità di campionamento adattivo può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Passaggi successivi

* [Diagnosticare errori ed eccezioni in ASP.NET][exceptions]
* [Vedere altre informazioni sulla ricerca][diagnostic]
* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
