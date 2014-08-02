<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

Introduzione alle notifiche push in Servizi mobili tramite Visual Studio 2012
=============================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

**Nota**

In questa esercitazione verranno aggiunte le notifiche push a un'app di Windows Store creata in Visual Studio 2012. In Visual Studio 2013 sono disponibili nuove funzionalità che semplificano la configurazione delle notifiche push nell'app di Windows Store tramite Servizi mobili. Per la versione di Visual Studio 2013, vedere [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili](#register)
2.  [Creazione della tabella Registrations](#create-table)
3.  [Aggiunta di notifiche push all'app](#add-push)
4.  [Aggiornamento degli script per l'invio di notifiche push](#update-scripts)
5.  [Inserimento di dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8
-   Account di Windows Store attivo

Questa esercitazione si basa sull'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet). Prima di iniziare questa esercitazione, è necessario completare [l'esercitazione seguente](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

Registrazione dell'appRegistrazione dell'app di Windows Store
-------------------------------------------------------------

Per poter inviare notifiche push ad app di Windows Store da Servizi mobili, è necessario inviare l'app a Windows Store e quindi configurare il servizio mobile per l'integrazione con Servizi notifica Push Windows.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Il servizio mobile e l'app sono ora configurati per funzionare con Servizi notifica Push Windows. Ora si creerà una nuova tabella in cui archiviare le registrazioni.

Creazione di una nuova tabella
------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Aggiunta di notifiche pushAggiunta di notifiche push all'app
------------------------------------------------------------

1.  Aprire il file di progetto MainPage.xaml.cs e aggiungere il codice seguente per creare una nuova classe **Registrations**:

         public class Registrations
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "handle")]
             public string Handle { get; set; }
         }

    La proprietà Handle viene utilizzata per archiviare l'URI di canale.

2.  Aprire il file App.xaml.cs e aggiungere l'istruzione using seguente:

         using Windows.Networking.PushNotifications;

3.  Aggiungere il codice seguente al file App.xaml.cs:

         private async void AcquirePushChannel()
         {
            CurrentChannel = 
                await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

    Questo codice inserisce il canale corrente nella tabella Registrations.

4.  All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

         AcquirePushChannel();

    In tal modo si garantirà che la proprietà **CurrentChannel** venga inizializzata ogni volta che si avvia l'applicazione.

5.  Aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

   	![][15]

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. 

Aggiornamento dello script insertAggiornamento dello script insert registrato nel portale di gestione
-----------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

   	![][5]

2.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

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
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
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

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][13]

   	Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

   	![][14]

Passaggi successivi
-------------------

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, con il servizio mobile è consigliabile utilizzare Hub di notifica di Azure. Per ulteriori informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Informazioni su Hub di notifica](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    Informazioni su come creare e inviare le notifiche push a utenti su più piattaforme.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/)
    Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


<!-- Images. -->





[5]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
