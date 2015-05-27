<properties 
	pageTitle="Integrazione di Mobile Engagement SDK per Windows Phone Silverlight" 
	description="Come integrare Azure Mobile Engagement con le app per Windows Phone Silverlight" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="piyushjo" />

#Integrazione di Mobile Engagement SDK per Windows Phone Silverlight

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Questa procedura descrive il modo più semplice per attivare le funzioni di analisi e monitoraggio di Azure Mobile Engagement in un'applicazione per Windows Phone Silverlight.

I passaggi seguenti sono sufficienti per attivare la segnalazione dei log necessari per calcolare tutte le statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici. La segnalazione dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve essere eseguita manualmente mediante l'API di Engagement (vedere [Come usare l'API di Engagement in Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

##Versioni supportate

Mobile Engagement SDK per Windows Silverlight può essere integrato solo nelle applicazioni per:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE]Se si usa Windows Phone 8.1 (non Silverlight) come piattaforma di destinazione, fare riferimento alla [procedura per l'integrazione nelle app di Windows universali](mobile-engagement-windows-store-integrate-engagement.md).

##Installare Mobile Engagement SDK per Windows Silverlight

Mobile Engagement SDK per Windows Silverlight è disponibile come pacchetto NuGet denominato *MicrosoftAzure.MobileEngagement*. È possibile installarlo da Gestione pacchetti NuGet di Visual Studio.

##Aggiungere le funzionalità

L'SDK di Engagement richiede alcune funzionalità di Windows Phone Silverlight SDK per funzionare correttamente.

Aprire il file `WMAppManifest.xml` e assicurarsi che le funzionalità seguenti siano dichiarate nel pannello `Capabilities`:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Inizializzare l'SDK di Engagement

### Configurazione di Engagement

La configurazione di Engagement è centralizzata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

-   La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di gestione di Azure.

### Inizializzazione di Engagement

Quando si crea un nuovo progetto, viene generato un file `App.xaml.cs`. Questa classe eredita da `Application` e contiene molti metodi importanti. Verrà inoltre usata per inizializzare l'SDK di Engagement.

Modificare il file `App.xaml.cs`:

-   Aggiungere quanto segue alle istruzioni `using`:

		using Microsoft.Azure.Engagement;

-   Inserire `EngagementAgent.Instance.Init` nel metodo `Application_Launching`:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		  EngagementAgent.Instance.Init();
		}

-   Inserire `EngagementAgent.Instance.OnActivated` nel metodo `Application_Activated`:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		}

> [AZURE.WARNING]È altamente sconsigliata l'aggiunta dell'inizializzazione di Engagement in un altro punto dell'applicazione. Tenere tuttavia presente che il metodo `EngagementAgent.Instance.Init` viene eseguito in un thread dedicato e non nel thread dell'interfaccia utente.

##Segnalazione di base

### Metodo consigliato: eseguire l'overload delle classi `PhoneApplicationPage`

Per attivare la segnalazione di tutti i log richiesti da Engagement per calcolare utenti, sessioni, attività, arresti anomali e statistiche tecniche, è possibile fare in modo che tutte le sottoclassi `PhoneApplicationPage` ereditino dalle classi `EngagementPage`.

Di seguito è riportato un esempio di come ottenere questo risultato per una pagina dell'applicazione. È possibile procedere allo stesso modo per tutte le pagine dell'applicazione.

#### File di origine C#

Modificare il file `.xaml.cs` della pagina:

-   Aggiungere quanto segue alle istruzioni `using`:

		using Microsoft.Azure.Engagement;

-   Sostituire `PhoneApplicationPage` con `EngagementPage`:

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

> [AZURE.WARNING]Se la pagina eredita dal metodo `OnNavigatedTo`, fare attenzione a consentire la chiamata `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata. In effetti, `EngagementPage` chiama `StartActivity` all'interno del metodo `OnNavigatedTo`.

#### File XAML

Modificare il file `.xaml` della pagina:

-   Aggiungere quanto segue alle dichiarazioni di spazi dei nomi:

		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Sostituire `phone:PhoneApplicationPage` con `engagement:EngagementPage`:

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

Questi metodi vengono chiamati dall'interno del metodo `OnNavigatedTo` della pagina.

### Metodo alternativo: chiamare `StartActivity()` manualmente

Se non si può o non si vuole eseguire l'overload delle classi `PhoneApplicationPage`, in alternativa è possibile avviare le attività chiamando direttamente i metodi `EngagementAgent`.

È consigliabile chiamare `StartActivity` all'interno del metodo `OnNavigatedTo` di PhoneApplicationPage.

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		   base.OnNavigatedTo(e);
		   EngagementAgent.Instance.StartActivity("MyPage");
		}

> [AZURE.IMPORTANT]Assicurarsi che la sessione venga terminata correttamente.
>
> L'SDK chiama automaticamente il metodo `EndActivity` quando viene chiusa l'applicazione. Di conseguenza, è **ALTAMENTE** consigliabile chiamare il metodo `StartActivity` ogni volta che l'attività dell'utente cambia e non chiamare **MAI** il metodo `EndActivity` poiché questo metodo forza la chiusura della sessione corrente.

##Segnalazione avanzata

Facoltativamente, è possibile segnalare eventi specifici dell'applicazione, errori e processi. A tale scopo, usare gli altri metodi disponibili nella classe `EngagementAgent`. L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement.

Per altre informazioni, vedere [Come usare l'API di Engagement in Windows Phone Silverlight](../mobile-engagement-windows-phone-use-engagement-api/).

##Configurazione avanzata

### Disabilitare la segnalazione automatica degli arresti anomali

È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quindi, quando si verifica un'eccezione non gestita, Engagement non effettuerà alcuna azione.

> [AZURE.WARNING]Se si prevede di disabilitare questa funzionalità, tenere presente che in caso di arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione a seconda del modo in cui è stata dichiarata:

#### Dal file `EngagementConfiguration.xml`

Impostare la segnalazione degli arresti anomali su `false` tra i tag `<reportCrash>` e `</reportCrash>`.

#### Dall'oggetto `EngagementConfiguration` in fase di esecuzione

Impostare la segnalazione degli arresti anomali su false utilizzando l'oggetto EngagementConfiguration.

		/* Engagement configuration. */

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### Modalità burst

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è consigliabile memorizzarli nel buffer e segnalarli tutti insieme a intervalli regolari (in "modalità burst").

A tale scopo, chiamare il metodo:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di usare una soglia di burst non maggiore di 30000 (30 secondi).

> [AZURE.WARNING]La soglia di burst non può essere configurata per un periodo inferiore a un secondo. Se si tenta di impostare un valore minore, l'SDK mostrerà una traccia con l'errore e verrà ripristinato automaticamente il valore predefinito, ovvero zero secondi. In questo modo verrà attivato l'SDK per la segnalazione dei log in tempo reale.
<!--HONumber=54-->