<properties 
	pageTitle="Integrazione di Copertura dell'SDK Windows Store per Azure Mobile Engagement" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Come integrare Copertura di Engagement in Windows Phone

È necessario seguire la procedura di integrazione descritta nel documento [Come integrare Engagement in Windows](../mobile-engagement-windows-store-integrate-engagement/) prima di seguire questa guida.

##Incorporare l'SDK Copertura di Engagement nel progetto Windows 

Nessun elemento da aggiungere. Risorse e riferimenti di `EngagementReach`  sono già presenti nel progetto.

> [AZURE.TIP] È possibile personalizzare le immagini della cartella `Resources` del progetto, soprattutto l'icona del marchio (che per impostazione predefinita è l'icona di Engagement).

##Aggiungere le funzionalità

L'SDK Copertura di Engagement richiede alcune funzionalità aggiuntive.

Aprire il file `Package.appxmanifest` e passare al pannello `Dichiarazioni`pannello. Selezionare l'opzione  `Associazioni tipi di file`  nella casella di scorrimento `Dichiarazioni disponibili` e aggiungerla. Impostare il nome su `engagement_reach_content` e il tipo di file su `.txt`.

##Abilitare il Servizio notifica di Windows

Per utilizzare il **Servizio notifica di Windows**  (definito WNS) nel file `Package.appxmanifest` su `Application UI` fare clic su `Tutti gli asset delle immagini` nella casella sinistra. A destra della casella in `Notifiche`, modificare `popup supportati` da `(non impostato)`  a `Sì`.

È inoltre necessario sincronizzare l'app con il proprio account Microsoft e con la piattaforma di Engagement. Nel front-end di Engagement passare all'impostazione dell'app in 'push nativo' e incollare le credenziali. Successivamente, fare clic sul progetto, selezionare  `store`  e `Associate App with the Store...`.

##Inizializzare l'SDK Copertura di Engagement

Modificare `App.xaml.cs`:

- Aggiungere alle istruzioni `using`:

			using Microsoft.Azure.Engagement;

- Inserire `EngagementReach.Instance.Init`  subito dopo `EngagementAgent.Instance.Init` in `OnLaunched` :

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			 EngagementReach.Instance.Init(args);
			}

> [AZURE.NOTE] `EngagementReach.Instance.Init`  viene eseguito in un thread dedicato. Non è necessario eseguirlo manualmente.

- Se si desidera avviare la copertura di Engagement quando viene attivata l'app, eseguire l'override del metodo `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			 EngagementReach.Instance.Init(args);
			}

> [AZURE.TIP] È possibile specificare il nome del canale di push WNS dell'applicazione nel file `Resources\EngagementConfiguration.xml` del progetto in `<channelName></channelName>`. Per impostazione predefinita, Engagement crea un nome basato su appId. Non è necessario specificare il nome manualmente, a meno che non si preveda di utilizzare il canale di push fuori da Engagement.

##Integrazione

Engagement fornisce due modi implementare per annuncio e notifica di copertura. Integrazione di sovrapposizione e l'integrazione di visualizzazione Web.

[Integrazione di sovrapposizione](#overlay-integration) non richiede una grande quantità di codice da scrivere nell'applicazione. È sufficiente contrassegnare i file di pagine, xaml e cs con EngagementPageOverlay. Inoltre se si personalizza la visualizzazione predefinita di Engagement la personalizzazione verrà condivisa con tutte le pagine contrassegnate e sarà definita solo una volta. Tuttavia, se le pagine devono ereditare da oggetto diverso da EngagementPageOverlay si è costretti a utilizzare l'integrazione di visualizzazione Web.

L'[integrazione di visualizzazione Web](#web-view-integration) è più complessa da implementare. Tuttavia, se le pagine dell'app devono ereditare da un oggetto diverso da "Pagina", è necessario integrare la visualizzazione Web e il relativo comportamento.

Una procedura ottimale nelle applicazioni di archivio di Windows 8.1 consiste nell'aggiunta di un elemento `<Grid></Grid>` di primo livello per racchiudere tutto il contenuto della pagina. Per l'integrazione di visualizzazione Web, è sufficiente aggiungere Webview come figlio della griglia. Se è necessario impostare il componente Engagement altrove, tenere presente che è necessario gestire manualmente le dimensioni di visualizzazione.

### Integrazione di sovrapposizione

Engagement fornisce una sovrapposizione per la visualizzazione di notifiche e avvisi.

Se si desidera utilizzarla, non usare  l'[integrazione di visualizzazione Web](#web-view-integration).

Nel file con estensione XAML modificare il riferimento EngagementPage in EngagementPageOverlay

- Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay"

- Sostituire `engagement:EngagementPage` con `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

			<engagement:EngagementPage 
			 xmlns:engagement="using:Microsoft.Azure.Engagement">
			
			 <!-- layout -->
			</engagement:EngagementPage>

