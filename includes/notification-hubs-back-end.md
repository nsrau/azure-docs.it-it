In questa sezione viene illustrato come inviare notifiche in due modi
diversi:

* [Da un'app console](#console)
* [Da Servizi mobili](#mobile-services)

Entrambi i back-end inviano notifiche sia a Windows Store sia a
dispositivi iOS. È possibile inviare notifiche da qualsiasi back-end
tramite l'[interfaccia REST degli Hub di notifica][1].

<h3><a name="console"></a>Per inviare notifiche da un'app console in C#</h3>


Se è stata creata un'app console quando è stata completata
l'esercitazione [Introduzione agli Hub di
notifica](/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/),
ignorare i passaggi da 1 a 3.

1.  In Visual Studio creare una nuova applicazione console in Visual C#:
    
       ![][13]

2.  Nel menu principale di Visual Studio fare clic su **Strumenti**,
    **Gestione pacchetti di librerie** e **Console di Gestione
    pacchetti**, quindi nella finestra di tipo console digitare quanto
    segue e premere **INVIO**:
    
         Install-Package WindowsAzure.ServiceBus
    
    Verrà aggiunto un riferimento ad Azure Service Bus SDK con il
    [pacchetto NuGet WindowsAzure.ServiceBus][2].

3.  Aprire il file Program.cs e aggiungere l'istruzione `using`
    seguente:
    
         using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente oppure
    sostituirlo se esiste già:
    
         private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";
                    // Send a WNS notification using the template.            
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";
                    // Send an MPNS notification using the template.            
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    // Send an APNS notification using the template.
                    await hub.SendAppleNativeNotificationAsync(alert, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }
    
    Con questo codice vengono inviate notifiche per ognuno dei sei tag
    della matrice di stringhe a Windows Store, Windows Phone e
    dispositivi iOS. Con i tag è possibile assicurarsi che i dispositivi
    ricevano notifiche solo per le categorie registrate.
		<div class="dev-callout">

	**Nota**
	
	Questo codice back-end supporta Windows Store, Windows Phone e
	client iOS. I metodi Send restituiscono una risposta di errore se
l

5.  Nel codice precedente sostituire i segnaposto `<hub
     name>` e `<connection  string
    with full access>` con il nome dell'hub di notifica e la stringa di
    connessione per *DefaultFullSharedAccessSignature* ottenuta in
    precedenza.

6.  Aggiungere le righe seguenti nel metodo **Main**:
    
          SendNotificationAsync();
          Console.ReadLine();

È ora possibile procedere con la sezione [Esecuzione dell'app e
generazione di notifiche](#test-app).
### <a name="mobile-services"></a>Per inviare notifiche da Servizi mobili

Per inviare una notifica tramite un servizio mobile, eseguire le
operazioni seguenti:

1.  Completare l'esercitazione [Introduzione a Servizi
    mobili](/en-us/develop/mobile/tutorials/get-started/#create-new-service)
    per creare il servizio mobile.

2.  Accedere al [portale di gestione di Azure][3], fare clic su Servizi
    mobili e quindi sul servizio mobile.

3.  Fare clic sulla scheda **Scheduler** e quindi su **Create**.
    
       ![][15]

4.  In **Create new job** digitare un nome, selezionare **On demand** e
    quindi fare clic sul segno di spunta per accettare.
    
       ![][16]

5.  Dopo la creazione del processo, fare clic sul relativo nome e quindi
    nella scheda **Script** inserire lo script seguente all'interno
    della funzione del processo pianificato:
    
        var azure = require('azure');
	    var notificationHubService = azure.createNotificationHubService(
				'<hub name>', 
				'<connection string with full access>');

   		 // Create an array of breaking news categories.
		    var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
		    for (var i = 0; i < categories.length; i++) {
		        // Send a WNS notification.
		        notificationHubService.wns.sendToastText01(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send a MPNS notification.
		        notificationHubService.mpns.sendToast(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send an APNS notification.
		        notificationHubService.apns.send(categories[i], {
		            alert: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		    }
    
    Con questo codice vengono inviate notifiche per ognuno dei sei tag
    della matrice di stringhe a Windows Store, Windows Phone e
    dispositivi iOS. Con i tag è possibile assicurarsi che i dispositivi
    ricevano notifiche solo per le categorie registrate.

6.  Nel codice precedente sostituire i segnaposto `<hub
     name>` e `<connection  string
    with full access>` con il nome dell'hub di notifica e la stringa di
    connessione per *DefaultFullSharedAccessSignature* ottenuta in
    precedenza.

7.  Aggiungere la funzione di supporto seguente dopo la funzione del
    processo pianificato, quindi fare clic su **Save**:

         function sendComplete(error) {
 		   if (error) {
	            // An exception is raised when there are no devices registered for 
	            // the iOS, Windows Store, or Windows Phone platforms. Consider using template 
	            // notifications instead.
	            //console.warn("Notification failed:" + error);
	        }
	    }

	<div class="dev-callout">

**Nota**

Questo codice supporta Windows Store, Windows Phone e client iOS. I metodi Send restituiscono una risposta di errore se per una determinata piattaforma non esiste una registrazione. Per evitare questo problema, è consigliabile utilizzare le registrazioni modello per inviare una singola notifica a più piattaforme. Per un esempio, vedere [Uso degli Hub di notifica per la trasmissione di notizie localizzate](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet/).
		</div>


È ora possibile procedere con la sezione [Esecuzione dell'app e
generazione di notifiche](#test-app).


<!-- Anchors -->

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[1]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
[2]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[3]: https://manage.windowsazure.com/


