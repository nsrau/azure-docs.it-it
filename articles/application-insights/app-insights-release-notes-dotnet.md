<properties 
	pageTitle="Note sulla versione per Application Insights per .NET" 
	description="Gli aggiornamenti più recenti per .NET SDK." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# Note sulla versione per Application Insights SDK per NET

[Application Insights SDK per .NET](app-insights-asp-net.md) invia dati di telemetria sull'app attiva ad [Application Insights](http://azure.microsoft.com/services/application-insights/), dove è possibile analizzarne l'utilizzo e le prestazioni.


#### Per installare l'SDK nell'applicazione

Vedere [Introduzione ad Application Insights per .NET](app-insights-asp-net.md).

#### Per eseguire l'aggiornamento all'SDK più recente 

* Dopo l'aggiornamento, è necessario unire nuovamente le personalizzazioni apportate al file ApplicationInsights.config. Se non si è certi che sia stato personalizzato, creare un nuovo progetto, aggiungervi Application Insights e confrontare il file.config con quello nel nuovo progetto. Prendere nota di eventuali differenze.
* In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
* Impostare il filtro in modo da visualizzare i pacchetti installati. 
* Selezionare **Microsoft.ApplicationInsights.Web** e scegliere **Aggiorna** (verranno aggiornati anche tutti i pacchetti dipendenti).
* Confrontare ApplicationInsights.config con la copia precedente. La maggior parte delle modifiche visualizzate è dovuta al fatto che alcuni moduli sono stati rimossi e altri sono stati resi parametrizzabili. Ripristinare eventuali personalizzazioni apportate al file precedente.
* Ricompilare la soluzione.

## Versione 2.0.0-beta4