**Con EngagementPageOverlay:**

			<engagement:EngagementPageOverlay 
			 xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			
			 <!-- layout -->
			</engagement:EngagementPageOverlay>

> **Con EngagementPageOverlay per 8.1:**

			<engagement:EngagementPageOverlay 
			 xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			 <Grid>
			 <!-- layout -->
			 </Grid>
			</engagement:EngagementPageOverlay>

Quindi nel file con estensione CS contrassegnare la pagina in "EngagementPageOverlay" anziché "EngagementPage" e importare "Microsoft.Azure.Engagement.Overlay".

			using Microsoft.Azure.Engagement.Overlay;

- Sostituire `EngagementPage` con `EngagementPageOverlay`:

**Con EngagementPage:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			 public sealed partial class ExamplePage : EngagementPage
			 {
			 [...]
			 }
			}

**Con EngagementPageOverlay:**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			 public sealed partial class ExamplePage : EngagementPageOverlay 
			 {
			 [...]
			 }
			}

Ora questa pagina utilizza il meccanismo di sovrapposizione di Engagement, non è necessario inserire una visualizzazione Web.

La sovrapposizione di Engagement utilizza il primo elemento "Grid" trovato nel file xaml per aggiungere due visualizzazioni Web nella pagina. Se si desidera individuare dove verranno impostate le visualizzazioni Web è possibile definire una griglia denominata "EngagementGrid" analoga alla seguente:

			<Grid x:Name="EngagementGrid"></Grid>

È possibile personalizzare la notifica e l'annuncio di sovrapposizione direttamente nei relativi file xaml e cs:

- `EngagementAnnouncement.html` : Il progetto html della visualizzazione Web `Annuncio` .
- `EngagementOverlayAnnouncement.xaml` : Il progetto xaml `Annuncio` .
- `EngagementOverlayAnnouncement.xaml.cs` : Il codice collegato `EngagementOverlayAnnouncement.xaml` .
- `EngagementNotification.html` : Il progetto html della visualizza Web `Notifica`.
- `EngagementOverlayNotification.xaml` : Il progetto xaml `Notifica` .
- `EngagementOverlayNotification.xaml.cs` : Il codice collegato `EngagementOverlayNotification.xaml`.
- `EngagementPageOverlay.cs` : Il codice di visualizzazione annunci e notifiche di  `Sovrapposizione`.

### Integrazione di visualizzazione Web

