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
ms.openlocfilehash: 125f1bc14a376523a22984e9d8efa7848408bf7a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035211"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Esplorare i log di traccia .NET/.NET Core in Application Insights

Inviare i log di traccia di diagnostica per l'applicazione ASP.NET/ASP.NET Core da ILogger, NLog, log4Net o System. Diagnostics. Trace per [applicazione Azure informazioni dettagliate][start]. Sarà quindi possibile esplorarli e cercarli. Questi log vengono uniti con gli altri file di log dell'applicazione, pertanto è possibile identificare le tracce associate a ogni richiesta utente e metterle in correlazione con altri eventi e report di eccezioni.

> [!NOTE]
> È necessario il modulo di acquisizione log? Si tratta di un adattatore utile per i logger di terze parti. Tuttavia, se non si usa già NLog, log4Net o System. Diagnostics. Trace, è sufficiente chiamare direttamente [**Application Insights TrackTrace ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) .
>
>
## <a name="install-logging-on-your-app"></a>Installare la registrazione nell'applicazione
Installare il Framework di registrazione scelto nel progetto, che dovrebbe causare una voce in app. config o Web. config.

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
Se non è ancora stato fatto, [aggiungere Application Insights al progetto](../../azure-monitor/app/asp-net.md) . Verrà visualizzata un'opzione per includere la raccolta dei log.

In alternativa, fare clic con il pulsante destro del mouse sul progetto Esplora soluzioni per **configurare Application Insights**. Selezionare l'opzione **Configura raccolta di tracce** .

> [!NOTE]
> Nessun menu Application Insights o opzione raccolta log? Vedere [Risoluzione dei problemi](#troubleshooting).

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

Il pacchetto NuGet installa gli assembly necessari e modifica Web. config o app. config se applicabile.

## <a name="ilogger"></a>ILogger

Per esempi relativi all'uso dell'implementazione Application Insights ILogger con le applicazioni console e ASP.NET Core, vedere [ApplicationInsightsLoggerProvider per i log ILogger di .NET Core](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserire chiamate di log di diagnostica
Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog, usare:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>USA eventi EventSource
È possibile configurare eventi [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **Nome** specifica il nome del EventSource da raccogliere.
 * **Level** specifica il livello di registrazione da raccogliere: *Critical*, *Error*, *Informational*, *LogAlways*, *verbose*o *warning*.
 * **Parole chiave** (facoltativo) specificare il valore intero delle combinazioni di parole chiave da usare.

## <a name="use-diagnosticsource-events"></a>USA eventi DiagnosticSource
È possibile configurare eventi [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Per ogni DiagnosticSource che si vuole tracciare, aggiungere una voce con l'attributo **Name** impostato sul nome del DiagnosticSource.

## <a name="use-etw-events"></a>USA eventi ETW
È possibile configurare gli eventi di Event Tracing for Windows (ETW) da inviare al Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EtwCollector`. Modificare quindi la sezione "TelemetryModules" del file [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> Gli eventi ETW possono essere raccolti solo se il processo che ospita l'SDK viene eseguito con un'identità che è membro di utenti o amministratori di log delle prestazioni.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * **ProviderName** è il nome del provider ETW da raccogliere.
 * **ProviderGuid** specifica il GUID del provider ETW da raccogliere. Può essere usato al posto di `ProviderName`.
 * **Level** imposta il livello di registrazione da raccogliere. Può essere *Critical*, *Error*, *Informational*, *LogAlways*, *verbose*o *warning*.
 * **Parole chiave** (facoltativo) impostare il valore intero delle combinazioni di parole chiave da usare.

## <a name="use-the-trace-api-directly"></a>Usare direttamente l'API Trace
È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio è possibile codificare dati POST.

È anche possibile aggiungere un livello di gravità al messaggio. Analogamente ad altri dati di telemetria, è possibile aggiungere valori di proprietà per filtrare o cercare set di tracce diversi. Esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Ciò consente di filtrare facilmente in [Cerca][diagnostic] tutti i messaggi di un determinato livello di gravità correlati a un determinato database.

## <a name="explore-your-logs"></a>Esplorare i log
Eseguire l'app in modalità di debug o distribuirla in tempo reale.

Nel riquadro della panoramica dell'app nel [portale di Application Insights][portal]selezionare [Cerca][diagnostic].

Ad esempio, è possibile:

* Filtrare in base alle tracce di log o agli elementi con proprietà specifiche.
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri dati del registro di sistema che si riferiscono alla stessa richiesta utente (ha lo stesso OperationId).
* Salva la configurazione di una pagina come preferita.

> [!NOTE]
>Se l'applicazione invia una grande quantità di dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o successiva, la funzionalità di campionamento adattivo può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="how-do-i-do-this-for-java"></a>Come procedere per Java?
Usare gli [adattatori log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Non è disponibile alcuna opzione di Application Insights nel menu di scelta rapida del progetto
* Assicurarsi che Developer Analytics Tools sia installato nel computer di sviluppo. In Visual Studio **Tools** > **extensions and Updates**cercare **Developer Analytics Tools**. Se la scheda non è **installata** , aprire la scheda **online** e installarla.
* Potrebbe trattarsi di un tipo di progetto non supportato dagli strumenti di analisi di Devloper. Usare l' [installazione manuale](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Non è disponibile alcuna opzione per l'adapter di log nello strumento di configurazione
* Installare prima il Framework di registrazione.
* Se si usa System. Diagnostics. Trace, verificare che sia [configurato in *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Assicurarsi di disporre della versione più recente di Application Insights. In Visual Studio passare a **strumenti** > **estensioni e aggiornamenti**e aprire la scheda **aggiornamenti** . Se **Developer Analytics Tools** è presente, selezionarlo per aggiornarlo.

### <a name="emptykey"></a>Viene ricevuto il messaggio di errore "la chiave di strumentazione non può essere vuota"
È probabile che sia stato installato il pacchetto NuGet dell'adapter di registrazione senza installare Application Insights. In Esplora soluzioni fare clic con il pulsante destro del mouse su *ApplicationInsights. config*e selezionare **Aggiorna Application Insights**. Verrà richiesto di accedere ad Azure e creare una risorsa Application Insights o riutilizzarne una esistente. Questo dovrebbe risolvere il problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>È possibile visualizzare tracce ma non altri eventi nella ricerca diagnostica
La pipeline può richiedere un po' di tempo per tutti gli eventi e le richieste.

### <a name="limits"></a>Quanti dati vengono conservati?
Diversi fattori influiscono sulla quantità di dati conservati. Per ulteriori informazioni, vedere la sezione [limiti](../../azure-monitor/app/api-custom-events-metrics.md#limits) della pagina metrica eventi del cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Non vedo alcune voci di log previste
Se l'applicazione invia enormi quantità di dati e si usa Application Insights SDK per ASP.NET versione 2.0.0-beta3 o successiva, la funzionalità di campionamento adattivo può funzionare e inviare solo una parte dei dati di telemetria. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)

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