---
title: Snapshot Debugger di Azure Application Insights per app .NET | Microsoft Docs
description: Gli snapshot di debug vengono raccolti automaticamente quando vengono generate eccezioni nelle app di produzione .NET
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5a0344dcef779a9818be3e320bd5c269a2859f71
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Snapshot di debug per le eccezioni nelle app .NET

Quando si verifica un'eccezione, è possibile raccogliere automaticamente uno snapshot di debug dall'applicazione Web live. Lo snapshot mostra lo stato del codice sorgente e delle variabili nel momento in cui è stata generata l'eccezione. Snapshot Debugger (anteprima) di [Azure Application Insights](app-insights-overview.md) monitora la telemetria delle eccezioni dall'app Web. Raccoglie snapshot per le eccezioni generate più frequentemente in modo che l'utente possa avere le informazioni necessarie per diagnosticare i problemi nell'ambiente di produzione. Includere il [pacchetto NuGet Snapshot Collector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'applicazione e configurare facoltativamente i parametri di raccolta in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Gli snapshot vengono visualizzati per le [eccezioni](app-insights-asp-net-exceptions.md) nel portale Application Insights.

È possibile visualizzare gli snapshot di debug nel portale per vedere lo stack di chiamate e ispezionare le variabili in ogni stack frame di chiamate. Per eseguire più efficacemente il debug del codice sorgente, aprire gli snapshot con Visual Studio 2017 Enterprise [scaricando l'estensione Snapshot Debugger per Visual Studio](https://aka.ms/snapshotdebugger). In Visual Studio è anche possibile [impostare punti di ancoraggio per creare in modo interattivo snapshot](https://aka.ms/snappoint) senza attendere un'eccezione.

La raccolta di snapshot è disponibile per:
* Applicazioni .NET Framework e ASP.NET che eseguono .NET Framework 4.5 o versione successiva.
* Applicazioni .NET Core 2.0 e ASP.NET Core 2.0 in esecuzione in Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET

1. [Abilitare Application Insights nell'app Web](app-insights-asp-net.md) se non è ancora stato fatto.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app. 

3. Esaminare le opzioni predefinite che il pacchetto ha aggiunto ad [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

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

4. Gli snapshot vengono raccolti solo per le eccezioni segnalate ad Application Insights. In alcuni casi (ad esempio, versioni precedenti della piattaforma .NET), potrebbe essere necessario [configurare la raccolta di eccezioni](app-insights-asp-net-exceptions.md#exceptions) per visualizzare le eccezioni con gli snapshot nel portale.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET Core 2.0

1. [Abilitare Application Insights nell'app Web ASP.NET Core](app-insights-asp-net-core.md) se non è ancora stato fatto.

> [!NOTE]
> Verificare che l'applicazione faccia riferimento alla versione 2.1.1 o più recente del pacchetto Microsoft.ApplicationInsights.AspNetCore.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Modificare la classe `Startup` dell'applicazione per aggiungere e configurare il processore di telemetria dell'agente di raccolta snapshot.

   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
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

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

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

4. Configurare l'agente di raccolta snapshot aggiungendo una sezione SnapshotCollectorConfiguration ad appsettings.json. ad esempio:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurare la raccolta di snapshot per le altre applicazioni .NET

1. Se l'applicazione non è già instrumentata con Application Insights, iniziare [abilitando Application Insights e impostando la chiave di strumentazione](app-insights-windows-desktop.md).

2. Aggiungere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

3. Gli snapshot vengono raccolti solo per le eccezioni segnalate ad Application Insights. Potrebbe essere necessario modificare il codice per segnalarle. Anche se il codice di gestione delle eccezioni dipende dalla struttura dell'applicazione, di seguito è riportato un esempio:
    ```C#
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
    
## <a name="grant-permissions"></a>Concedere le autorizzazioni

Gli snapshot possono essere ispezionati dai proprietari della sottoscrizione di Azure. Agli altri utenti deve essere concessa l'autorizzazione da un proprietario.

Per concedere l'autorizzazione, assegnare il ruolo `Application Insights Snapshot Debugger` agli utenti che ispezioneranno gli snapshot. Questo ruolo può essere assegnato a singoli utenti o gruppi dai proprietari della sottoscrizione per la risorsa di Application Insights di destinazione oppure per il gruppo di risorse o la sottoscrizione di tale risorsa.

1. Aprire il pannello Controllo di accesso (IAM).
1. Fare clic sul pulsante +Aggiungi.
1. Selezionare Debugger di snapshot di Application Insights nell'elenco a discesa Ruoli.
1. Cercare e immettere un nome per l'utente da aggiungere.
1. Fare clic sul pulsante Salva per aggiungere l'utente al ruolo.


[!IMPORTANT]
    Gli snapshot possono contenere informazioni personali e altre informazioni riservate nei valori delle variabili e dei parametri.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Snapshot di debug nel portale di Application Insights

Se è disponibile uno snapshot per una determinata eccezione o un determinato ID problema, viene visualizzato il pulsante **Open Debug Snapshot** (Apri snapshot di debug) per l'[eccezione](app-insights-asp-net-exceptions.md) nel portale di Application Insights.

![Pulsante Open Debug Snapshot per l'eccezione](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Nella vista Debug Snapshot (Snapshot di debug) vengono visualizzati uno stack di chiamate e un riquadro delle variabili. Quando si selezionano frame dello stack di chiamate nel riquadro corrispondente, è possibile visualizzare i parametri e le variabili locali per la chiamata di funzione nel riquadro delle variabili.

![Visualizzare uno snapshot di debug nel portale](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Gli snapshot potrebbero contenere informazioni riservate e per impostazione predefinita non sono visibili. Per visualizzare gli snapshot, è necessario che all'utente sia stato assegnato il ruolo `Application Insights Snapshot Debugger`.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Snapshot di debug con Visual Studio 2017 Enterprise
1. Fare clic sul pulsante **Download Snapshot** (Scarica snapshot) per scaricare un file `.diagsession` che può essere aperto con Visual Studio 2017 Enterprise. 

2. Per aprire il file `.diagsession`, è necessario prima di tutto [scaricare e installare l'estensione Snapshot Debugger per Visual Studio](https://aka.ms/snapshotdebugger).

3. Quando si apre il file di snapshot, in Visual Studio viene visualizzata la pagina per il debug di minidump. Fare clic su **Debug Managed Code** (Debug codice gestito) per avviare il debug dello snapshot. Lo snapshot viene aperto alla riga di codice in cui è stata generata l'eccezione in modo da consentire il debug dello stato corrente del processo.

    ![Visualizzare lo snapshot di debug in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Lo snapshot scaricato contiene tutti file di simboli trovati nel server applicazioni Web. Questi file di simboli sono necessari per associare i dati degli snapshot al codice sorgente. Per le app del servizio app, assicurarsi di abilitare la distribuzione dei simboli al momento della pubblicazione delle app Web.

## <a name="how-snapshots-work"></a>Funzionamento degli snapshot

All'avvio dell'applicazione viene creato un processo di caricamento degli snapshot separato che monitora le richieste di snapshot nell'applicazione. Quando viene richiesto uno snapshot, viene creata una copia shadow del processo in esecuzione in circa 10-20 millisecondi. Il processo shadow viene quindi analizzato e viene creato uno snapshot mentre il processo principale rimane in esecuzione e continua a gestire il traffico verso gli utenti. Lo snapshot viene quindi caricato in Application Insights insieme agli eventuali file di simboli pertinenti (con estensione pdb) che sono necessari per visualizzare lo snapshot.

## <a name="current-limitations"></a>Limitazioni correnti

### <a name="publish-symbols"></a>Pubblicare i simboli
Per poter decodificare le variabili e offrire un'esperienza di debug in Visual Studio, Snapshot Debugger richiede la presenza dei file di simboli nel server di produzione. Per impostazione predefinita, la versione 15.2 di Visual Studio 2017 pubblica i simboli per le build di versione durante la pubblicazione nel servizio app. Nelle versioni precedenti è necessario aggiungere la riga seguente al file `.pubxml` del profilo di pubblicazione per pubblicare i simboli in modalità versione:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Per Calcolo di Azure e altri tipi di calcoli, verificare che i file di simboli si trovino nella stessa cartella della DLL dell'applicazione principale (generalmente `wwwroot/bin`) o che siano disponibili nel percorso corrente.

### <a name="optimized-builds"></a>Compilazioni ottimizzate
In alcuni casi le variabili locali non possono essere visualizzate nelle build di versione a causa delle ottimizzazioni applicate durante il processo di compilazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questi suggerimenti consentono di risolvere i problemi relativi al debugger di snapshot.

### <a name="verify-the-instrumentation-key"></a>Verificare la chiave di strumentazione

Verificare di usare la chiave di strumentazione corretta nell'applicazione pubblicata. Application Insights in genere legge la chiave di strumentazione dal file ApplicationInsights.config. Verificare che il valore sia lo stesso della chiave di strumentazione per la risorsa di Application Insights visualizzata nel portale.

### <a name="check-the-uploader-logs"></a>Controllare i log dell'utilità di caricamento

Dopo la creazione di uno snapshot, un file di minidump (DMP) viene creato sul disco. Un processo di caricamento separato prende il file di minidump e lo carica, con i file PDB associati, nella risorsa di archiviazione Debugger di snapshot di Application Insights. Il minidump, dopo essere stato correttamente caricato, viene eliminato dal disco. I file di log dell'utilità di caricamento del minidump vengono conservati sul disco. In un ambiente del servizio app questi log si trovano in `D:\Home\LogFiles\Uploader_*.log`. Usare il sito di gestione di Kudu per il servizio app per trovare questi file di log.

1. Aprire l'applicazione del servizio app nel portale di Azure.

2. Selezionare il pannello **Strumenti avanzati** o cercare **Kudu**.
3. Fare clic su **Vai**.
4. Nell'elenco a discesa **Console di debug** selezionare **CMD**.
5. Fare clic su **LogFiles**.

Verrà visualizzato almeno un file con il nome che inizia con `Uploader_` e l'estensione `.log`. Fare clic sull'icona appropriata per scaricare i file di log o aprirli in un browser.
Il nome file include il nome del computer. Se l'istanza del servizio app è ospitata in più di un computer, è presente un file di log separato per ogni computer. Quando l'utilità di caricamento rileva un nuovo file di minidump file, il file viene registrato nel file di log. Ecco un esempio di caricamento corretto:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

Nell'esempio precedente la chiave di strumentazione è `c12a605e73c44346a984e00000000000`. Questo valore deve corrispondere alla chiave di strumentazione dell'applicazione.
Il minidump è associato a uno snapshot con l'ID `139e411a23934dc0b9ea08a626db16c5`. Sarà possibile usare questo ID in seguito per individuare i dati di telemetria delle eccezioni associati in Application Insights Analytics.

L'utilità di caricamento cerca i nuovi file PDB ogni 15 minuti circa. Ad esempio:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Per le applicazioni _non_ ospitate nel servizio app, i log di caricamento sono nella stessa cartella dei minidump: `%TEMP%\Dumps\<ikey>` (dove `<ikey>` è la chiave di strumentazione).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usare la ricerca di Application Insights per trovare le eccezioni con gli snapshot

Quando viene creato uno snapshot, l'eccezione generata viene contrassegnata con un ID snapshot. Quando i dati di telemetria dell'eccezione vengono segnalati ad Application Insights, tale ID snapshot viene incluso come proprietà personalizzata. Usando il pannello Ricerca in Application Insights, è possibile trovare tutti i dati di telemetria con la proprietà personalizzata `ai.snapshot.id`.

1. Passare alla risorsa di Application Insights nel portale di Azure.
2. Fare clic su **Search**(Cerca).
3. Digitare `ai.snapshot.id` nella casella di testo di ricerca e premere INVIO.

![Cercare i dati di telemetria con i ID snapshot nel portale](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Se questa ricerca non restituisce risultati, significa che nessuno snapshot è stato segnalato ad Application Insights per l'applicazione nell'intervallo di tempo selezionato.

Per cercare uno specifico ID snapshot dei log di caricamento, digitare tale ID nella casella di ricerca. Se non è possibile trovare dati di telemetria per uno snapshot che è stato sicuramente caricato, seguire questa procedura:

1. Controllare di esaminare la risorsa di Application Insights corretta verificando la chiave di strumentazione.

2. Usando il timestamp del log di caricamento, modificare il filtro Intervallo di tempo della ricerca per coprire tale intervallo di tempo.

Se ancora non vengono visualizzate eccezioni con tale ID snapshot, significa che i dati di telemetria dell'eccezione non sono stati segnalati ad Application Insights. Questa situazione si può verificare se l'applicazione ha subito un arresto anomalo del sistema dopo avere acquisito lo snapshot, ma prima di segnalare i dati di telemetria dell'eccezione. In questo caso, controllare i log del servizio app in `Diagnose and solve problems` per accertare se si sono verificati riavvi non previsti o eccezioni non gestite.

## <a name="next-steps"></a>Passaggi successivi

* [Impostare punti di ancoraggio nel codice](https://docs.microsoft.com/en-us/visualstudio/debugger/debug-live-azure-applications) per ottenere gli snapshot senza attendere un'eccezione.
* L'articolo [Diagnosticare eccezioni nelle app Web](app-insights-asp-net-exceptions.md) spiega come rendere visibile un maggior numero di eccezioni in Application Insights. 
* Il [rilevamento intelligente](app-insights-proactive-diagnostics.md) rileva automaticamente le anomalie delle prestazioni.
