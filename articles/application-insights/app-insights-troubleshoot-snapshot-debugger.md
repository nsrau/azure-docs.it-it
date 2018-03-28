---
title: Guida per la risoluzione dei problemi del debugger di snapshot di Azure Application Insights | Microsoft Docs
description: Domande frequenti sul debugger di snapshot di Azure Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2b4a5f548578b563c92f8d7ff85457b50b02fbd4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Debugger di snapshot: guida per la risoluzione dei problemi

Il debugger di snapshot di Application Insights consente di raccogliere automaticamente uno snapshot di debug da applicazioni Web live. Lo snapshot mostra lo stato del codice sorgente e delle variabili nel momento in cui è stata generata un'eccezione. Questo articolo illustra il funzionamento del debugger e descrive le soluzioni ai problemi più comuni.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Funzionamento del debugger di snapshot di Application Insights

Application Insights Snapshot Debugger fa parte della pipeline di dati di telemetria di Application Insights (istanza di ITelemetryProcessor). L'agente di raccolta snapshot consente di monitorare sia le eccezioni generate nel codice (AppDomain.FirstChanceException) che i dati di telemetria delle eccezioni segnalati ad Application Insights tramite `TelemetryClient.TrackException`. Dopo che l'agente di raccolta snapshot è stato aggiunto correttamente al progetto e ha rilevato un evento nella pipeline dei dati di telemetria, verrà inviato un evento personalizzato con il nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotCollectorEnabled' nei dati personalizzati. Allo stesso tempo, verrà avviato un processo denominato 'SnapshotUploader.exe' (versione 1.2.0 o versione successiva) o 'MinidumpUploader.exe' (prima della versione 1.2.0), per caricare i file di dati di snapshot raccolti in Application Insights.  All'avvio del processo di caricamento, viene inviato un evento personalizzato con il nome 'UploaderStart'. Verrà poi ripristinato il normale comportamento di monitoraggio dell'agente di raccolta snapshot.

Durante il monitoraggio della telemetria delle eccezioni di Application Insights, l'agente di raccolta snapshot usa i parametri (ad esempio, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) definiti nella configurazione per stabilire quando raccogliere uno snapshot. Quando vengono soddisfatte tutte le regole, l'agente di raccolta richiederà uno snapshot per l'eccezione successiva generata nella stessa posizione. Contemporaneamente, verrà inviato un evento personalizzato di Application Insights con il nome 'AppInsightsSnapshotCollectorLogs' e 'RequestSnapshots'. Poiché il compilatore ottimizzerà il codice di rilascio, le variabili locali potrebbero non essere visibili nello snapshot raccolto. Quando vengono richiesti gli snapshot, l'agente di raccolta snapshot tenterà di deottimizzare il metodo che ha generato l'eccezione. Durante questo periodo, un evento personalizzato di Application Insights con il nome 'AppInsightsSnapshotCollectorLogs' e 'ProductionBreakpointsDeOptimizeMethod' verrà inviato nei dati personalizzati.  Quando viene raccolto lo snapshot dell'eccezione successiva, le variabili locali saranno disponibili. Dopo la raccolta dello snapshot, il codice verrà riottimizzato. 

> [!NOTE]
> La deottimizzazione richiede che sia installata l'estensione del sito di Application Insights.

Quando viene richiesto uno snapshot per un'eccezione specifica, l'agente di raccolta snapshot inizierà a monitorare la pipeline di gestione delle eccezioni dell'applicazione (AppDomain.FirstChanceException). Quando l'eccezione si verifica nuovamente, l'agente di raccolta avvierà uno snapshot (evento personalizzato di Application Insights con il nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStart' nei dati personalizzati). Verrà quindi eseguita una copia shadow del processo in esecuzione (la tabella della pagina sarà duplicata). Questa operazione richiederà in genere 10-20 millisecondi. Al termine, verrà inviato un evento personalizzato di Application Insights con il nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStop' nei dati personalizzati. Quando viene creato il processo con fork, la memoria di paging totale verrà aumentata della stessa quantità della memoria di paging dell'applicazione in esecuzione (il working set sarà molto più piccolo). Mentre il processo dell'applicazione è in esecuzione normalmente, verrà eseguito il dump su disco della memoria del processo nella copia shadow e la memoria sarà caricata in Application Insights. Al termine del caricamento dello snapshot, verrà inviato un evento personalizzato di Application Insights con il nome 'UploadSnapshotFinish'.

## <a name="is-the-snapshot-collector-working-properly"></a>L'agente di raccolta snapshot funziona correttamente?

