---
title: Snapshot Debugger di Azure Application Insights per app .NET | Microsoft Docs
description: Gli snapshot di debug vengono raccolti automaticamente quando vengono generate eccezioni nelle app di produzione .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: a92b54a80de645dda8ea0cc0259bd07f72330204
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136716"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Snapshot di debug per le eccezioni nelle app .NET

Quando si verifica un'eccezione, è possibile raccogliere automaticamente uno snapshot di debug dall'applicazione Web live. Lo snapshot mostra lo stato del codice sorgente e delle variabili nel momento in cui è stata generata l'eccezione. Snapshot Debugger (anteprima) di [Azure Application Insights](app-insights-overview.md) monitora la telemetria delle eccezioni dall'app Web. Raccoglie snapshot per le eccezioni generate più frequentemente in modo che l'utente possa avere le informazioni necessarie per diagnosticare i problemi nell'ambiente di produzione. Includere il [pacchetto NuGet Snapshot Collector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'applicazione e configurare facoltativamente i parametri di raccolta in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Gli snapshot vengono visualizzati per le [eccezioni](app-insights-asp-net-exceptions.md) nel portale Application Insights.

È possibile visualizzare gli snapshot di debug nel portale per vedere lo stack di chiamate e ispezionare le variabili in ogni stack frame di chiamate. Per ottenere un'esperienza di debug più potente con il codice sorgente, aprire gli snapshot con Visual Studio 2017 Enterprise. In Visual Studio è anche possibile [impostare punti di ancoraggio per creare in modo interattivo snapshot](https://aka.ms/snappoint) senza attendere un'eccezione.

Gli snapshot di debug vengono archiviati per sette giorni. I criteri di conservazione sono impostati per ogni singola applicazione. Se è necessario aumentare questo valore, è possibile richiedere un aumento aprendo un caso di supporto nel portale di Azure.

La raccolta di snapshot è disponibile per:
* Applicazioni .NET Framework e ASP.NET che eseguono .NET Framework 4.5 o versione successiva.
* Applicazioni .NET Core 2.0 e ASP.NET Core 2.0 in esecuzione in Windows.

Sono supportati i seguenti ambienti:
* Servizio app di Azure.
* Servizio cloud di Azure in esecuzione nella famiglia del sistema operativo 4 o versione successiva.
* Servizi Azure Service Fabric in esecuzione su Windows Server 2012 R2 o versione successiva.
* Macchine virtuali di Azure in esecuzione su Windows Server 2012 R2 o versione successiva.
* Macchine fisiche o virtuali locali in esecuzione su Windows Server 2012 R2 o versione successiva.

> [!NOTE]
> Le applicazioni client (ad esempio, WPF, Windows Forms o piattaforma UWP) non sono supportate.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET

1. [Abilitare Application Insights nell'app Web](app-insights-asp-net.md) se non è ancora stato fatto.

2. Includere il pacchetto NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'app.

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

4. Gli snapshot vengono raccolti solo per le eccezioni segnalate ad Application Insights. In alcuni casi (ad esempio, versioni precedenti della piattaforma .NET), potrebbe essere necessario [configurare la raccolta di eccezioni](app-insights-asp-net-exceptions.md#exceptions) per visualizzare le eccezioni con gli snapshot nel portale.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurare la raccolta di snapshot per le applicazioni ASP.NET Core 2.0

1. [Abilitare Application Insights nell'app Web ASP.NET Core](app-insights-asp-net-core.md) se non è ancora stato fatto.

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

4. Configurare l'agente di raccolta snapshot aggiungendo una sezione SnapshotCollectorConfiguration ad appsettings.json. Ad esempio: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
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

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurare la raccolta di snapshot per le altre applicazioni .NET

1. Se l'applicazione non è già instrumentata con Application Insights, iniziare [abilitando Application Insights e impostando la chiave di strumentazione](app-insights-windows-desktop.md).

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

## <a name="grant-permissions"></a>Concedere le autorizzazioni

L'accesso agli snapshot è protetto dal controllo degli accessi in base al ruolo. Per ispezionare uno snapshot, è necessario prima essere aggiunti al ruolo necessario dal proprietario di una sottoscrizione.

> [!NOTE]
> Proprietari e collaboratori non hanno automaticamente questo ruolo. Per visualizzare gli snapshot, devono aggiungersi al ruolo.

I proprietari di sottoscrizione devono assegnare il ruolo `Application Insights Snapshot Debugger` agli utenti che ispezioneranno gli snapshot. Questo ruolo può essere assegnato a singoli utenti o gruppi dai proprietari della sottoscrizione per la risorsa di Application Insights di destinazione oppure per il gruppo di risorse o la sottoscrizione di tale risorsa.

1. Passare alla risorsa di Application Insights nel portale di Azure.
1. Fare clic su **Controllo di accesso (IAM)**.
1. Fare clic sul pulsante **+Aggiungi un'assegnazione di ruolo**.
1. Selezionare **Snapshot Debugger di Azure Application Insights** nell'elenco a discesa **Ruoli**.
1. Cercare e immettere un nome per l'utente da aggiungere.
1. Fare clic sul pulsante **Salva** per aggiungere l'utente al ruolo.


> [!IMPORTANT]
> Gli snapshot possono contenere informazioni personali e altre informazioni riservate nei valori delle variabili e dei parametri.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Snapshot di debug nel portale di Application Insights

Se è disponibile uno snapshot per una determinata eccezione o un determinato ID problema, viene visualizzato il pulsante **Open Debug Snapshot** (Apri snapshot di debug) per l'[eccezione](app-insights-asp-net-exceptions.md) nel portale di Application Insights.

![Pulsante Open Debug Snapshot per l'eccezione](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Nella vista Debug Snapshot (Snapshot di debug) vengono visualizzati uno stack di chiamate e un riquadro delle variabili. Quando si selezionano frame dello stack di chiamate nel riquadro corrispondente, è possibile visualizzare i parametri e le variabili locali per la chiamata di funzione nel riquadro delle variabili.

![Visualizzare uno snapshot di debug nel portale](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Gli snapshot possono contenere informazioni riservate e per impostazione predefinita non sono visibili. Per visualizzare gli snapshot, è necessario che all'utente sia stato assegnato il ruolo `Application Insights Snapshot Debugger`.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Snapshot di debug con Visual Studio 2017 Enterprise
1. Fare clic sul pulsante **Download Snapshot** (Scarica snapshot) per scaricare un file `.diagsession` che può essere aperto con Visual Studio 2017 Enterprise.

2. Per aprire il file `.diagsession`, è necessario avere installato il componente di Visual Studio Snapshot Debugger. Il componente Snapshot Debugger è un componente obbligatorio del carico di lavoro ASP .NET in Visual Studio e può essere selezionato nell'elenco dei singoli componenti del programma di installazione di Visual Studio. Se si usa una versione di Visual Studio precedente alla 15.5, è necessario installare l'estensione da [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Quando si apre il file di snapshot, in Visual Studio viene visualizzata la pagina per il debug di minidump. Fare clic su **Debug Managed Code** (Debug codice gestito) per avviare il debug dello snapshot. Lo snapshot viene aperto alla riga di codice in cui è stata generata l'eccezione in modo da consentire il debug dello stato corrente del processo.

    ![Visualizzare lo snapshot di debug in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Lo snapshot scaricato contiene tutti i file di simboli trovati nel server applicazioni Web. Questi file di simboli sono necessari per associare i dati degli snapshot al codice sorgente. Per le app del servizio app, assicurarsi di abilitare la distribuzione dei simboli al momento della pubblicazione delle app Web.

## <a name="how-snapshots-work"></a>Funzionamento degli snapshot

L'agente di raccolta snapshot viene implementato come un [processore di Application Insights Telemetry](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando l'applicazione viene eseguita, il processore di telemetria dell'agente di raccolta snapshot viene aggiunto alla pipeline di telemetria dell'applicazione.
Ogni volta che l'applicazione chiama [TrackException](app-insights-asp-net-exceptions.md#exceptions), l'agente di raccolta snapshot consente di calcolare un ID problema dal tipo di eccezione generata e dal metodo generante.
Ogni volta che l'applicazione chiama TrackException, un contatore viene incrementato con l'ID problema appropriato. Quando il contatore raggiunge il valore `ThresholdForSnapshotting`, l'ID problema viene aggiunto a un piano di raccolta.

Snapshot Collector monitora anche le eccezioni quando vengono generate sottoscrivendo l'evento [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Quando viene generato l'evento, l'ID problema dell'eccezione viene calcolato e confrontato con gli ID problema inclusi nel piano di raccolta.
Se esiste una corrispondenza, viene creato uno snapshot del processo in esecuzione. Allo snapshot viene assegnato un identificatore univoco e l'eccezione viene contrassegnata con tale identificatore. Dopo che è stato restituito il gestore FirstChanceException, l'eccezione generata viene elaborata come di consueto. Infine, l'eccezione raggiunge nuovamente il metodo TrackException in cui viene segnalata ad Application Insights, insieme all'identificatore dello snapshot.

L'esecuzione del processo principale continua e rende disponibile il traffico agli utenti con un'interruzione minima. Nel frattempo, lo snapshot viene trasferito al processo di caricamento degli snapshot. Tale processo crea un minidump e lo carica in Application Insights insieme agli eventuali file di simboli pertinenti (con estensione pdb).

> [!TIP]
> - Uno snapshot del processo è un clone sospeso del processo in esecuzione.
> - Per la creazione dello snapshot sono necessari da 10 a 20 millisecondi circa.
> - Il valore predefinito di `ThresholdForSnapshotting` è 1, ovvero il valore minimo. Pertanto, l'app deve attivare la stessa eccezione **due volte** prima che venga creato uno snapshot.
> - Impostare `IsEnabledInDeveloperMode` su true se si vuole generare gli snapshot durante il debug in Visual Studio.
> - La frequenza di creazione dello snapshot è limitata dall'impostazione `SnapshotsPerTenMinutesLimit`. Per impostazione predefinita, il limite è uno snapshot ogni dieci minuti.
> - Non è possibile caricare più di 50 snapshot al giorno.

## <a name="current-limitations"></a>Limitazioni correnti

### <a name="publish-symbols"></a>Pubblicare i simboli
Per poter decodificare le variabili e offrire un'esperienza di debug in Visual Studio, Snapshot Debugger richiede la presenza dei file di simboli nel server di produzione.
Per impostazione predefinita, la versione 15.2 (o successiva) di Visual Studio 2017 pubblica i simboli per le build di versione durante la pubblicazione nel servizio app. Nelle versioni precedenti è necessario aggiungere la riga seguente al file `.pubxml` del profilo di pubblicazione per pubblicare i simboli in modalità versione:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Per Calcolo di Azure e altri tipi di calcoli, verificare che i file di simboli si trovino nella stessa cartella della DLL dell'applicazione principale (generalmente `wwwroot/bin`) o che siano disponibili nel percorso corrente.

### <a name="optimized-builds"></a>Compilazioni ottimizzate
In alcuni casi, le variabili locali non possono essere visualizzate nelle build di versione a causa delle ottimizzazioni applicate dal compilatore JIT.
Tuttavia, in Servizi app di Azure, l'agente di raccolta snapshot può deottimizzare generando metodi che fanno parte del relativo piano di raccolta.

> [!TIP]
> Installare l'estensione del sito Application Insights nel servizio app per ottenere supporto per la deottimizzazione.

## <a name="troubleshooting"></a>risoluzione dei problemi

Questi suggerimenti consentono di risolvere i problemi relativi al debugger di snapshot.

### <a name="use-the-snapshot-health-check"></a>Usare il controllo integrità dello snapshot
Alcuni problemi comuni riguardano la mancata visualizzazione di Apri snapshot di debug. Ad esempio, se si usa un agente di raccolta snapshot obsoleto, se si raggiunge il limite giornaliero di caricamento o se il caricamento dello snapshot richiede molto tempo. Per la risoluzione di problemi comuni, usare il controllo integrità dello snapshot.

Nel riquadro dell'eccezione è presente un collegamento di visualizzazione traccia end-to-end che consente di visualizzare il controllo integrità dello snapshot.

![Immettere il controllo integrità dello snapshot](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

L'interfaccia interattiva, simile a una chat, esegue la ricerca di problemi comuni e guida l'utente nella risoluzione.

![Controllo integrità](./media/app-insights-snapshot-debugger/healthcheck.png)

Se il problema non viene risolto, fare riferimento ai passaggi manuali di risoluzione dei problemi seguenti.

### <a name="verify-the-instrumentation-key"></a>Verificare la chiave di strumentazione

Verificare di usare la chiave di strumentazione corretta nell'applicazione pubblicata. In genere, la chiave di strumentazione viene letta dal file ApplicationInsights.config. Verificare che il valore sia lo stesso della chiave di strumentazione per la risorsa di Application Insights visualizzata nel portale.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Eseguire l'aggiornamento alla versione più recente del pacchetto NuGet

Usare Gestione pacchetti NuGet di Visual Studio per assicurarsi di usare la versione più recente di Microsoft.ApplicationInsights.SnapshotCollector. Le note sulla versione sono disponibili all'indirizzo https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Controllare i log dell'utilità di caricamento

Dopo la creazione di uno snapshot, un file di minidump (DMP) viene creato sul disco. Un processo di caricamento separato crea il file di minidump e lo carica, con i file PDB associati, nella risorsa di archiviazione Snapshot Debugger di Application Insights. Il minidump, dopo essere stato correttamente caricato, viene eliminato dal disco. I file di log per il processo di caricamento vengono conservati sul disco. In un ambiente del servizio app questi log si trovano in `D:\Home\LogFiles`. Usare il sito di gestione di Kudu per il servizio app per trovare questi file di log.

1. Aprire l'applicazione del servizio app nel portale di Azure.
2. Fare clic su **Strumenti avanzati** o cercare **Kudu**.
3. Fare clic su **Vai**.
4. Nell'elenco a discesa **Console di debug** selezionare **CMD**.
5. Fare clic su **LogFiles**.

Verrà visualizzato almeno un file con il nome che inizia con `Uploader_` o `SnapshotUploader_` e l'estensione `.log`. Fare clic sull'icona appropriata per scaricare i file di log o aprirli in un browser.
Il nome del file include un suffisso univoco che identifica l'istanza di Servizio app. Se l'istanza del servizio app è ospitata in più di un computer, è presente un file di log separato per ogni computer. Quando l'utilità di caricamento rileva un nuovo file di minidump, quest'ultimo viene registrato nel file di log. Ecco un esempio di snapshot e caricamento corretti:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> L'esempio precedente è tratto dalla versione 1.2.0 del pacchetto NuGet Microsoft.ApplicationInsights.SnapshotCollector. Nelle versioni precedenti il processo di caricamento è denominato `MinidumpUploader.exe` e il log è meno dettagliato.

Nell'esempio precedente la chiave di strumentazione è `c12a605e73c44346a984e00000000000`. Questo valore deve corrispondere alla chiave di strumentazione dell'applicazione.
Il minidump è associato a uno snapshot con l'ID `139e411a23934dc0b9ea08a626db16c5`. Sarà possibile usare questo ID in seguito per individuare i dati di telemetria delle eccezioni associati in Application Insights Analytics.

L'utilità di caricamento cerca i nuovi file PDB ogni 15 minuti circa. Ad esempio:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Per le applicazioni _non_ ospitate nel servizio app, i log di caricamento sono nella stessa cartella dei minidump: `%TEMP%\Dumps\<ikey>` (dove `<ikey>` è la chiave di strumentazione).

### <a name="troubleshooting-cloud-services"></a>Risoluzione dei problemi di servizi cloud
Per i ruoli nei servizi cloud, la cartella temporanea predefinita potrebbe essere troppo piccola per contenere i file di minidump, con conseguente perdita di snapshot.
Lo spazio necessario dipende dal working set totale dell'applicazione e dal numero di snapshot simultanei.
Il working set di un ruolo Web ASP.NET a 32 bit è in genere compreso tra 200 MB e 500 MB.
È necessario consentire almeno due snapshot simultanei.
Se ad esempio l'applicazione usa 1 GB di working set totale, è necessario verificare che siano disponibili almeno 2 GB di spazio su disco per archiviare gli snapshot.
Seguire questi passaggi per configurare il ruolo del servizio cloud con una risorsa locale dedicata per gli snapshot.

1. Aggiungere una nuova risorsa locale al servizio cloud modificando il file di definizione del servizio cloud (con estensione csdef). L'esempio seguente definisce una risorsa denominata `SnapshotStore` con una dimensione pari a 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modificare il codice di avvio del ruolo per aggiungere una variabile di ambiente che punti alla risorsa locale `SnapshotStore`. Per i ruoli di lavoro, il codice deve essere aggiunto al metodo `OnStart` del ruolo:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Per i ruoli Web (ASP.NET), il codice deve essere aggiunto al metodo `Application_Start` dell'applicazione Web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aggiornare il file ApplicationInsights.config del ruolo per sostituire il percorso della cartella temporanea usato da `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="overriding-the-shadow-copy-folder"></a>Sostituzione della cartella di copia shadow

Quando Snapshot Collector viene avviato, tenta di trovare una cartella sul disco adatta all'esecuzione dell'utilità di caricamento dello snapshot. La cartella selezionata è nota come cartella di copia shadow.

Snapshot Collector controlla alcuni percorsi noti, verificando di disporre delle autorizzazioni per copiare i file binari dell'utilità di caricamento dello snapshot. Vengono usate le variabili di ambiente seguenti:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se non viene trovata una cartella appropriata, Snapshot Collector segnala un errore del tipo _"Impossibile trovare una cartella di copia shadow idonea"._

Se la copia ha esito negativo, Snapshot Collector segnala un errore `ShadowCopyFailed`.

Se non è possibile avviare l'utilità di caricamento, Snapshot Collector segnala un errore `UploaderCannotStartFromShadowCopy`. Il corpo del messaggio contiene spesso `System.UnauthorizedAccessException`. Questo errore si verifica in genere perché l'applicazione è in esecuzione con un account con autorizzazioni ridotte. L'account dispone delle autorizzazioni di scrittura nella cartella di copia shadow, ma non è autorizzato a eseguire il codice.

Poiché questi errori si verificano in genere durante l'avvio, sono seguiti in genere da un `ExceptionDuringConnect` errore del tipo _"Impossibile avviare l'utilità di caricamento."_

Per risolvere questi errori, è possibile specificare la cartella di copia shadow manualmente tramite l'opzione di configurazione `ShadowCopyFolder`. Esempio per ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

In alternativa, se si usa appsettings.json con un'applicazione .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usare la ricerca di Application Insights per trovare le eccezioni con gli snapshot

Quando viene creato uno snapshot, l'eccezione generata viene contrassegnata con un ID snapshot. Tale ID snapshot viene incluso come proprietà personalizzata, quando i dati di telemetria dell'eccezione vengono segnalati ad Application Insights. Usando **Cerca** in Application Insights, è possibile trovare tutti i dati di telemetria con la proprietà personalizzata `ai.snapshot.id`.

1. Passare alla risorsa di Application Insights nel portale di Azure.
2. Fare clic su **Search**(Cerca).
3. Digitare `ai.snapshot.id` nella casella di testo di ricerca e premere INVIO.

![Cercare i dati di telemetria con i ID snapshot nel portale](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Se questa ricerca non restituisce risultati, significa che nessuno snapshot è stato segnalato ad Application Insights per l'applicazione nell'intervallo di tempo selezionato.

Per cercare uno specifico ID snapshot dei log di caricamento, digitare tale ID nella casella di ricerca. Se non è possibile trovare dati di telemetria per uno snapshot che è stato sicuramente caricato, seguire questa procedura:

1. Controllare di esaminare la risorsa di Application Insights corretta verificando la chiave di strumentazione.

2. Usando il timestamp del log di caricamento, modificare il filtro Intervallo di tempo della ricerca per coprire tale intervallo di tempo.

Se ancora non vengono visualizzate eccezioni con tale ID snapshot, significa che i dati di telemetria dell'eccezione non sono stati segnalati ad Application Insights. Questa situazione si può verificare se l'applicazione ha subito un arresto anomalo del sistema dopo avere acquisito lo snapshot, ma prima di segnalare i dati di telemetria dell'eccezione. In questo caso, controllare i log del servizio app in `Diagnose and solve problems` per accertare se si sono verificati riavvi non previsti o eccezioni non gestite.

### <a name="edit-network-proxy-or-firewall-rules"></a>Modificare le regole proxy o firewall di rete

Se l'applicazione si connette a Internet tramite un proxy o un firewall, può essere necessario modificare le regole per consentire all'applicazione di comunicare con il servizio Snapshot Debugger. Ecco un [elenco di porte e indirizzi IP usati da Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Passaggi successivi

* [Impostare punti di ancoraggio nel codice](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) per ottenere gli snapshot senza attendere un'eccezione.
* L'articolo [Diagnosticare eccezioni nelle app Web](app-insights-asp-net-exceptions.md) spiega come rendere visibile un maggior numero di eccezioni in Application Insights.
* Il [rilevamento intelligente](app-insights-proactive-diagnostics.md) rileva automaticamente le anomalie delle prestazioni.
