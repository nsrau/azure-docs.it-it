<properties
	pageTitle="Aggiungere notifiche push all'app di Servizi mobili (Windows Phone) | Microsoft Azure"
	description="Informazioni su come usare Hub di notifica e Servizi mobili di Azure per inviare notifiche push all'app di Windows Phone."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Panoramica

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Windows Phone Silverlight. In questa esercitazione si usa Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

Questa esercitazione è basata sull'app di esempio TodoList. Prima di iniziare questa esercitazione, è necessario completare l'argomento [Aggiungere Servizi mobili a un'app esistente] per connettere il progetto al servizio mobile. Se non è stato collegato un servizio mobile, la procedura guidata Aggiungi notifica push può creare automaticamente la connessione.

>[AZURE.NOTE] Per inviare notifiche push a un'app di Windows Phone 8.1 Store, vedere la versione per [app di Windows Store](mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md) dell'esercitazione.

##<a id="update-app"></a> Aggiornare l'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1. In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.Phone.Notification;

3. Aggiungere il codice seguente al file App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio notifica Push Microsoft (MPNS) usato da Windows Phone 8.x "Silverlight", quindi di registrarlo per le notifiche push.

	>[AZURE.NOTE]In questa esercitazione il servizio mobile invia una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale.

4. All'inizio del gestore dell'evento **Application\_Launching** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

        AcquirePushChannel();

	In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

5. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

6.	In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** sia selezionata.

   	![Abilitazione delle notifiche in Visual Studio](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

##<a id="update-scripts"></a> Aggiornare gli script del server per l'invio di notifiche push

È infine necessario aggiornare lo script registrato nell'operazione di inserimento nella tabella TodoItem per l'invio di notifiche.

1. Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

2. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
		    '</wp:Text2></wp:Toast></wp:Notification>';

		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutte le app per Windows Phone registrate dopo la corretta esecuzione dell'inserimento.

3. Fare clic sulla scheda **Push**, selezionare **Abilita notifiche push non autenticate** e quindi fare clic su **Salva**.

	In questo modo il servizio mobile verrà abilitato per la connessione al Servizio di notifica Push di Microsof in modalità senza autenticazione per l'invio di notifiche push.

	>[AZURE.NOTE]In questa esercitazione verrà usato il Servizio notifica Push Microsoft in modalità senza autenticazione. In tale modalità, il Servizio notifica Push Microsoft limita il numero di notifiche che è possibile inviare a un canale di dispositivo. Per rimuovere questa restrizione è necessario generare e caricare un certificato facendo clic su **Upload** e selezionando il certificato. Per ulteriori informazioni sulla generazione del certificato, vedere [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone].

##<a id="test"></a> Testare le notifiche push nell'app

1. In Visual Studio premere F5 per eseguire l'app.

    >[AZURE.NOTE] Durante il test sull'emulatore di Windows Phone è possibile che venga generata un'eccezione RegistrationAuthorizationException e che venga restituita una risposta 401 (Non autorizzato). Questa situazione può verificarsi durante la chiamata a `RegisterNativeAsync()` a causa della modalità errata con cui l'orologio dell'emulatore di Windows Phone viene sincronizzato con il PC host. Di conseguenza, è possibile che un token di sicurezza venga rifiutato. Per risolvere questo problema, è sufficiente impostare manualmente l'orologio prima del test.

5. Nell'app immettere il testo "hello push" nella casella di testo, fare clic su **Salva**, quindi fare immediatamente clic sul pulsante Avvia o Indietro per chiudere l'app.

   	![Immissione del testo nell'app](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

  	Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto. Si noti che il dispositivo riceve una notifica di tipo avviso popup con il testo **hello push**.

	![Ricezione di una notifica di avviso popup](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]La notifica non viene ricevuta se l'app è ancora aperta. Per ricevere una notifica di tipo avviso popup mentre l'app è attiva, è necessario gestire l'evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

+ [Inviare notifiche di trasmissione ai sottoscrittori](../notification-hubs/notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie a cui sono interessati.

+ [Inviare notifiche indipendenti dalla piattaforma ad altri sottoscrittori](../notification-hubs/notification-hubs-aspnet-cross-platform-notification.md) <br/>Informazioni su come usare i modelli per inviare notifiche push dal servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.


Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Hub di notifica di Azure - Linee guida sulla diagnostica](../notification-hubs/notification-hubs-push-notification-fixer.md) <br/>Informazioni sulla risoluzione dei problemi relativi alle notifiche push.

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi tramite Servizi mobili.

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Riferimento per i concetti e le procedure di Servizi mobili con .NET] <br/>Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

* [Informazioni di riferimento sugli script del server di Servizi mobili] <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Aggiungere Servizi mobili a un'app esistente]: mobile-services-windows-phone-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-windows-phone-get-started-users.md

[Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Riferimento per i concetti e le procedure di Servizi mobili con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Informazioni su Hub di notifica]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0727_2016-->