### <a name="how-to-find-snapshot-collector-logs"></a>Come individuare i log dell'agente di raccolta snapshot
I log dell'agente di raccolta snapshot vengono inviati all'account di Application Insights, se è disponibile il [pacchetto NuGet dell'agente di raccolta snapshot](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) versione 1.1.0 o successiva. Assicurarsi che *ProvideAnonymousTelemetry* non sia impostato su false (il valore è true per impostazione predefinita).

* Passare alla risorsa di Application Insights nel portale di Azure.
* Fare clic su *Cerca* nella sezione Panoramica.
* Immettere la stringa seguente nella casella di ricerca:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Nota: modificare il valore in *Intervallo di tempo* se necessario.

![Schermata di ricerca dei log dell'agente di raccolta snapshot](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Esaminare i log dell'agente di raccolta snapshot
Quando si esegue una ricerca dei log dell'agente di raccolta snapshot, nell'intervallo di tempo preso in considerazione dovrebbero essere presenti eventi di tipo 'UploadSnapshotFinish'. Se il pulsante "Apri snapshot di debug" per l'apertura dello snapshot non è ancora visualizzato, inviare un messaggio di posta elettronica all'indirizzo snapshothelp@microsoft.com con la chiave di strumentazione di Application Insights.

![Schermata di analisi dei log dell'agente di raccolta snapshot](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Non è possibile trovare uno snapshot da aprire
Se i passaggi consigliati di seguito non consentono di risolvere il problema, inviare un messaggio di posta elettronica all'indirizzo snapshothelp@microsoft.com con la chiave di strumentazione di Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Passaggio 1: assicurarsi che l'applicazione invii i dati di telemetria e i dati sull'eccezione ad Application Insights
Passare alla risorsa di Application Insights e verificare la presenza di dati inviati dall'applicazione.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Passaggio 2: assicurarsi che l'agente di raccolta snapshot venga aggiunto correttamente alla pipeline di Application Insights Telemetry dell'applicazione
Se è possibile trovare i log nel passaggio "Come individuare i log dell'agente di raccolta snapshot", l'agente di raccolta snapshot è aggiunto correttamente al progetto ed è possibile ignorare questo passaggio.

Se non è presente alcun log dell'agente di raccolta snapshot, verificare quanto segue:
* Per le applicazioni ASP.NET classiche, cercare questa riga *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* nel file *ApplicationInsights.config*.

* Per le applicazioni ASP.NET Core, assicurarsi che *ITelemetryProcessorFactory* con *SnapshotCollectorTelemetryProcessor* sia aggiunto ai servizi *IServiceCollection*.

* Verificare inoltre di usare la chiave di strumentazione corretta nell'applicazione pubblicata.

* L'agente di raccolta snapshot non supporta più chiavi di strumentazione all'interno di un'applicazione, ma invierà snapshot alla chiave di strumentazione della prima eccezione che viene osservata.

* Se si imposta *InstrmentationKey* manualmente nel codice, aggiornare l'elemento *InstrumentationKey* da *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Passaggio 3: assicurarsi che l'utilità di caricamento del minidump sia avviata
Nei log dell'agente di raccolta snapshot cercare *UploaderStart* (digitare UploaderStart nella casella di testo di ricerca). Dovrebbe esserci un evento quando l'agente di raccolta snapshot ha monitorato la prima eccezione. Se questo evento non esiste, verificare altri log per i dettagli. Una delle possibili soluzioni per questo problema consiste nel riavvio dell'applicazione.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Passaggio 4: assicurarsi che l'agente di raccolta snapshot abbia espresso l'intento di raccogliere snapshot
Nei log dell'agente di raccolta snapshot cercare *RequestSnapshots* (digitare ```RequestSnapshots``` nella casella di testo di ricerca).  Se non è presente, controllare la configurazione. Ad esempio *ThresholdForSnapshotting*, che indica il numero di una specifica eccezione che può verificarsi prima che abbia inizio la raccolta di snapshot.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Passaggio 5: assicurarsi che lo snapshot non sia disabilitato a causa della protezione della memoria
Per proteggere le prestazioni dell'applicazione, uno snapshot verrà acquisito solo nel caso di un buffer di memoria consistente. Nei log dell'agente di raccolta snapshot cercare 'CannotSnapshotDueToMemoryUsage'. Nei dati personalizzati dell'evento sarà presente un motivo dettagliato. Se l'applicazione è in esecuzione in un'app Web di Azure, la restrizione può essere rigorosa. Poiché l'app Web di Azure riavvierà l'app quando vengono soddisfatte determinate regole di memoria, è possibile provare ad aumentare il piano di servizio includendo computer con più memoria per risolvere questo problema.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Passaggio 6: assicurarsi che gli snapshot siano stati acquisiti
Nei log dell'agente di raccolta snapshot cercare ```RequestSnapshots```.  Se non è presente, controllare la configurazione. Ad esempio *ThresholdForSnapshotting*, che indica il numero di un'eccezione specifica prima della raccolta di uno snapshot.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Passaggio 7: assicurarsi che gli snapshot siano caricati correttamente
Nei log dell'agente di raccolta snapshot cercare ```UploadSnapshotFinish```.  Se non è presente, inviare un messaggio di posta elettronica all'indirizzo snapshothelp@microsoft.com con la chiave di strumentazione di Application Insights. Se questo evento esiste, aprire uno dei log e copiare il valore 'SnapshotId' nei dati personalizzati, quindi cercare il valore. In questo modo verrà trovata l'eccezione corrispondente allo snapshot. Fare clic sull'eccezione e aprire lo snapshot di debug (Se non è presente alcuna eccezione corrispondente, la telemetria delle eccezioni può essere campionata, a causa dell'elevato volume. Provare un altro oggetto snapshotId.)

![Schermata di ricerca di SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Schermata di apertura dell'eccezione](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Schermata di apertura dello snapshot di debug](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Visualizzazione snapshot: le variabili locali non sono complete

Alcune delle variabili locali sono mancanti. Se l'applicazione esegue codice di rilascio, il compilatore procederà all'ottimizzazione di alcune variabili, Ad esempio: 

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Se lo scenario è diverso, potrebbe trattarsi di un bug. Inviare un messaggio di posta elettronica all'indirizzo snapshothelp@microsoft.com con la chiave di strumentazione di Application Insights e il frammento di codice.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Visualizzazione snapshot: non è possibile ottenere il valore dell'argomento o della variabile locale
Assicurarsi che l'[estensione del sito di Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) sia installata. Se il problema persiste, inviare un messaggio di posta elettronica all'indirizzo snapshothelp@microsoft.com con la chiave di strumentazione di Application Insights.
