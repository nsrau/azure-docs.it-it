<properties 
	pageTitle="Integrazione di Engagement SDK per app universali di Windows" 
	description="Come integrare Azure Mobile Engagement con le app universali di Windows" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

# Integrazione di Engagement SDK per app universali di Windows

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Questa procedura descrive il modo più semplice per attivare le funzioni di analisi e monitoraggio di Engagement in un'applicazione universale di Windows.

I passaggi seguenti sono sufficienti per attivare la segnalazione dei log necessari per calcolare tutte le statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici. La segnalazione dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve essere eseguita manualmente mediante l'API di Engagement (vedere [Come usare l'API di Engagement in un'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

## Versioni supportate

Mobile Engagement SDK per app universali di Windows può essere integrato solo nelle applicazioni Windows Runtime e UWP (Universal Windows Platform) per:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (versioni per desktop e portatili)

> [AZURE.NOTE] Se si usa Windows Phone Silverlight come piattaforma di destinazione, fare riferimento alla [procedura per l'integrazione di Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## Installare Mobile Engagement SDK per app universali

### Tutte le piattaforme

Mobile Engagement SDK per app di Windows universali è disponibile come pacchetto NuGet denominato *MicrosoftAzure.MobileEngagement*. È possibile installarlo da Gestione pacchetti NuGet di Visual Studio.

### Windows 8.x e Windows Phone 8.1

NuGet distribuisce automaticamente le risorse SDK nella cartella `Resources` nella radice del progetto dell'applicazione.

### Applicazioni UWP di Windows 10

NuGet non esegue ancora la distribuzione automatica di risorse SDK in applicazioni UWP. Fino a quando la funzione di distribuzione delle risorse non verrà reintrodotta in NuGet, sarà necessario eseguire questa operazione manualmente:

1.  Aprire Esplora file.
2.  Passare al percorso seguente (**x.x.x** è la versione di Mobile Engagement che si sta installando): *%USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\**x.x.x**\\content\\win81*
3.  Trascinare la cartella **Risorse** da Esplora file alla radice del progetto in Visual Studio.
4.  In Visual Studio selezionare il progetto e attivare l'icona **Mostra tutti i file** nella parte superiore di **Esplora soluzioni**.
5.  Alcuni file non sono inclusi nel progetto. Per importarli tutti contemporaneamente, fare clic con il pulsante destro del mouse su **Risorse** e scegliere **Escludi dal progetto**, quindi fare nuovamente clic con il pulsante destro del mouse su **Risorse** e scegliere **Includi nel progetto** per includere l'intera cartella. Tutti i file della cartella **Risorse** sono ora inclusi nel progetto.

## Aggiungere le funzionalità

Engagement SDK richiede alcune funzionalità di Windows SDK per funzionare correttamente.

Aprire il file `Package.appxmanifest` e assicurarsi che le seguenti funzionalità siano dichiarate:

-   `Internet (Client)`

## Inizializzare Engagement SDK

### Configurazione di Engagement

La configurazione di Engagement è centralizzata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

-   La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure classico.

### Inizializzazione di Engagement

Quando si crea un nuovo progetto, viene generato un file `App.xaml.cs`. Questa classe eredita da `Application` e contiene molti metodi importanti. Verrà inoltre usata per inizializzare Engagement SDK.

Modificare il file `App.xaml.cs`:

-   Aggiungere quanto segue alle istruzioni `using`:

		using Microsoft.Azure.Engagement;

-   Definire un metodo per condividere l'inizializzazione di Engagement una sola volta per tutte le chiamate:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
		
		  // or
		
		  EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Chiamare `InitEngagement` nel metodo `OnLaunched`:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
          InitEngagement(e);
		}

-   Quando l'applicazione viene avviata usando uno schema personalizzato, un'altra applicazione o la riga di comando, viene chiamato il metodo `OnActivated`. È inoltre necessario avviare l'SDK di Engagement quando viene attivata l'applicazione. A tale scopo, eseguire l'override del metodo `OnActivated`:

		protected override void OnActivated(IActivatedEventArgs args)
		{
          InitEngagement(args);
		}

