<properties 
	pageTitle="Come usare Hub di notifica con Python" 
	description="Informazioni su come usare Hub di notifica di Azure da un back-end Python." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="python" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="yuaxu"/>

# Come usare Hub di notifica da Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
		
Per accedere a tutte le funzionalità di Hub di notifica da un back-end Java/PHP/Ruby, è possibile usare l'interfaccia REST di Hub di notifica come descritto nell'argomento [API REST degli hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx) in MSDN.

> [AZURE.NOTE]Di seguito è riportato un esempio di riferimento per l'implementazione degli invii di notifiche in Python. Non si tratta dell'SDK Python di Hub di notifica.

> [AZURE.NOTE]L'esempio è scritto in Python 3.4.

In questo argomento viene illustrato come:

* Compilare un client REST per le funzionalità di Hub di notifica in Python.
* Inviare notifiche tramite l'interfaccia di Python alle API REST di Hub di notifica. 
* Eseguire un dump della richiesta/risposta HTTP REST a scopo didattico/di debug. 

Completare l'[esercitazione introduttiva](notification-hubs-windows-store-dotnet-get-started.md) per la piattaforma mobile preferita, implementando la parte del back-end in Python.

> [AZURE.NOTE]L'ambito dell'esempio è limitato all'invio di notifiche. Non viene eseguita alcuna gestione delle registrazioni.