Se si desidera utilizzarla, non usare [Overlay integration](#overlay-integration).

Per visualizzare il contenuto di Engagement è necessario integrare i due elementi WebView xaml in ogni pagina in cui occorre visualizzare notifiche e avvisi. Aggiungere quindi questo codice nel file xaml:

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Per l'integrazione 8.1:**

			<engagement:EngagementPage
			 xmlns:engagement="using:Microsoft.Azure.Engagement">
			 <Grid>
			 <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			 <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			 <!-- layout -->
			 </Grid>
			</engagement:EngagementPage>

Il file con estensione cs deve apparire come segue:

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			 public ExampleEngagementReachPage()
			 {
			 this.InitializeComponent();
			
			 /* Set your webview elements to the correct size */
			 SetWebView(width, height);
			
			 Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			 }
			
			 #region to implement
			 /* Allow webview script to notify system */
			 private void scriptEvent(object sender, NotifyEventArgs e)
			 {
			 }
			
			 /* When page is left ensure to detach SizeChanged handler */
			 protected override void OnNavigatedFrom(NavigationEventArgs e)
			 {
			 Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			 base.OnNavigatedFrom(e);
			 }
			
			 /* "width" is the current width of your application display */
			 double width = Window.Current.Bounds.Width;
			
			 /* "height" is the current height of your application display */
			 double height = Window.Current.Bounds.Height;
			
			 /// <summary>
			 /// Set your webview elements to the correct size
			 /// </summary>
			 /// <param name="width">The width of your current display</param>
			 /// <param name="height">The height of your current display</param>
			 private void SetWebView(double width, double height)
			 {
			 #pragma warning disable 4014
			 CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			 () =>
			 {
			 this.engagement_notification_content.Width = width;
			 this.engagement_announcement_content.Width = width;
			 this.engagement_announcement_content.Height = height;
			 });
			 }
			
			 /// <summary>
			 /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			 /// </summary>
			 /// <param name="sender">Original event trigger</param>
			 /// <param name="e">Window Size Changed Event argument</param>
			 private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			 {
			 double width = e.Size.Width;
			 double height = e.Size.Height;
			
			 /* Set your webview elements to the correct size */
			 SetWebView(width, height);
			 }
			 #endregion
			}

> Questa implementazione ha incorporato il ridimensionamento di WebView quando lo schermo del dispositivo è attivo.

##Gestire il push di dati (facoltativo)

Se si desidera che l'applicazione sia in grado di ricevere push di dati di copertura, è necessario implementare due eventi della classe EngagementReach:

in App.xaml.cs in "Public App(){}" aggiungere:

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

È possibile notare che il callback di ogni metodo restituisce un valore booleano. Engagement invia un feedback per il back-end dopo l'invio del push di dati. Se il callback restituisce false, sarà inviato il feedback  `exit`. In caso contrario, il feedback sarà  `action`. Se nessun callback è impostato per gli eventi, a Engagement sarà restituito il feedback  `drop`.

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

Quindi, impostare il contenuto del campo  `EngagementReach.Instance.Handler` con l'oggetto personalizzato nella classe  `App.xaml.cs` all'interno del metodo )`.

**Codice di esempio :**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			 // your app initialization 
			 EngagementReach.Instance.Handler = new ExampleReachHandler();
			 // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Per impostazione predefinita, Engagement utilizza la propria implementazione di  `EngagementReachHandler`.
> Non è necessario crearne di proprie e, se ne viene creata una, non è necessario eseguire l'override di ogni metodo. Il comportamento predefinito consiste nel selezionare l'oggetto di base di Engagement.

### Visualizzazione Web

Per impostazione predefinita, Copertura utilizzerà le risorse incorporate della DLL per visualizzare le pagine e le notifiche.

Per fornire possibilità di personalizzazione completa è utilizzata solo la visualizzazione web. Se si desidera personalizzare i layout, eseguire direttamente l'override dei file di risorse  `EngagementAnnouncement.html` e  `EngagementNotification.html`. Engagement richiede tutto il codice in `<body></body>` per un'esecuzione corretta. È possibile, tuttavia, aggiungere tag esterni a  `engagement_webview_area` 

nonché utilizzare le proprie risorse.

È possibile eseguire l'override dei metodi  `EngagementReachHandler` in una sottoclasse per indicare a Engagement di utilizzare i layout, ma fare attenzione al meccanismo di Engagement incorporato:

**Codice di esempio :**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			 return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			 return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			 return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			 return base.GetNotfificationName();
			}


Per impostazione predefinita, AnnouncementHTML è  `ms-appx-web:///Resources/EngagementAnnouncement.html`. Rappresenta il file html per il progetto del contenuto di un messaggio push (annuncio di testo, annuncio Web e annuncio di sondaggio). AnnouncementName è  `engagement_announcement_content`. È il nome del progetto webview nella pagina xaml.

