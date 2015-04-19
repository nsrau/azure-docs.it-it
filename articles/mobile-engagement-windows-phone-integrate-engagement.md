<properties 
	pageTitle="Integrazione dell'SDK Windows Phone per Azure Mobile Engagement " 
	description="Come integrare Engagement in Windows Phone" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Come integrare Engagement in Windows Phone

In questa procedura viene descritto il modo più semplice per attivare le funzioni Analisi e Monitoraggio nell'applicazione Windows Phone.

I passaggi seguenti sono sufficienti per attivare il report dei log necessari per calcolare tutte le statistiche relative ad utenti, sessioni, attività, arresti anomali e informazioni tecniche. Il report dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve avvenire manualmente utilizzando l'API Engagement (vedere  [Come utilizzare l'API di tag Mobile Engagement avanzata nell'app Windows Phone ](mobile-engagement-windows-phone-use-engagement-api.md) ) poiché queste statistiche dipendono dall'applicazione.

##Versioni supportate

L'SDK Windows Phone per Engagement può essere integrato solo nelle applicazioni per:

- Windows Phone OS 8.0
- Windows Phone OS 8.1 con Silverlight

L'SDK Windows Phone per Engagement è compatibile con dispositivi Windows Phone 8.0 e 8.1.

##Incorporare l'SDK Engagement nel progetto Windows Phone

Il pacchetto NuGet per Engagement Mobile non è ancora disponibile in linea, pertanto è necessario scaricare l'archivio SDK, decomprimerlo e quindi, in Visual Studio, fare clic con il pulsante destro del mouse sul progetto, passare a "Gestisci pacchetti NuGet", Impostazioni e aggiungere la cartella lib come origine di un nuovo pacchetto.


> [AZURE.IMPORTANT] NuGet è il gestore pacchetto principale per i progetti .net. Scaricherà, aggiungerà e copierà tutto ciò che è necessario per utilizzare l'SDK Engagement. Su WP 8 Engagement utilizza librerie di terze parti (pacchetto di portabilità Microsoft.Bcl.Build v1.0.10 e Microsoft.Bcl v1.1.3) a scopo di portabilità tra piattaforme; NuGet ne chiederà automaticamente l'installazione. È possibile trovare ulteriori informazioni sul [sito Web NuGet](http://docs.nuget.org/docs/start-here/overview).

##Aggiungere le funzionalità

L'SDK Engagement richiede alcune funzionalità dell'SDK Windows Phone per il corretto funzionamento.

Aprire il file  `WMAppManifest.xml`  e assicurarsi che le funzionalità seguenti siano dichiarate nel pannello `Capabilities` :

- `ID_CAP_NETWORKING`
- `ID_CAP_IDENTITY_DEVICE`

##Inizializzare l'SDK Engagement

### Configurazione di Engagement

La configurazione di Engagement è centralizzata nel file  `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

- La stringa di connessione dell'applicazione tra i tag `<connectionString>` e  `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale Azure.

### Inizializzazione di Engagement

Quando si crea un nuovo progetto, viene generato un file `App.xaml.cs` . Questa classe eredita da  `Application` e contiene molti metodi importanti. Verrà inoltre utilizzata per inizializzare l'SDK Engagement.

Modificare `App.xaml.cs`:

- Aggiungere alle istruzioni `using` :

			using Microsoft.Azure.Engagement;

- Inserire `EngagementAgent.Instance.Init` nel metodo `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			 EngagementAgent.Instance.Init();
			}

- Inserire `EngagementAgent.Instance.OnActivated` nel metodo `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			 EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] È vivamente sconsigliata l'aggiunta dell'inizializzazione di Engagement in un altro punto dell'applicazione. Tuttavia, tenere presente che il metodo `EngagementAgent.Instance.Init` viene eseguito in un thread dedicato e non nel thread dell'interfaccia utente.

##Report di base

### Metodo consigliato: eseguire l'overload delle classi `PhoneApplicationPage`

Per attivare il report di tutti i log richiesti da Engagement per il calcolo di utenti, sessioni, attività, arresti anomali del sistema e statistiche tecniche, è possibile semplicemente fare in modo che tutte le sottoclassi `PhoneApplicationPage` ereditino dalle classi 'EngagementPage'.

Di seguito è riportato un esempio di come ottenere questo risultato per una pagina dell'applicazione. È possibile procedere allo stesso modo per tutte le pagine dell'applicazione.