- I metodi di estensione UseSampling e UseAdaptiveSampling sono stati spostati in Microsoft.ApplicationInsights.Extensibility
- Rimozione del supporto per le applicazioni universali Windows Phone e Store
- Aggiornamento di ```DependencyTelemetry``` per avere nuove proprietà ```ResultCode``` e ```Id```. ```ResultCode``` sarà usato per fornire un codice di risposta HTTP per le dipendenze HTTP e un codice di errore per le dipendenze SQL. ```Id``` sarà usato per la correlazione tra componenti. 
- Se si esegue l'inizializzazione di ```ServerTelemetryChannel``` a livello di codice ora è necessario chiamare il metodo ```ServerTelemetryChannel.Initialize()```. In caso contrario non verrà inizializzato un archivio permanente (ciò significa che, se i dati di telemetria non possono essere inviati in caso di problemi di connettività temporanei, verranno eliminati).
- ```ServerTelemetryChannel``` ha una nuova proprietà ```StorageFolder``` che può essere impostata tramite codice o tramite configurazione. Se questa proprietà è impostata, ApplicationInsights usa il percorso fornito per archiviare i dati di telemetria non inviati in caso di problemi di connettività temporanei. Se la proprietà non è impostata o la cartella fornita non è accessibile, ApplicationInsights tenterà di usare LocalAppData o le cartelle Temp come in precedenza.
- Il metodo di estensione ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder``` viene eliminato. Invece di questo metodo, utilizzare il metodo di istanza ```TelemetryConfiguration.TelemetryProcessorChainBuilder```.
- La classe ```TelemetryConfiguration``` ha una nuova proprietà ```TelemetryProcessors``` che fornisce l'accesso di sola lettura alla raccolta ```TelemetryProcessors```.
- ```Use```, ```UseSampling``` e ```UseAdaptiveSampling``` mantengono ```TelemetryProcessors``` caricato dalla configurazione.
- Due processori di telemetria sono integrati nel file di configurazione: il processore di telemetria del filtro dell'agente utente e il processore di telemetria del gestore della richiesta. È possibile personalizzarne il comportamento. È possibile aggiungere una stringa dell'agente utente che si desidera escludere nel file AI.config. Per impostazione predefinita viene esclusa la stringa agente utente ```AllwaysOn```. Il comportamento corrente confronta le stringhe nel file di configurazione con la stringa dell'agente utente mediante un confronto completo che non fa distinzione tra minuscole e maiuscole. È inoltre possibile personalizzare l'elenco dei gestori per il quale si desidera escludere le richieste. 
- La versione NuGet di Dependent Microsoft.ApplicationInsights.Agent.Intercept è stata aggiornata a 1.2.1. Include correzioni di bug della raccolta della dipendenza SQL.

## Versione 2.0.0-beta3

- [Campionamento adattivo](app-insights-sampling.md) attivato per impostazione predefinita nel canale dati di telemetria del server. 
- Firma fissa di ```UseSampling``` per consentire la concatenazione con altre chiamate a ```Use``` di processori di telemetria.  
- Proprietà ```Request.ID``` restituita. ```OperationContext``` ha ora una proprietà ```ParentId``` per la correlazione end-to-end.
- ```TimestampTelemetryInitializer``` rimosso. Il Timestamp verrà aggiunto automaticamente da ```TelemetryClient```.
- ```OperationCorrelationTelemetryInitializer``` viene aggiunto per impostazione predefinita per abilitare la correlazione delle operazioni.
- ```OperationCorrelationTelemetryInitializer``` viene utilizzato al posto di ```OperationIdTelemetryInitializer```.
- Agente utente non verrà raccolto per impostazione predefinita. Inizializzatore di telemetria agente utente è stato rimosso.
- Il campo ```DependencyTelemetry.Async``` non verrà raccolto dal modulo di telemetria dell'agente di raccolta dati di dipendenza. 
- Le richieste di diagnostica e il contenuto statico non verranno raccolti dal modulo di telemetria di richiesta. Utilizzare la raccolta ```HandlersToFilter``` di ```RequestTrackingTelemetryModule``` per filtrare le richieste generate da determinati gestori http. 
- La telemetria di richiesta generata automaticamente è accessibile tramite il metodo di estensione HttpContext: System.Web.HttpContextExtension.GetRequestTelemetry  


## Versione 2.0.0-beta2
- Aggiunta del supporto per ITelemetryProcessor e possibilità di configurare tramite codice o configurazione. [Abilita il filtro personalizzato nell’SDK](app-insights-api-filtering-sampling/#filtering)
- Inizializzatori di contesto rimossi. Usare invece [Inizializzatori di telemetria]( https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering).
- Application Insights aggiornato per .Net framework 4.6. 
- I nomi degli eventi personalizzati ora possono avere un massimo di 512 caratteri.
- Proprietà ```OperationContext.Name``` rinominata in ```RootName```.
- Proprietà ```RequestTelemetry.Id``` rimossa.
- Le proprietà ```Id``` e ```Context.Operation.Id``` di RequestTelemetry non verrebbero inizializzate durante la creazione di nuovi RequestTelemetry.
- ```RequestTelemetry.Name``` non è più inizializzato. Verrà invece utilizzato ```RequestTelemetry.Context.Operation.Name```.
- Nel monitorare la richiesta, il codice di risposta 401 fa parte dell'handshake di autenticazione normale e genererà una richiesta completata.
- Correggere il thread dell'interfaccia utente di blocco durante l'inizializzazione di InMemoryChannel (canale predefinito) dal thread dell'interfaccia utente. Consente di risolvere i problemi di congelamento dell’interfaccia utente con le applicazioni WPF.
 
## Versione 2.0.0 - beta1
- TrackDependency produrrà codice JSON valido quando non vengono specificati tutti i campi obbligatori.
- La proprietà ridondante ```RequestTelemetry.ID``` costituisce un proxy per ```RequestTelemetry.Operation.Id```.
- La nuova interfaccia ```ISupportSampling``` e la sua relativa implementazione esplicita per la maggior parte dei tipi di elementi di dati.
- Proprietà ```Count``` in DependencyTelemetry contrassegnata come obsoleta. Usare invece ```SamplingPercentage```.
- Nuovo ```CloudContext``` introdotto e proprietà ```RoleName``` e ```RoleInstance``` spostate in questo contesto da ```DeviceContext```.
- Nuova proprietà ```AuthenticatedUserId``` in ```UserContext``` per specificare l'identità dell'utente autenticato.
- Aggiunta di `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` per l'inizializzazione del contesto dell'utente autenticato, come specificato da JavaScript SDK.
- Aggiunta di `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` e supporto del campionamento a frequenza fissa come relativa implementazione.
- Aggiunta di `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` che consente la creazione di `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` con un set di `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Versione 1.2

- Gli inizializzatori di telemetria che non hanno dipendenze sulle librerie ASP.NET sono stati spostati da `Microsoft.ApplicationInsights.Web` al nuovo pacchetto NuGet della dipendenza `Microsoft.ApplicationInsights.WindowsServer`
- `Microsoft.ApplicationInsights.Web.dll` è stato rinominato in `Microsoft.AI.Web.dll`
- `Microsoft.ApplicationInsights.Web.TelemetryChannel`NuGet è stato rinominato in`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`.`Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel`assembly è stato rinominato in`Microsoft.AI.ServerTelemetryChannel.dll`.`Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel`class è stato rinominato in`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Tutti gli spazi dei nomi che fanno parte dell'SDK Web sono stati modificati per escludere la parte `Extensibility`. Ciò include tutti gli inizializzatori di telemetria in ApplicationInsights.config e il modulo `ApplicationInsightsWebTracking` nel file web.config.
- Le dipendenze raccolte tramite l'agente di strumentazione runtime (abilitato tramite Status Monitor o estensione del sito Web di Azure) non saranno contrassegnate come asincrone se non sono presenti HttpContext.Current nel thread.
- La proprietà `SamplingRatio` di `DependencyTrackingTelemetryModule` non esegue alcuna operazione ed è contrassegnata come obsoleta.
- L'assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` è stato rinominato in `Microsoft.AI.PerfCounterCollector`
- Numerose correzioni di bug più piccole nel Web e dispositivi SDK


## Versione 1.1