NotfificationHTML è  `ms-appx-web:///Resources/EngagementNotification.html`. Rappresenta il file html per il progetto la notifica di un messaggio di push. NotfificationName è  `engagement_notification_content`. È il nome del progetto webview nella pagina xaml.

### Personalizzazione

È possibile personalizzare la visualizzazione Web di notifiche e annunci nel modo desiderato se si conserva l'oggetto Engagement. Tenere presente che l'oggetto webview è descritto tre volte. La prima volta in xaml, la seconda nel file cs nel metodo "setwebview()" e la terza volta nel file html.

- Nel file xaml si descrive il componente di webview del layout grafico corrente.
- Nel file cs è possibile definire "setwebview()" in cui è possibile impostare la dimensione delle due webview (notifica, annuncio). Questa procedura è molto efficace per il ridimensionamento dell'applicazione.
- Nel file html di Engagement viene descritto il contenuto della visualizzazione Web, il progetto e le posizioni degli elementi.

### Messaggio di avvio

Quando un utente fa clic su una notifica del sistema (avviso popup), Engagement avvia l'applicazione, carica il contenuto dei messaggi di push e visualizza la pagina per la campagna corrispondente.

Tra l'avvio dell'applicazione e la visualizzazione della pagina si verifica un ritardo (a seconda della velocità della rete).

Per indicare all'utente che è in corso un caricamento, è necessario fornire un'indicazione visiva, ad esempio una barra o un indicatore di avanzamento. Engagement non è in grado di gestire questa situazione, ma fornisce alcuni gestori.

Per implementare il callback, in App.xaml.cs in "Public App(){}" aggiungere:

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

È possibile impostare il callback nel metodo "App pubblica (){}" del file  `App.xaml.cs`, preferibilmente prima della chiamata  `EngagementReach.Instance.Init()`.

> [AZURE.TIP] Ogni gestore viene chiamato dal thread dell'interfaccia utente. Non è necessario preoccuparsi quando si utilizza MessageBox o un elemento correlato all'interfaccia utente.

##Suggerimento per lo schema

È possibile utilizzare uno schema personalizzato. È possibile inviare un tipo diverso di URI dal front-end di Engagement da utilizzare nell'applicazione Engagement. Lo schema predefinito come  `http, ftp, ...` è gestito da Windows. Una finestra chiederà se vi sono applicazioni predefinite installate sul dispositivo. È possibile utilizzare altri schemi come schema applicazione. È inoltre possibile utilizzare uno schema personalizzato per l'applicazione.

Il metodo semplice per impostare uno schema personalizzato nell'applicazione consiste nell'aprire  `Package.appxmanifest` e andare al pannello  `Dichiarazioni`. Selezionare l'opzione  `Protocollo` nella casella di scorrimento Dichiarazioni disponibili e aggiungerla. Modificare il campo  `Nome` con il nuovo protocollo desiderato.

Per utilizzare questo protocollo modificare  `App.xaml.cs` con il metodo  `OnActivated` e non dimenticare di inizializzare Engagement anche qui:

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			 /* Init engagement like it was launch by a custom uri scheme */
			 EngagementAgent.Instance.Init(args);
			 EngagementReach.Instance.Init(args);
			
			 //TODO design action to do when app is launch
			
			 #region Custom scheme use
			 if (args.Kind == ActivationKind.Protocol)
			 {
			 ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			 if (myProtocol.Uri.Scheme.Equals("protocolName"))
			 {
			 string path = myProtocol.Uri.AbsolutePath;
			 }
			 }
			 #endregion

<!--HONumber=47-->
