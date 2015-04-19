<properties 
	pageTitle="Panoramica dell'SDK Windows Store per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure per l'SDK Windows Store per Azure Mobile Egagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Come integrare Engagement in Windows 

In questa procedura viene descritto il modo più semplice per attivare le funzioni Analisi e Monitoraggio nell'applicazione Windows.

I passaggi seguenti sono sufficienti per attivare il report dei log necessari per calcolare tutte le statistiche relative ad utenti, sessioni, attività, arresti anomali del sistema e informazioni tecniche. Il report dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve avvenire manualmente utilizzando l'API Engagement (vedere  [Come utilizzare l'API di tag Mobile Engagement avanzata nell'app Windows Store](mobile-engagement-windows-store-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

##Versioni supportate

Questo SDK Windows supporta solo:

- Applicazione Windows 8 C\# Store
- Applicazione Windows 8,1 C\# Store

##Incorporare l'SDK Engagement nel progetto Windows

Incorporare l'SDK Engagement mediante Gestione pacchetti NuGet per la piattaforma di sviluppo Microsoft.

Il pacchetto NuGet per Engagement Mobile non è ancora disponibile in linea, pertanto è necessario scaricare l'archivio SDK, decomprimerlo e quindi, in Visual Studio, fare clic con il pulsante destro del mouse sul progetto, passare a "Gestisci pacchetti Nuget", Impostazioni e aggiungere la cartella lib come origine di un nuovo pacchetto.

> [AZURE.NOTE] NuGet è il gestore pacchetto principale per i progetti .net. Scaricherà, aggiungerà e copierà tutto ciò che è necessario per utilizzare l'SDK Engagement. L'applicazione Engagement C\ # Metro per Windows 8 utilizza librerie di terze parti (Microsoft.Bcl.Build v1.0.10 e pacchetto portabilità di Microsoft v1.1.3) a scopo di portabilità tra piattaforme; NuGet ne chiederà automaticamente l'installazione. È possibile trovare ulteriori informazioni sul [sito Web NuGet]

##Aggiungere le funzionalità

L'SDK Engagement richiede alcune funzionalità dell'SDK Windows per il corretto funzionamento.

Aprire il file `Package.appxmanifest` e assicurarsi che le seguenti funzionalità siano dichiarate:

- `Internet (Client)`

Andare al pannello `Declarations` del file Package.appxmanifest.

In `Dichiarazioni disponibili` selezionare l'opzione `Associazioni tipi di file`  e aggiungerla. Nella schermata di destra, nel campo `Nome`, scrivere `engagement_log_file` e nel campo Tipo di file scrivere `.set`.

Quindi, in `Dichiarazioni disponibili` selezionare `Aggiornamento file nella cache`  e aggiungere.

##Inizializzare l'SDK Engagement

### Configurazione di Engagement

La configurazione di Engagement è centralizzata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

- La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale Azure.

### Inizializzazione di Engagement

Quando si crea un nuovo progetto, viene generato un file `App.xaml.cs`. Questa classe eredita da `Application` e contiene molti metodi importanti. Verrà inoltre utilizzato per inizializzare l'SDK di Engagement.

Modificare `App.xaml.cs`::

- Aggiungere alle istruzioni `using`:

			using Microsoft.Azure.Engagement;

- Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			
			 // or
			
			 EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

- Quando l'applicazione viene avviata utilizzando uno schema personalizzato, un'altra applicazione o la riga di comando, viene chiamato il metodo `OnActivated`. Inoltre, è necessario avviare l'agente Engagement quando viene attivata l'applicazione. A tale scopo, eseguire l'override del metodo `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			
			 // or
			
			 EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] È vivamente sconsigliata l'aggiunta dell'inizializzazione di Engagement in un altro punto dell'applicazione.

##Report di base

### Metodo consigliato: eseguire l'overload delle classi `Pages`.

Per attivare il report di tutti i log richiesti da Engagement per calcolare utenti, sessioni, attività, arresti anomali e statistiche tecniche, è possibile far sì che tutte le sottoclassi `Page`  ereditino dalle classi `EngagementPage` .

Di seguito è riportato un esempio di come ottenere questo risultato per una pagina dell'applicazione. È possibile procedere allo stesso modo per tutte le pagine dell'applicazione.

#### File di origine C\ #

Modificare il file `.xaml.cs` della pagina:

