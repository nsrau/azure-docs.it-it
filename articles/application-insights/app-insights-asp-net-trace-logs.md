---
title: Esplorare i log di traccia .NET in Application Insights
description: Cercare i log generati con Trace, NLog o Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 6da0bf009fa71885d7d8e3bd5376c5a7c9d4a344
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Esplorare i log di traccia .NET in Application Insights
Se si usa NLog, log4Net o System.Diagnostics.Trace per l'analisi diagnostica nell'applicazione ASP.NET, è possibile fare in modo che i log vengano inviati a [Application Insights di Azure][start], dove è possibile esplorarli ed eseguirvi ricerche. I log verranno uniti con gli altri eventi di telemetria provenienti dall'applicazione, in modo da potere identificare le tracce associate alla gestione di ogni richiesta dell'utente e metterle in correlazione con altri eventi e i report di eccezioni.

> [!NOTE]
> Se è necessario un modulo di acquisizione dei log, questo adattatore è utile per i logger terze parti, ma se NLog, log4Net o System.Diagnostics.Trace non sono già in uso, chiamare direttamente [TrackTrace() di Application Insights](app-insights-api-custom-events-metrics.md#tracktrace).
>
>

## <a name="install-logging-on-your-app"></a>Installare la registrazione nell'applicazione
Installare il framework di registrazione scelto nel progetto. Verrà inserita una voce nel file app.config o web.config.

Se si usa System.Diagnostics.Trace, è necessario aggiungere una voce a web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Configurare Application Insights per la raccolta dei log
Se non è ancora stato fatto, **[aggiungere Application Insights al progetto](app-insights-asp-net.md)**. Verrà visualizzata un'opzione per includere la raccolta dei log.

In alternativa, **configurare Application Insights** facendo clic con il pulsante destro del mouse in Esplora soluzioni. Selezionare l'opzione per **configurare la raccolta delle tracce**.

*Il menu di Application Insights o  l'opzione di raccolta non viene visualizzata?* Vedere [Risoluzione dei problemi](#troubleshooting).

## <a name="manual-installation"></a>Installazione manuale
Usare questo metodo se il tipo di progetto non è supportato dal programma di installazione di Application Insights, ad esempio un progetto Desktop di Windows.

1. Se si intende usare log4Net o NLog, installarlo nel progetto.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
3. Cercare "Application Insights"
4. Selezionare il pacchetto appropriato tra:

   * Microsoft.ApplicationInsights.TraceListener (per acquisire le chiamate System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.EventSourceListener, per acquisire gli eventi EventSource
   * Microsoft.ApplicationInsights.EtwListener, per acquisire gli eventi ETW
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Il pacchetto NuGet installa gli assembly necessari e modifica inoltre web.config o app.config.

## <a name="insert-diagnostic-log-calls"></a>Inserire chiamate di log di diagnostica
Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Uso degli eventi EventSource
È possibile configurare eventi [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * `Name` specifica il nome di EventSource da raccogliere.
 * `Level` specifica il livello di registrazione da raccogliere. Può essere uno tra `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` è facoltativo e specifica il valore intero di combinazioni di parole chiave da usare.

## <a name="using-diagnosticsource-events"></a>Uso degli eventi DiagnosticSource
È possibile configurare eventi [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnsoticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Per ogni DiagnosticSource che si desidera tracciare, aggiungere una voce con l'attributo `Name` impostato sul nome di DiagnosticSource.

## <a name="using-etw-events"></a>Uso degli eventi ETW
È possibile configurare eventi ETW da inviare ad Application Insights come tracce. Installare innanzitutto il pacchetto NuGet `Microsoft.ApplicationInsights.EtwCollector`. Quindi modificare la sezione `TelemetryModules` del file [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Gli eventi ETW possono essere raccolti solo se il processo che ospita l'SDK viene eseguito in un'identità che è membro di "Performance Log Users" o amministratori.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Per ogni origine è possibile impostare i parametri seguenti:
 * `ProviderName` è il nome del provider ETW da raccogliere.
 * `ProviderGuid` specifica il GUID del provider ETW da raccogliere, può essere usato al posto di `ProviderName`.
 * `Level` imposta il livello di registrazione da raccogliere. Può essere uno tra `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` è facoltativo e imposta il valore intero di combinazioni di parole chiave da usare.

## <a name="using-the-trace-api-directly"></a>Uso diretto dell'API di traccia
È possibile chiamare direttamente l'API di traccia di Application Insights. Gli adattatori di registrazione usano questa API.

Ad esempio: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio, è possibile codificare dati POST.

È anche possibile aggiungere al messaggio un livello di gravità. E come per altri tipi di dati di telemetria, si possono aggiungere valori di proprietà che è possibile usare per filtrare o cercare set di tracce diversi, Ad esempio: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Questo consentirà, in [Ricerca][diagnostic], di filtrare facilmente tutti i messaggi di un determinato livello di gravità relativi a un database specifico.

## <a name="explore-your-logs"></a>Esplorare i log
Eseguire l'app in modalità debug o distribuirla.

Nel pannello Panoramica dell'app nel [portale di Application Insights][portal] scegliere [Cerca][diagnostic].

![In Application Insights scegliere Cerca](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Ricerca](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Ad esempio, è possibile:

* Filtrare in base alle tracce dei log o agli elementi con proprietà specifiche
* Esaminare un elemento specifico in modo dettagliato
* Trovare altri eventi di telemetria relativi alla stessa richiesta dell'utente (ovvero, con lo stesso valore OperationId)
* Salvare la configurazione di questa pagina come preferita

> [!NOTE]
> **Campionamento.** Se l'applicazione invia una grande quantità di dati e si sta utilizzando la versione 2.0.0-beta3 o versioni successive dell’SDK di Application Insights per ASP.NET, la funzionalità del campionamento adattivo può operare e inviare solo una percentuale dei dati di telemetria. [Altre informazioni sul campionamento.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Passaggi successivi
[Diagnosticare errori ed eccezioni in ASP.NET][exceptions]

[Altre informazioni sulla ricerca][diagnostic].

## <a name="troubleshooting"></a>risoluzione dei problemi
### <a name="how-do-i-do-this-for-java"></a>Come procedere per Java?
Usare gli [adattatori log Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Non è disponibile alcuna opzione di Application Insights nel menu di scelta rapida del progetto
* Verificare che Strumenti Application Insights sia installato nel computer di sviluppo. In Visual Studio, scegliere Strumenti, Estensioni e Aggiornamenti e cercare Strumenti Application Insights. Se non è visualizzato nella scheda degli elementi installati, aprire la scheda Online e installarlo.
* Potrebbe trattarsi di un tipo di progetto non supportato da Strumenti Application Insights. Usare l' [installazione manuale](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Nello strumento di configurazione non è disponibile alcuna opzione per l'adattatore log
* È necessario installare innanzitutto il framework di registrazione.
* Se si usa System.Diagnostics.Trace, verificare di [aver eseguito la configurazione in `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Si dispone della versione più recente di Application Insights? Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti** di Visual Studio e aprire la scheda **Aggiornamenti**. Se Developer Analytics Tools è presente, fare clic per eseguire l'aggiornamento.

### <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"
Risulta che l'utente abbia installato il pacchetto NuGet dell'adattatore di registrazione senza aver installato Application Insights.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa di Application Insights o a riusarne una esistente. Il problema verrà in tal modo risolto.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Nella ricerca diagnostica vengono visualizzate le tracce ma non gli altri eventi
Talvolta la visualizzazione di tutti gli eventi e le richieste nella pipeline può richiedere un po' di tempo.

### <a name="limits"></a>Quanti dati vengono conservati?
Diversi fattori influiscono sulla quantità di dati mantenuti. Per altre informazioni, vedere la sezione dei [limiti](app-insights-api-custom-events-metrics.md#limits) della pagina delle metriche degli eventi dei clienti. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Non è possibile vedere alcune delle voci di log previste
Se l'applicazione invia una grande quantità di dati e si sta utilizzando la versione 2.0.0-beta3 o versioni successive dell’SDK di Application Insights per ASP.NET, la funzionalità del campionamento adattivo può operare e inviare solo una percentuale dei dati di telemetria. [Altre informazioni sul campionamento.](app-insights-sampling.md)

## <a name="add"></a>Passaggi successivi
* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
