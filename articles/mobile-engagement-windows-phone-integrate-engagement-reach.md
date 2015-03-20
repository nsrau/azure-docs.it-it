<properties 
	pageTitle="Integrazione dell'SDK Windows Phone per Azure Mobile Engagement" 
	description="Come integrare Copertura di Engagement in Windows Phone"				
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
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

#Come integrare l'SDK Copertura di Engagement in Windows Phone

È necessario seguire la procedura di integrazione descritta nel documento [Come integrare Engagement in Windows Phone](../mobile-engagement-windows-phone-integrate-engagement/) prima di seguire questa guida.

##Incorporare l'SDK Copertura di Engagement nel progetto Windows Phone

Nessun elemento da aggiungere. Risorse e riferimenti di  `EngagementReach` sono già presenti nel progetto.

> [AZURE.TIP] È possibile personalizzare le immagini della cartella  `Resources` del progetto, soprattutto l'icona del marchio (che per impostazione predefinita è l'icona di Engagement).

##Aggiungere le funzionalità

L'SDK Copertura di Engagement richiede alcune funzionalità aggiuntive.

Aprire il file  `WMAppManifest.xml` e assicurarsi che le funzionalità seguenti siano dichiarate nel pannello  `Capabilities`:

- `ID_CAP_PUSH_NOTIFICATION`
- `ID_CAP_WEBBROWSERCOMPONENT`

##Abilitare il Servizio notifica Push di Microsoft

Per utilizzare il **Servizio notifica Push di Microsoft** (detto MPNS) il file  `WMAppManifest.xml` deve avere un tag `<App />` con un attributo  `Publisher` impostato sul nome del progetto.

##Inizializzare l'SDK Copertura di Engagement

### Configurazione di Engagement

La configurazione di Engagement è centralizzata nel file  `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare la configurazione della copertura:

- *Facoltativamente* indicare se il push nativo (MPNS) è attivato tra i tag `<enableNativePush>` e `</enableNativePush>` (l'impostazione predefinita è `true`).
- *Facoltativamente* indicare il nome del canale di push tra i tag `<channelName>` e `</channelName>`; fornire quello che potrebbe essere in uso nell'applicazione o lasciare il campo vuoto.

Se si desidera specificarlo in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			engagementConfiguration.Reach.EnableNativePush = true; /* [Optional] whether the native push (MPNS) is activated or not. */
			engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME"; /* [Optional] Provide the same channel name that your application may currently use. */
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] È possibile specificare il nome del canale di push MPNS dell'applicazione. Per impostazione predefinita, Engagement crea un nome basato su appId. Non è necessario specificare il nome manualmente, a meno che non si preveda di utilizzare il canale di push fuori da Engagement.

### Inizializzazione di Engagement

Modificare  `App.xaml.cs`:

- Aggiungere alle istruzioni  `using`:

			using Microsoft.Azure.Engagement;

- Inserire  `EngagementReach.Instance.Init` subito dopo  `EngagementAgent.Instance.Init` in  `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			 EngagementAgent.Instance.Init();
			 EngagementReach.Instance.Init();
			}