> [AZURE.IMPORTANT] È altamente sconsigliata l'aggiunta dell'inizializzazione di Engagement in un altro punto dell'applicazione.

## Segnalazione di base

### Metodo consigliato: eseguire l'overload delle classi `Page`

Per attivare la segnalazione di tutti i log richiesti da Engagement per calcolare utenti, sessioni, attività, arresti anomali e statistiche tecniche, è possibile fare in modo che tutte le sottoclassi `Page` ereditino dalle classi `EngagementPage`.

Di seguito è riportato un esempio di come ottenere questo risultato per una pagina dell'applicazione. È possibile procedere allo stesso modo per tutte le pagine dell'applicazione.

#### File di origine C#

Modificare il file `.xaml.cs` della pagina:

-   Aggiungere quanto segue alle istruzioni `using`:

		using Microsoft.Azure.Engagement;

-   Sostituire `Page` con `EngagementPage`:

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

> [AZURE.IMPORTANT] Se la pagina esegue l'override del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata (`EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`).

#### File XAML

Modificare il file `.xaml` della pagina:

-   Aggiungere le dichiarazioni di spazi dei nomi:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Sostituire `Page` con `engagement:EngagementPage`:

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

Per impostazione predefinita, il nome della classe della pagina viene indicato come nome dell'attività, senza elementi aggiuntivi. Se la classe usa il suffisso "Page", Engagement rimuoverà anche questo elemento.

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

Se non si può o non si vuole eseguire l'overload delle classi `Page`, in alternativa è possibile avviare le attività chiamando direttamente i metodi `EngagementAgent`.

È consigliabile chiamare `StartActivity` all'interno del metodo `OnNavigatedTo` della pagina.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]  Assicurarsi che la sessione venga terminata correttamente.
> 
> Windows Universal SDK chiama automaticamente il metodo `EndActivity` quando l'applicazione viene chiusa. È quindi **ALTAMENTE** consigliabile chiamare il metodo `StartActivity` ogni volta che l'attività dell'utente subisce modifiche e non chiamare **MAI** il metodo `EndActivity`, che segnala al server di Engagement che l'utente ha chiuso l'applicazione e influisce così su tutti i log delle applicazioni.

## Segnalazione avanzata

Facoltativamente, è possibile segnalare eventi specifici dell'applicazione, errori e processi. A tale scopo, usare gli altri metodi disponibili nella classe `EngagementAgent`. L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement.

Per altre informazioni, vedere [Come usare l'API di Engagement in un'app di Windows universale](../mobile-engagement-windows-store-use-engagement-api/).

##Configurazione avanzata

### Disabilitare la segnalazione automatica degli arresti anomali

È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quindi, quando si verifica un'eccezione non gestita, Engagement non effettuerà alcuna azione.

> [AZURE.WARNING] Se si prevede di disabilitare questa funzionalità, tenere presente che quando si verifica un arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione in base al modo in cui che è stata dichiarata:

#### Dal file `EngagementConfiguration.xml`

Impostare la segnalazione degli arresti anomali su `false` tra i tag `<reportCrash>` e `</reportCrash>`.

#### Dall'oggetto `EngagementConfiguration` in fase di esecuzione

Impostare la segnalazione degli arresti anomali su false usando l'oggetto EngagementConfiguration.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Modalità burst

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è consigliabile memorizzarli nel buffer e segnalarli tutti insieme a intervalli regolari (in "modalità burst").

A tale scopo, chiamare il metodo:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi verrà arrotondata alla soglia di burst (di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili). Si consiglia di usare una soglia di burst non maggiore di 30000 (30 secondi). Occorre notare che per i log salvati è previsto un limite di 300 elementi. Se l'invio richiede troppo tempo, è possibile che alcuni log vadano persi.

> [AZURE.WARNING] Non è possibile configurare la soglia di burst per un periodo inferiore a 1s. Se si tenta di impostare un valore minore, l'SDK mostrerà una traccia con l'errore e verrà reimpostato automaticamente sul valore predefinito, vale a dire, 0s. In questo modo verrà attivato l'SDK per la segnalazione dei log in tempo reale.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=AcomDC_0128_2016-->