- È stato aggiunto il nuovo tipo di telemetria `DependencyTelemetry`, che può essere usato per inviare informazioni sulle chiamate di dipendenza dall'applicazione (ad esempio chiamate SQL, HTTP e così via).
- È stato aggiunto un nuovo metodo di overload `TelemetryClient.TrackDependency` che permette di inviare informazioni sulle chiamate di dipendenza.
- È stata corretta l'eccezione NullReferenceException generata dal modulo di diagnostica quando si usa TelemetryConfiguration.CreateDefault.

## Versione 1.0

- Gli inizializzatori e i moduli di telemetria sono stati spostati dagli spazi dei nomi secondari separati allo spazio dei nomi radice `Microsoft.ApplicationInsights.Extensibility.Web`.
- Il prefisso "Web" è stato rimosso dai nomi degli inizializzatori e dei moduli di telemetria, poiché è già incluso nel nome dello spazio dei nomi `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` è stato spostato dall'assembly `Microsoft.ApplicationInsights` all'assembly `Microsoft.ApplicationInsights.Extensibility.Web` ed è stato convertito in un `ITelemetryInitializer`.
- Cambiare i nomi dello spazio dei nomi e dell'assembly da `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` a `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` per coerenza con il nome del pacchetto NuGet.
- Rinominare `RemoteDependencyModule` in `DependencyTrackingTelemetryModule`.
- Rinominare `CustomPerformanceCounterCollectionRequest` in `PerformanceCounterCollectionRequest`.

## Versione 0.17
- Rimozione dipendenza EventSource NuGet per le applicazioni del framework 4.5.
- I cookie di utente e sessione anonimi non verranno generati sul lato server. Per implementare il rilevamento di utenti e sessioni per le app Web, è ora necessaria la strumentazione con JS SDK. I cookie da JavaScript SDK verranno comunque rispettati. I moduli di telemetria ```WebSessionTrackingTelemetryModule``` e ```WebUserTrackingTelemetryModule``` non sono più supportati e sono stati rimossi dal file ApplicationInsights.config. Si noti che questa modifica potrebbe provocare una rideterminazione significativa dei conteggi relativi a utenti e sessioni, poiché vengono ora contate solo le sessioni originate dagli utenti.
- Il valore OSVersion non viene più popolato dall'SDK per impostazione predefinita. Se vuoti, i valori per OS e OSVersion vengono calcolati dalla pipeline di Application Insights, in base all'agente utente. 
- Il canale di persistenza ottimizzato per scenari di carico elevato viene usato per l'SDK Web. Problema "spirale della morte" risolto. La "spirale della morte" è una condizione in cui il picco nel numero di elementi di telemetria, che supera in misura significativa la soglia di limitazione sull'endpoint, conduce a nuovi tentativi dopo un determinato periodo di tempo e sarà limitato nuovamente durante i nuovi tentativi.
- La modalità di sviluppo è ottimizzata per la produzione. Se attivata per errore non causa un sovraccarico importante come in precedenza nel provare a dare un output di informazioni aggiuntive.
- Per impostazione predefinita, la modalità di sviluppo verrà attivata solo quando l'applicazione è nel debugger. È possibile eseguirne l'override usando la proprietà ```DeveloperMode``` dell'interfaccia ```ITelemetryChannel```.

## Versione 0.16 

Anteprima del 28/04/2015

- L'SDK supporta ora la piattaforma di destinazione DNX per consentire il monitoraggio delle applicazioni per [.NET Core Framework](http://www.dotnetfoundation.org/NETCore5).
- Le istanze di ```TelemetryClient``` non memorizzano più nella cache la chiave di strumentazione. Ora, se la chiave di strumentazione non è stata impostata in modo esplicito in ```TelemetryClient```, ```InstrumentationKey``` restituirà Null. In questo modo viene risolto un problema per cui quando si imposta ```TelemetryConfiguration.Active.InstrumentationKey``` dopo che sono già stati raccolti alcuni dati di telemetria, i moduli di telemetria come agente di raccolta dipendenze, raccolta dati delle richieste Web e agente di raccolta contatori delle prestazioni useranno una nuova chiave di strumentazione.

## Versione 0.15

- La nuova proprietà ```Operation.SyntheticSource``` è ora disponibile in ```TelemetryContext```. Ora è possibile contrassegnare gli elementi di telemetria come "traffico utente non reale" e specificare il modo in cui il traffico è stato generato. Ad esempio impostando questa proprietà è possibile distinguere il traffico che deriva dall'automazione dei test da quello che deriva dal test di carico.
- La logica di canale è stata spostata in un altro pacchetto NuGet denominato Microsoft.ApplicationInsights.PersistenceChannel. Il canale predefinito è ora denominato InMemoryChannel
- Il nuovo metodo ```TelemetryClient.Flush``` consente di scaricare gli elementi di telemetria dal buffer in modo sincrono

## Versione 0.13

Per le versioni precedenti non sono disponibili le note sulla versione.

 

<!----HONumber=AcomDC_0121_2016-->