<properties pageTitle="Introduzione alle notifiche push (push legacy) | Mobile Developer Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">Backend .NET</a> | <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">Backend JavaScript</a>
</div>

In questo argomento viene illustrato come usare Servizi mobili di Azure per inviare notifiche push a un'app di Windows Phone 8. 
In questa esercitazione si userà il Servizio di notifica Push di Microsoft per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

>[WACOM.NOTE]In questo argomento sono supportati servizi mobili <em>esistenti</em> che <em>non sono stati ancora aggiornati</em> per l'uso dell'integrazione di Hub di notifica. Quando si crea un <em>nuovo</em> servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. <em>È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile</em>. Dopo aver eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Creazione della tabella Registrations]
2. [Aggiungere notifiche push all'app]
3. [Aggiornare gli script per l'invio di notifiche push]
4. [Inserire dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario disporre di [Visual Studio 2012 Express per Windows Phone] o versione successiva.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

   >[WACOM.NOTE]Quando si inviano ogni giorno più di 500 messaggi per ogni utente, è necessario usare Hub di notifica. Per altre informazioni, vedere <a href="/it-it/manage/services/notification-hubs/getting-started-windows-dotnet/">Introduzione ad Hub di notifica</a>.

## <a name="create-table"></a>Creazione di una nuova tabella

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Aggiungere notifiche push all'app</h2>
		
1. In Visual Studio aprire il file di progetto MainPage.xaml.cs e aggiungere il codice seguente per creare una nuova classe **Registrations**:

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	La proprietà Handle viene utilizzata per archiviare l'URI di canale.

2. Aprire il file App.xaml.cs e aggiungere l'istruzione using seguente:

        using Microsoft.Phone.Notification;

3. Aggiungere il codice seguente al file App.xaml.cs:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Questo codice consente di acquisire e archiviare un canale per una sottoscrizione di notifica push e lo associa al riquadro predefinito dell'app.

	<div class="dev-callout"><b>Nota</b>
		<p>In questa esercitazione il servizio mobile invia una notifica di tipo riquadro capovolto al dispositivo. Quando si invia una notifica di tipo avviso popup, è invece necessario chiamare il metodo <strong>BindToShellToast</strong> sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia <strong>BindToShellTile</strong> che <strong>BindToShellToast</strong> </p>
	</div>
    
4. All'inizio del gestore eventi **Application_Launching** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

        AcquirePushChannel();

   	In tal modo si garantirà che la proprietà **CurrentChannel** venga inizializzata ogni volta che si avvia l'applicazione.


5.	In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID___CAP___PUSH_NOTIFICATION** sia selezionata.

   	![][1]

   	In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.

<h2><a name="update-scripts"></a>Aggiornare gli script insert registrati nel portale di gestione</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Fare clic su **TodoItem**, quindi su **Script** e selezionare **Inserisci**. 

   	![][10]

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutti i canali archiviati nella tabella **Registrations**.

<h2><a name="test"></a>Testare le notifiche push nell'app</h2>

1. In Visual Studio scegliere **Distribuisci soluzione** dal menu **Compila**.

2. Nell'emulatore scorrere verso sinistra per visualizzare l'elenco delle app installate e individuare la nuova app **TodoList**.

3. Toccare e tenere premuto sull'icona dell'app, quindi scegliere **aggiungi a start** dal menu contestuale.

  	![][2]

  	Al menu Start verrà aggiunto un riquadro denominato **TodoList**.

4. Toccare il riquadro **TodoList** per avviare l'app. 

  	![][3]

5. Nell'app immettere il testo "hello push" nella casella di testo, quindi fare clic su **Save**.

   	![][4]

  	Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto.

6. Premere **Start** per tornare al menu corrispondente. 

  	![][5]

  	Si noti che l'applicazione ha ricevuto la notifica push e che il titolo del riquadro è ora **hello push**.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, con il servizio mobile è consigliabile usare Hub di notifica di Azure. Per altre informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

+ [Introduzione ad Hub di notifica]
  <br/>Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

+ [Informazioni su Hub di notifica]
	<br/>Informazioni su come creare e inviare le notifiche push a utenti su più piattaforme.

+ [Inviare notifiche ai sottoscrittori]
	<br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET. 

<!-- Anchors. -->
[Creazione della tabella Registrations]: #create-table
[Aggiornare gli script per l'invio di notifiche push]: #update-scripts
[Aggiungere notifiche push all'app]: #add-push
[Inserire dati per la ricezione di notifiche]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-wp8
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto mpns]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/
[Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Informazioni su Hub di notifica]: /it-it/develop/net/how-to-guides/service-bus-notification-hubs/
[Inviare notifiche ai sottoscrittori]: /it-it/manage/services/notification-hubs/breaking-news-wp8/
[Inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users/
[Inviare notifiche agli utenti tra piattaforme diverse]: /it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services/
