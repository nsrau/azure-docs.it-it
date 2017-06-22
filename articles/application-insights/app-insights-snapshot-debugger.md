---
title: Snapshot Debugger di Azure Application Insights per app .NET | Microsoft Docs
description: Snapshot di debug raccolti automaticamente quando vengono generate eccezioni nelle app di produzione .NET
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Snapshot di debug per le eccezioni nelle app .NET

*Questa funzionalità è in anteprima.*

Quando si verifica un'eccezione, è necessario raccogliere automaticamente uno snapshot di debug dall'applicazione Web live. Lo snapshot mostra lo stato del codice sorgente e delle variabili nel momento in cui è stata generata l'eccezione. Snapshot Debugger di [Application Insights](app-insights-overview.md) consente di monitorare la telemetria delle eccezioni dall'app Web. Raccoglie snapshot per le eccezioni generate più frequentemente in modo che l'utente possa avere a disposizione le informazioni necessarie per diagnosticare i problemi nell'ambiente di produzione. Includere il [pacchetto NuGet Snapshot Collector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'applicazione e configurare facoltativamente i parametri di raccolta in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Gli snapshot vengono visualizzati per le [eccezioni](app-insights-asp-net-exceptions.md) nel portale Application Insights.

È possibile visualizzare gli snapshot di debug nel portale per vedere lo stack di chiamate e ispezionare le variabili in ciascuno stack frame di chiamate. Per eseguire più efficacemente il debug del codice sorgente, aprire gli snapshot con Visual Studio 2017 Enterprise [scaricando l'estensione Snapshot Debugger per Visual Studio](https://aka.ms/snapshotdebugger).

La raccolta di snapshot è disponibile per le app Web ASP.NET eseguite in .NET Framework 4.6 e versioni successive, ospitato su ISS nel Calcolo di Azure o nel Servizio app di Azure.

## <a name="configure-snapshot-collection"></a>Configurare la raccolta di snapshot

1. Se non è ancora stato fatto, [abilitare Application Insights nell'app Web](app-insights-asp-net.md).

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app. 

3. Esaminare le opzioni predefinite che il pacchetto ha aggiunto ad [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

Gli snapshot vengono raccolti solo per le eccezioni visibili all'SDK di Application Insights. In alcuni casi potrebbe essere necessario [configurare la raccolta delle eccezioni](app-insights-asp-net-exceptions.md#exceptions) per visualizzare le eccezioni con snapshot presenti nel portale.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Snapshot di debug nel portale Application Insights

Se è disponibile uno snapshot per una determinata eccezione o un determinato ID problema, viene visualizzato il collegamento *Open Debug Snapshot* (Apri snapshot di debug) per l'[eccezione](app-insights-asp-net-exceptions.md) nel portale Application Insights.

![Pulsante Open Debug Snapshot per l'eccezione](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Nella vista Debug Snapshot (Snapshot di debug) vengono visualizzati uno stack di chiamate e un riquadro delle variabili. La selezione dei frame dello stack di chiamate nel riquadro corrispondente consente di visualizzare le variabili e i parametri locali per la chiamata di funzione specificata nel riquadro delle variabili.

![Visualizzare uno snapshot di debug nel portale](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Gli snapshot possono contenere informazioni sensibili e, per impostazione predefinita, non possono essere visualizzati. Per visualizzare gli snapshot, è necessario richiedere l'assegnazione del ruolo `Application Insights Snapshot Debugger` nel portale per la sottoscrizione o la risorsa. Attualmente questo ruolo può essere assegnato solo dai proprietari di sottoscrizioni ai singoli utenti. L'assegnazione del ruolo ai gruppi Azure Active Directory non è attualmente supportata.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Snapshot di debug con Visual Studio 2017 Enterprise
È possibile fare clic sul pulsante *Download Snapshot* (Scarica snapshot) per scaricare un file `.diagsession`, che può essere aperto con Visual Studio 2017 Enterprise. Per aprire il file `.diagsession`, è attualmente necessario prima [scaricare e installare l'estensione Snapshot Debugger per Visual Studio](https://aka.ms/snapshotdebugger).

Dopo l'apertura del file dello snapshot, viene visualizzata la pagina Minidump Debugging (Debug del minidump) di Visual Studio, in cui è possibile iniziare a eseguire il debug dello snapshot facendo clic su *Debug Managed Code* (Esegui debug del codice gestito). Viene visualizzata la riga del codice in cui è stata generata l'eccezione ed è possibile eseguire il debug dello stato corrente del processo.

![Visualizzare snapshot di debug in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Lo snapshot scaricato contiene tutti file di simboli trovati nel server delle applicazioni Web. Questi file di simboli sono obbligatori per associare i dati degli snapshot al codice sorgente. Per le app di Servizio app di Azure, assicurarsi di abilitare la distribuzione dei simboli al momento della pubblicazione delle app Web.

## <a name="how-snapshots-work"></a>Come funzionano gli snapshot

All'avvio dell'applicazione, viene creato un processo di caricamento degli snapshot separato che esegue il monitoraggio delle richieste di snapshot nell'applicazione. Quando è richiesto uno snapshot, viene creata una copia shadow del processo in esecuzione in circa 10-20 ms. Il processo shadow viene quindi analizzato, poi viene creato uno snapshot mentre il processo principale rimane in esecuzione e garantisce il traffico agli utenti. Lo snapshot viene quindi caricato in Application Insights insieme agli eventuali file di simboli pertinenti (.pdb) necessari per visualizzare lo snapshot.

## <a name="current-limitations"></a>Limitazioni correnti

### <a name="publishing-symbols"></a>Pubblicazione dei simboli
Snapshot Debugger richiede che i file di simboli siano presenti nel server di produzione per decodificare le variabili e offrire un'esperienza di debug in Visual Studio. Per impostazione predefinita, la versione 15.2 di Visual Studio 2017 pubblica i simboli per le compilazioni di rilascio durante la pubblicazione nel Servizio app di Azure. Nelle versioni precedenti è necessario aggiungere la riga seguente al file di profilo `.pubxml` da pubblicare per pubblicare i simboli in modalità di rilascio.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Per Calcolo di Azure e altri tipi di calcoli, verificare che i file di simboli si trovino nella stessa cartella dell'applicazione .dll principale (generalmente `wwwroot/bin`) o che siano disponibili nel percorso corrente.

### <a name="optimized-builds"></a>Compilazioni ottimizzate
In alcuni casi le variabili locali non possono essere visualizzate nelle compilazioni di rilascio a causa delle ottimizzazioni applicate durante il processo di compilazione. Questa limitazione verrà corretta in un rilascio futuro del pacchetto NuGet.

## <a name="next-steps"></a>Passaggi successivi

* L'articolo [Diagnosticare eccezioni nelle app Web](app-insights-asp-net-exceptions.md) spiega come rendere visibile un maggior numero di eccezioni in Application Insights. 
* Il [rilevamento intelligente](app-insights-proactive-diagnostics.md) rileva automaticamente le anomalie delle prestazioni.