- Inserire  `EngagementReach.Instance.OnActivated` nel metodo  `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			 EngagementAgent.Instance.OnActivated(e);
			 EngagementReach.Instance.OnActivated(e);
			}

> [AZURE.IMPORTANT]  `EngagementReach.Instance.Init` viene eseguito in un thread dedicato. Non è necessario eseguirlo manualmente.

Invio
----------

Microsoft impone alcune regole per l'utilizzo delle notifiche push.

Dalla documentazione [Criteri di applicazione] di Microsoft, sezione 2.9:

> 2.9.1) L'applicazione deve descrivere innanzitutto le notifiche da fornire e **ottenere l'autorizzazione esplicita dell'utente (consenso)**, e **deve fornire un meccanismo attraverso il quale l'utente può rifiutare esplicitamente la ricezione di notifiche push**. Tutte le notifiche fornite tramite il Servizio notifica Push di Microsoft devono essere coerenti con la descrizione fornita all'utente e conformi a tutti i [criteri di applicazione], [criteri contenuto] e [requisiti aggiuntivi per specifici tipi di applicazioni] applicabili.

**Breve**: È necessario chiedere all'utente di accettare la ricezione delle notifiche push. Aggiungere quindi nelle impostazioni un modo per disattivare le notifiche push.

L'oggetto EngagementReach fornisce due metodi per gestire il consenso/rifiuto,  `EnableNativePush()` e  `DisableNativePush()`. È possibile, ad esempio, creare un'opzione nelle impostazioni con un elemento toggle per disabilitare o abilitare MPNS.

È inoltre possibile disattivare MPNS tramite la configurazione di Engagement \<windows-phone-sdk-reach-configuration\>.

> 2.9.2) L'applicazione che utilizza il Servizio notifica Push di Microsoft non deve sfruttare in modo eccessivo la capacità di rete o la larghezza di banda del Servizio notifica Push di Microsoft o sovraccaricare inutilmente un dispositivo Windows Phone o altro dispositivo o servizio Microsoft con un numero eccessivo di notifiche push, come stabilito da Microsoft a sua ragionevole discrezione, e non deve danneggiare o interferire con le reti o i server Microsoft o i server e le reti di terze parti connesse al Servizio notifica Push di Microsoft.

**Breve**: Non fare un uso eccessivo di notifiche push. Engagement gestisce l'uso delle notifiche push in tal senso.

> 2.9.3) Il Servizio notifica Push di Microsoft non può essere utilizzato per inviare notifiche mission crirical o che possono influire sui questioni di vita o di morte, incluse, senza alcuna limitazione, notifiche correlate a una condizione o a un dispositivo medico. MICROSOFT RIFIUTA ESPRESSAMENTE QUALSIASI GARANZIA CHE L'UTILIZZO DEL SERVIZIO DI NOTIFICA PUSH DI MICROSOFT O CHE IL RECAPITO DELLE NOTIFICHE DEL SERVIZIO DI NOTIFICA PUSH DI MICROSOFT SARÀ ININTERROTTO, PRIVO DI ERRORI O CHE SARÀ ESEGUITO IN TEMPO REALE.

**Breve**: Non fare affidamento su MPNS per inviare informazioni critiche. Engagement utilizza MPNS, pertanto questa regola è valida anche per le campagne create all'interno del front-end di Engagement.

**Non è possibile garantire che l'applicazione supererà il processo di convalida se non si rispettano queste indicazioni.**

##Gestire il push di dati (facoltativo)

Se si desidera che l'applicazione sia in grado di ricevere push di dati di copertura, è necessario implementare due eventi della classe EngagementReach:

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			 Debug.WriteLine("String data push message received: " + body);
			 return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			 Debug.WriteLine("Base64 data push message received: " + encodedBody);
			 // Do something useful with decodedBody like updating an image view
			 return true;
			};

È possibile notare che il callback di ogni metodo restituisce un valore booleano. Engagement invia un feedback per il back-end dopo l'invio del push di dati. Se il callback restituisce false, verrà inviato il feedback  `exit`. In caso contrario, sarà  `action`. Se non è impostato alcun callback per gli eventi, il feedback  `drop` verrà restituito ad Engagement.

> [AZURE.WARNING] Engagement non è in grado di ricevere più feedback per un push di dati. Se si prevede di impostare diversi gestori su un evento, tenere presente che il feedback corrisponderà all'ultimo inviato. In questo caso, è consigliabile restituire sempre lo stesso valore per evitare confusione di feedback sul front-end.

##Personalizzare l'interfaccia utente (facoltativo)

### Primo passaggio

È possibile personalizzare l'interfaccia utente di copertura.

A tale scopo, è necessario creare una sottoclasse della classe  `EngagementReachHandler`.

**Codice di esempio :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			 internal class ExampleReachHandler : EngagementReachHandler
			 {
			 // Override EngagementReachHandler methods depending on your needs
			 }
			}

Quindi, impostare il contenuto del campo  `EngagementReach.Instance.Handler` con l'oggetto personalizzato nella classe  `App.xaml.cs` all'interno del metodo  `Application_Launching`.

**Codice di esempio :**

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			 // your app initialization 
			 EngagementReach.Instance.Handler = new ExampleReachHandler();
			 // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Per impostazione predefinita, Engagement utilizza la propria implementazione di  `EngagementReachHandler`. Non è necessario crearne di proprie e, se ne viene creata una, non è necessario eseguire l'override di ogni metodo. Il comportamento predefinito consiste nel selezionare l'oggetto di base di Engagement.

### Layout

Per impostazione predefinita, Copertura utilizzerà le risorse incorporate della DLL per visualizzare le pagine e le notifiche.

Tuttavia, è possibile decidere di utilizzare le proprie risorse in modo da riflettere il marchio in questi componenti.

È possibile eseguire l'override dei metodi  `EngagementReachHandler` in una sottoclasse per indicare a Engagement di utilizzare i layout:

**Codice di esempio :**

			// In your subclass of EngagementReachHandler
			
			public override string GetTextViewAnnouncementUri()
			{
			 // return the path of your own xaml
			}
			
			public override string GetWebViewAnnouncementUri()
			{
			 // return the path of your own xaml
			}
			
			public override string GetPollUri()
			{
			 // return the path of your own xaml
			}
			
			public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
			{
			 // return a new instance of your own notification
			}

> [AZURE.TIP] Il metodo  `CreateNotification` può restituire null. La notifica non verrà visualizzata e la campagna di copertura verrà eliminata.

Per semplificare l'implementazione di layout, viene fornito anche un xaml che può essere utilizzato come base per il codice. Tale elemento si trova nell'archivio dell'SDK Engagement (/src/reach/).

> [AZURE.WARNING] Le origini fornite da Microsoft sono le stesse. Pertanto, se si desidera modificarle direttamente, non dimenticare di modificare lo spazio dei nomi e il nome.

### Posizione di notifica

Per impostazione predefinita, una notifica in-app viene visualizzata nella parte inferiore sinistra dell'applicazione. È possibile modificare questo comportamento eseguendo l'override del metodo dell'oggetto  `GetNotificationPosition`  `EngagementReachHandler`.

			// In your subclass of EngagementReachHandler
			
			public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
			{
			 // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
			}

Attualmente, è possibile scegliere tra le posizioni  `BOTTOM` (impostazione predefinita) e  `TOP`.

### Messaggio di avvio

Quando un utente fa clic su una notifica del sistema (avviso popup), Engagement avvia l'app, carica il contenuto dei messaggi di push e visualizza la pagina per la campagna corrispondente.

Tra l'avvio dell'applicazione e la visualizzazione della pagina si verifica un ritardo (a seconda della velocità della rete).

Per indicare all'utente che è in corso un caricamento, è necessario fornire un'indicazione visiva, ad esempio una barra o un indicatore di avanzamento. Engagement non è in grado di gestire questa situazione, ma fornisce alcuni gestori.

Per implementare il callback:

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

È possibile impostare il callback nel metodo  `Application_Launching` del file  `App.xaml.cs`, preferibilmente prima della chiamata  `EngagementReach.Instance.Init()`.

> [AZURE.TIP] Ogni gestore viene chiamato dal thread dell'interfaccia utente. Non è necessario preoccuparsi quando si utilizza MessageBox o un elemento correlato all'interfaccia utente.


[Criteri di applicazione]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Criteri di contenuto]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisiti aggiuntivi per i tipi di applicazione specifici]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

<!--HONumber=47-->
