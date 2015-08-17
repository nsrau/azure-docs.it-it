

Nell'app back-end è ora necessario passare all'invio di notifiche modello anziché di payload nativi. In questo modo il codice back-end verrà semplificato, in quanto non è necessario inviare più payload per diverse piattaforme.

Quando si inviano notifiche modello, è necessario solo fornire un set di proprietà. In questo caso verrà inviato il set di proprietà contenente la versione localizzata delle notizie correnti, ad esempio:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


In questa sezione viene illustrato come inviare notifiche in due modi diversi:

- Da un'app console
- Da uno script di Servizi mobili

Il codice incluso trasmette le notifiche sia Windows Store che a dispositivi iOS, poiché il back-end è in grado di trasmettere a qualsiasi dispositivo supportato.



## Per inviare notifiche tramite un'app console C# ##

Il metodo *SendNotificationAsync* verrà modificato con l'invio di una singola notifica modello.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Si noti che questa semplice chiamata distribuirà la notizia localizzata corretta a **tutti** i dispositivi, indipendentemente dalla piattaforma, in quanto l'Hub di notifica crea il payload nativo corretto e lo distribuisce a tutti i dispositivi che hanno sottoscritto un tag specifico.

### Servizi mobili

Nell'utilità di pianificazione di Servizi mobili, sovrascrivere lo script con il codice seguente:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
Si noti come in questo caso non sia necessario inviare più notifiche per impostazioni locali e piattaforme diverse.

<!---HONumber=August15_HO6-->