## Interfaccia del client
L'interfaccia principale del client può fornire gli stessi metodi disponibili nell'[SDK di Hub di notifica per .NET](http://msdn.microsoft.com/library/jj933431.aspx). Questo consentirà di convertire direttamente tutte le esercitazioni e gli esempi disponibili in questo sito e a cui hanno contribuito i membri della community su Internet.

Tutto il codice disponibile è incluso nell'[esempio di wrapper REST Python].

Ad esempio, per creare un client:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
Per inviare una notifica di tipo avviso popup di Windows:
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Implementazione
Se non è già stato fatto, seguire l'[esercitazione Introduzione ad Hub di notifica] fino all'ultima sezione in cui è necessario implementare il back-end.

Tutti i dettagli per implementare un wrapper REST completo sono disponibili in [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). In questa sezione viene illustrata l'implementazione Python dei passaggi principali necessari per accedere agli endpoint REST di Hub di notifica e inviare notifiche:

1. Analizzare la stringa di connessione
2. Generare il token di autorizzazione
3. Inviare una notifica tramite l'API REST HTTP

### Analizzare la stringa di connessione

Questa è la classe principale che implementa il client, il cui costruttore analizza la stringa di connessione:

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Creare il token di sicurezza
I dettagli della creazione del token di sicurezza sono disponibili [qui](http://msdn.microsoft.com/library/dn495627.aspx). È necessario aggiungere i metodi seguenti alla classe **NotificationHub** per creare il token in base all'URI della richiesta corrente e delle credenziali estratte dalla stringa di connessione.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Inviare una notifica tramite l'API REST HTTP
Definire innanzitutto una classe che rappresenta la notifica.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Questa classe è un contenitore per un corpo di notifica nativo oppure un set di proprietà nel caso di una notifica modello e un set di intestazioni che contengono il formato (modello o piattaforma nativa) e proprietà specifiche della piattaforma (come la proprietà di scadenza e le intestazioni WNS di Apple).

Per tutte le opzioni disponibili fare riferimento alla [documentazione delle API REST di Hub di notifica](http://msdn.microsoft.com/library/dn495827.aspx) e ai formati delle piattaforme di notifica specifiche.

Una volta definita questa classe, è possibile scrivere i metodi di notifica all'interno della classe **NotificationHub**.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

I metodi sopra indicati inviano una richiesta POST HTTP all'endpoint /messages dell'hub di notifica, contenenti il corpo e le intestazioni corrette per l'invio della notifica.

### Uso di proprietà di debug per abilitare la registrazione dettagliata
L'abilitazione della proprietà di debug durante l'inizializzazione di Hub di notifica consente la scrittura di informazioni di registrazione dettagliate sulla richiesta HTTP e sul dump di risposta, nonché del risultato dettagliato dell'invio del messaggio di notifica. È stata recentemente aggiunta la [proprietà Notification Hubs TestSend](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) che restituisce informazioni dettagliate sul risultato dell'invio della notifica. Per usarle, inizializzarle con le seguenti operazioni:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

L'URL HTTP della richiesta di invio a Hub di notifica viene aggiunto con una querystring "test" come risultato.

##<a name="complete-tutorial"></a>Completare l'esercitazione
È ora possibile completare l'esercitazione introduttiva inviando la notifica da un back-end Python.

Inizializzare il client di Hub di notifica, sostituendo la stringa di connessione e il nome hub come indicato nell'esercitazione [Introduzione a Hub di notifica]\:

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Aggiungere quindi il codice di invio a seconda della piattaforma mobile di destinazione. In questo esempio vengono aggiunti anche metodi di livello più elevato per abilitare l'invio di notifiche in base alla piattaforma, ad esempio send\_windows\_notification per Windows o send\_apple\_notification per Apple e così via.

### Windows Store e Windows Phone 8.1 (non Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Windows Phone 8.0 e 8.1 Silverlight

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

Eseguendo il codice Python dovrebbe essere visualizzata una notifica sul dispositivo di destinazione.

## Esempi:

### Abilitazione della proprietà di debug
Quando si abilita il flag di debug durante l'inizializzazione di Hub di notifica, verranno visualizzati una richiesta HTTP dettagliata e un dump di risposta, nonché un risultato di notifica simile a quello riportato di seguito, dove è possibile comprendere quali intestazioni HTTP vengono passate e quale risposta HTTP è stata ricevuta da Hub di notifica: ![][1]

verrà visualizzato il risultato dettagliato di Hub di notifica, ad esempio:

- quando il messaggio viene inviato correttamente al servizio di notifica push. 
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Se non fosse possibile trovare destinazioni per qualsiasi notifica push, probabilmente nella risposta verrà visualizzato quanto segue, dove viene indicato che non è stata trovata alcuna registrazione per recapitare la notifica perché nelle registrazioni erano presenti alcuni tag non corrispondenti.

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Trasmettere notifiche di tipo avviso popup a Windows 

Notare le intestazioni che vengono inviate quando si inoltra una notifica di tipo popup al client Windows.

	hub.send_windows_notification(wns_payload)

![][2]

### Inviare la notifica specificando un tag (o un'espressione tag)

Si noti l'intestazione HTTP Tags che viene aggiunta alla richiesta HTTP (nell'esempio seguente viene inviata la notifica solo alle registrazioni con payload 'sports')

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Inviare la notifica specificando più tag

Si noti come l'intestazione HTTP Tags cambia quando vengono inviati più tag.
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Notifica basata su modelli

Si noti che l'intestazione HTTP Format cambia e che il corpo del payload viene inviato come parte del corpo della richiesta HTTP:

**Lato client - modello registrato**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Lato server - invio del payload**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client REST Python per Hub di notifica. A questo punto è possibile:

* Scaricare l'intero [esempio di wrapper REST Python], che contiene tutto il codice sopra indicato.
* Visualizzare altre informazioni sulla funzionalità di aggiunta tag di Hub di notifica nell'[esercitazione sull'invio delle ultime notizie]
* Per altre informazioni sulla funzionalità relativa ai modelli di Hub di notifica, vedere l'[esercitazione sull'invio di notizie localizzate]

<!-- URLs -->
[esempio di wrapper REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Introduzione a Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[esercitazione Introduzione ad Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[esercitazione sull'invio delle ultime notizie]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[esercitazione sull'invio di notizie localizzate]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 

<!---HONumber=Oct15_HO3-->