---
title: Esplorare i log di traccia .NET in Application Insights
description: Eseguire ricerche nei log generati da Trace, NLog o Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472059"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Esplorare i log di traccia .NET/.NET Core in Application Insights

Inviare i log di traccia di diagnostica per l'applicazione ASP.NET/ASP.NET Core dall'ILogger, NLog, log4Net o Trace per [Azure Application Insights][start]. È quindi possibile esplorare ed eseguirvi ricerche. Questi log vengono uniti con gli altri file di log dall'applicazione, pertanto è possibile identificare le tracce che sono associati a ogni richiesta dell'utente e metterle in correlazione con altri eventi e i report di eccezione.

> [!NOTE]
> È necessario il modulo di acquisizione di log? Si tratta di una scheda utile per i logger terze parti. Ma se già non si usa NLog, log4Net o Trace, è possibile chiamare solo [ **tracktrace () di Application Insights** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) direttamente.
>
>
## <a name="install-logging-on-your-app"></a>Installare la registrazione nell'applicazione
Installare il framework di registrazione scelto nel progetto, con il risultato di una voce nel file app. config o Web. config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurare Application Insights per la raccolta dei log
[Aggiungere Application Insights al progetto](../../azure-monitor/app/asp-net.md) se non è ancora stato fatto. Verrà visualizzata un'opzione per includere la raccolta dei log.

O il pulsante destro del mouse sul progetto in Esplora soluzioni per **Configura Application Insights**. Selezionare il **configurare la traccia raccolta** opzione.

> [!NOTE]
> Nessuna opzione di agente di raccolta log o menu Application Insights? Vedere [Risoluzione dei problemi](#troubleshooting).

## <a name="manual-installation"></a>Installazione manuale
Usare questo metodo se il tipo di progetto non è supportato dal programma di installazione di Application Insights, ad esempio un progetto Desktop di Windows.

1. Se si intende usare log4Net o NLog, installarlo nel progetto.
2. In Esplora soluzioni fare clic sul progetto e selezionare **Gestisci pacchetti NuGet**.
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

Il pacchetto NuGet installa gli assembly necessari e modifica Web. config o App. config, se applicabile.

## <a name="ilogger"></a>ILogger

Per esempi dell'uso dell'implementazione di Application Insights ILogger con le applicazioni console e ASP.NET Core, vedere [ApplicationInsightsLoggerProvider per .NET Core ILogger registra](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserire chiamate di log di diagnostica
Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog, usare:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Usare gli eventi EventSource
È possibile configurare eventi [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **Nome** specifica il nome di EventSource da raccogliere.
 * **Livello** specifica il livello di registrazione da raccogliere: *Critici*, *errore*, *informativo*, *LogAlways*, *Verbose*, o *avviso*.
 * **Parole chiave** (facoltativo) specificare il valore intero di combinazioni di parole chiave da usare.

## <a name="use-diagnosticsource-events"></a>Usare gli eventi DiagnosticSource
È possibile configurare eventi [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Quindi modificare la sezione "TelemetryModules" del [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) file.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Per ogni DiagnosticSource che si desidera tracciare, aggiungere una voce con la **nome** attributo impostato sul nome di DiagnosticSource.

## <a name="use-etw-events"></a>Usare gli eventi ETW
È possibile configurare eventi di Event Tracing for Windows (ETW) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EtwCollector`. Quindi modificare la sezione "TelemetryModules" del [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) file.

> [!NOTE] 
> Eventi ETW possono essere raccolti solo se il processo che ospita il SDK viene eseguito con un'identità che è un membro di Administrators o Performance Log Users.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **ProviderName** è il nome del provider ETW da raccogliere.
 * **ProviderGuid** specifica il GUID del provider ETW da raccogliere. Può essere utilizzato al posto di `ProviderName`.
 * **Livello** imposta il livello di registrazione da raccogliere. Può essere *critici*, *errore*, *Informational*, *LogAlways*, *Verbose*, oppure *Avviso*.
 * **Parole chiave** (facoltativo) imposta il valore intero di combinazioni di parole chiave da usare.

## <a name="use-the-trace-api-directly"></a>Usare direttamente l'API di traccia
È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio è possibile codificare dati POST.

È anche possibile aggiungere un livello di gravità per il messaggio. E, come altri dati di telemetria, è possibile aggiungere i valori delle proprietà per consentire di filtrare o cercare set di tracce diversi. Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Questo consentirà di filtrare facilmente nelle [ricerca] [ diagnostic] tutti i messaggi di un determinato livello di gravità relativi a un database specifico.

## <a name="explore-your-logs"></a>Esplorare i log
Eseguire l'app in modalità di debug o distribuirla.

Nel riquadro di panoramica dell'app nel [portale di Application Insights][portal], selezionare [ricerca][diagnostic].

Ad esempio, è possibile:

* Filtrare le tracce del log o sugli elementi con proprietà specifiche.
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri dati di log di sistema che si riferisce alla stessa richiesta dell'utente (con lo stesso valore OperationId).
* Salvare la configurazione di una pagina come preferita.

> [!NOTE]
>Se l'applicazione invia una grande quantità di dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o versioni successive, il *campionamento adattivo* funzionalità può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>risoluzione dei problemi
### <a name="how-do-i-do-this-for-java"></a>Come procedere per Java?
Usare gli [adattatori log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Non è disponibile alcuna opzione di Application Insights nel menu di scelta rapida del progetto
* Assicurarsi che Developer Analytics Tools è installato nel computer di sviluppo. In Visual Studio **degli strumenti** > **estensioni e aggiornamenti**, cercare **Developer Analytics Tools**. Se non è nel **Installed** scheda, aprire il **Online** scheda e installarlo.
* Potrebbe trattarsi di un tipo di progetto che non supporta gli strumenti di Analitica di sviluppatori. Usare l' [installazione manuale](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Vi è alcuna possibilità di adattatori di log nello strumento di configurazione
* Installare innanzitutto il framework di registrazione.
* Se si usa Trace, assicurarsi di averla installata [configurata nel *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Assicurarsi di avere la versione più recente di Application Insights. In Visual Studio, passare a **degli strumenti** > **estensioni e aggiornamenti**e aprire il **aggiornamenti** scheda. Se **Developer Analytics Tools** è presente, selezionarlo per aggiornarlo.

### <a name="emptykey"></a>Viene visualizzato il messaggio di errore "chiave di strumentazione non può essere vuota"
Probabilmente è installato il pacchetto Nuget dell'adattatore registrazione senza installare Application Insights. In Esplora soluzioni fare doppio clic su *applicationinsights. config*e selezionare **Aggiorna Application Insights**. Verrà richiesto di accedere ad Azure e creare una risorsa di Application Insights o usarne uno esistente. Che dovrebbe risolvere il problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso vedere le tracce ma non altri eventi in ricerca diagnostica
Può richiedere un po' di tempo per tutti gli eventi e le richieste attraverso la pipeline.

### <a name="limits"></a>Quanti dati vengono conservati?
Molti fattori influiscono sulla quantità di dati che vengono mantenuti. Per altre informazioni, vedere la [limiti](../../azure-monitor/app/api-custom-events-metrics.md#limits) sezione della pagina metriche evento del cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Non vedo alcune voci di log previsto
Se l'applicazione invia voluminosa quantità di dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o versioni successive, la funzionalità di campionamento adattivo può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Passaggi successivi

* [Diagnosticare errori ed eccezioni in ASP.NET][exceptions]
* [Altre informazioni sulla ricerca][diagnostic]
* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md