#### File di origine C\ #

Modificare il file `.xaml.cs` della pagina:

- Aggiungere alle istruzioni `using`:

			using Microsoft.Azure.Engagement;

- Sostituire `PhoneApplicationPage` con `EngagementPage`:

**Senza Engagement:**

			namespace Example
			{
			 public partial class ExamplePage : PhoneApplicationPage
			 {
			 [...]
			 }
			}

**Con Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			 public partial class ExamplePage : EngagementPage 
			 {
			 [...]
			 }
			}

> [AZURE.WARNING] Se la pagina eredita dal metodo `OnNavigatedTo`, fare attenzione a consentire la chiamata `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata. In effetti, `EngagementPage` chiama `StartActivity` all'interno del metodo `OnNavigatedTo`.

#### File XAML

Modificare il file `.xaml` della pagina:

- Aggiungere alle dichiarazioni di spazi dei nomi:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

- Sostituire `phone:PhoneApplicationPage` con `engagement:EngagementPage` :

**Senza Engagement:**

			<phone:PhoneApplicationPage>
			 <!-- layout -->
			</phone:PhoneApplicationPage>

**Con Engagement:**

			<engagement:EngagementPage 
			 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			 <!-- layout -->
			</engagement:EngagementPage >

#### Sostituire il comportamento predefinito

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

Ulteriori dettagli sulle informazioni aggiuntive sono disponibili [qui](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Questi metodi vengono chiamati dall'interno del metodo  `OnNavigatedTo` della pagina.

### Metodo alternativo: chiamare  `StartActivity()` manualmente

Se non è possibile o non si desidera eseguire l'overload delle classi  `PhoneApplicationPage`, è possibile invece avviare le attività chiamando direttamente i metodi  `EngagementAgent`.

È consigliabile chiamare  `StartActivity` all'interno del metodo  `OnNavigatedTo` di PhoneApplicationPage.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			 base.OnNavigatedTo(e);
			 EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Assicurarsi che la sessione venga terminata correttamente.
>
> L'SDK Windows Phone chiama automaticamente il metodo  `EndActivity` quando l'applicazione viene chiusa. Di conseguenza, è **ALTAMENTE** consigliabile chiamare il metodo  `StartActivity` ogni volta che l'attività dell'utente cambia e non chiamare **MAI** il metodo  `EndActivity` poiché questo metodo forza la chiusira della sessione corrente.

##Report avanzati

Facoltativamente, è possibile segnalare eventi specifici dell'applicazione, errori e processi. A tale scopo, utilizzare gli altri metodi disponibili nella classe  `EngagementAgent`. L'API Engagement consente di utilizzare tutte le funzionalità avanzate di Engagement.

Per ulteriori informazioni, vedere [Come utilizzare l'API di tag Mobile Engagement avanzata nell'app Windows Phone](mobile-engagement-windows-phone-use-the-engagement-api.md).

##Configurazione avanzata

### Disabilitare la segnalazione automatica degli arresti anomali

È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quindi, quando si verifica un'eccezione non gestita, Engagement non effettuerà alcuna azione.

> [AZURE.WARNING] Se si prevede di disabilitare questa funzionalità, tenere presente che quando si verifica un arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione a seconda del modo in cui è stata dichiarata:

#### Dal file  `EngagementConfiguration.xml` 

Impostare la segnalazione degli arresti anomali su  `false` tra i tag `<reportCrash>` e `</reportCrash>`.

#### Dall'oggetto  `EngagementConfiguration` in fase di esecuzione

Impostare la segnalazione degli arresti anomali su false utilizzando l'oggetto EngagementConfiguration.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### Modalità burst [beta]

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è consigliabile memorizzarli nel buffer e segnalarli tutti insieme a intervalli regolari (in "modalità burst").

A tale scopo, chiamare il metodo:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitoraggio di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di utilizzare una soglia di burst non maggiore di 30000 (30 secondi).

> [AZURE.WARNING] La soglia di burst non può essere configurata per un periodo inferiore a un secondo.  Se si tenta di impostare un valore minore, l'SDK mostrerà una traccia con l'errore e verrà ripristinato automaticamente il valore predefinito, zero secondi,  e l'SDK segnalerà i log in tempo reale.

<!--HONumber=47-->