- Aggiungere alle istruzioni `using`:

			using Microsoft.Azure.Engagement;

- Sostituire `Page` con `EngagementPage`:

**Senza Engagement:**

			namespace Example
			{
			 public sealed partial class ExamplePage : Page
			 {
			 [...]
			 }
			}

**Con Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			 public sealed partial class ExamplePage : EngagementPage 
			 {
			 [...]
			 }
			}

> [AZURE.IMPORTANT] Se la pagina esegue l'ovverride del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata (`EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`).

#### File XAML 

Modificare il file `.xaml` della pagina:

- Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

- Sostituire `Page` con `engagement:EngagementPage`:

**Senza Engagement:**

			<Page>
			 <!-- layout -->
			 ...
			</Page>

**Con Engagement:**

			<engagement:EngagementPage 
			 xmlns:engagement="using:Microsoft.Azure.Engagement">
			 <!-- layout -->
			 ...
			</engagement:EngagementPage >

#### Eseguire l'override del comportamento predefinito

Per impostazione predefinita, il nome della classe della pagina viene indicato come nome dell'attività, senza elementi aggiuntivi. Se la classe utilizza il suffisso "Page", Engagement rimuoverà anche questo elemento.

Se si desidera eseguire l'override del comportamento predefinito per il nome, aggiungere quanto segue al codice:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			 /* your code */
			 return "new name";
			}

Se si desidera segnalare alcune informazioni aggiuntive con l'attività, è possibile aggiungere quanto segue al codice:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			 /* your code */
			 return extra;
			}

Ulteriori dettagli sulle informazioni aggiuntive sono disponibili qui \ <windows sdk-extra parameters\>.

Questi metodi vengono chiamati dall'interno del metodo  `OnNavigatedTo` della pagina.

### Metodo alternativo: chiamare  `StartActivity()` manualmente

Se non è possibile o non si desidera eseguire l'overload delle classi  `Page`, è possibile invece avviare le attività chiamando direttamente i metodi  `EngagementAgent`.

È consigliabile chiamare  `StartActivity` all'interno del metodo  `OnNavigatedTo` della pagina.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			 base.OnNavigatedTo(e);
			 EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] L'SDK Windows chiama automaticamente il metodo  `EndActivity` quando l'applicazione viene chiusa. Di conseguenza, è  *HIGHLY* consigliabile chiamare il metodo  `StartActivity` ogni volta che l'attività dell'utente cambia e non chiamare  *NEVER* il metodo  `EndActivity`, poiché questo metodo forza la chiusura della sessione corrente.

##Report avanzati

Facoltativamente, è possibile segnalare eventi specifici dell'applicazione, errori e processi. A tale scopo, utilizzare gli altri metodi disponibili nella classe  `EngagementAgent`. L'API Engagement consente di utilizzare tutte le funzionalità avanzate di Engagement.

Per ulteriori informazioni, vedere Come utilizzare l'API Engagement in Windows.

##Configurazione avanzata

### Disabilitare la segnalazione automatica degli arresti anomali

È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quindi, quando si verifica un'eccezione non gestita, Engagement non effettuerà alcuna azione.

> [AZURE.WARNING] Se si prevede di disabilitare questa funzionalità, tenere presente che quando si verifica un arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione in base al modo in cui che è stata dichiarata:

#### Dal file  `EngagementConfiguration.xml`

Impostare la segnalazione degli arresti anomali su  `false` tra i tag `<reportCrash>`  e `</reportCrash>`.

#### Dall'oggetto  `EngagementConfiguration` in fase di esecuzione

Impostare la segnalazione degli arresti anomali su false utilizzando l'oggetto EngagementConfiguration.

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### Modalità burst [beta]

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è consigliabile memorizzarli nel buffer e segnalarli tutti insieme a intervalli regolari (in "modalità burst").

A tale scopo, chiamare il metodo:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitoraggio di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di utilizzare una soglia di burst non maggiore di 30000 (30 secondi).

> [AZURE.WARNING] Non è possibile configurare la soglia di burst per un periodo inferiore a 1s. Se si tenta di impostare un valore minore, l'SDK mostrerà una traccia con l'errore e verrà reimpostato automaticamente sul valore predefinito, vale a dire, 0s. In questo modo verrà attivato l'SDK per il report dei log in tempo reale.

[qui]:http://www.nuget.org/packages/Capptain.WindowsCS
[sito Web NuGet]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
