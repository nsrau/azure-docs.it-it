<properties urlDisplayName="How to use Notification Hubs with Java" pageTitle="Come usare Hub di notifica con Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="11/14/2014" ms.author="piyushjo" />

# Come usare Hub di notifica da Java/PHP
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/it-it/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/it-it/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Per accedere a tutte le funzionalità di Hub di notifica da un back-end Java/PHP/Ruby, è possibile usare l'interfaccia REST di Hub di notifica come descritto nell'argomento [API REST degli hub di notifica di MSDN](http://msdn.microsoft.com/it-it/library/dn223264.aspx).

In questo argomento viene illustrato come:

* Compilare un client REST per le funzionalità di Hub di notifica in Java.
* Completare l'[esercitazione introduttiva](http://azure.microsoft.com/it-it/documentation/articles/notification-hubs-ios-get-started/) per la piattaforma mobile preferita, implementando la parte del backend in Java.

##<a name="client-interface"></a>Interfaccia del client
L'interfaccia del client principale può fornire gli stessi metodi disponibili nell'[SDK di Hub di notifica per .NET](http://msdn.microsoft.com/it-it/library/jj933431.aspx). In questo modo sarà possibile tradurre direttamente tutte le esercitazioni e gli esempi disponibili in questo sito oppure messi a disposizione dalla community in Internet.

Tutto il codice disponibile è incluso nell'[esempio di wrapper REST Java].

Ad esempio, per creare un client:

	new NotificationHub("connection string", "hubname");	

Per creare una registrazione iOS (analoga per Windows, Android, Windows Phone e Kindle Fire):

	String id = hub.createRegistrationId();
	AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.upsertRegistration(reg);

Per inviare una notifica nativa iOS:
	
	Notification n = Notification.createAppleNotifiation("APNS body");
	hub.sendNotification(n);

##<a name="implementation"></a>Implementazione
Se non è già stato fatto, seguire l'[esercitazione introduttiva] fino all'ultima sezione in cui è necessario implementare il back-end.
Se si vuole, si può anche usare il codice dell'[esempio di wrapper REST Java] e passare direttamente alla sezione [Completare l'esercitazione](#complete-tutorial).

Tutti i dettagli per implementare un wrapper REST completo sono disponibili in [MSDN](http://msdn.microsoft.com/it-it/library/dn530746.aspx). In questa sezione viene illustrata l'implementazione Java dei passaggi principali necessari per accedere agli endpoint REST di Hub di notifica:

1. Analizzare la stringa di connessione
2. Generare il token di autorizzazione
3. Eseguire la chiamata HTTP

Nei frammenti di codice seguenti vengono usati i componenti seguenti:

* [Apache HttpComponents](http://hc.apache.org/httpcomponents-client-ga/)
* [Apache Commons-Codec](http://commons.apache.org/proper/commons-codec/)
* [Apache Commons-Io](http://commons.apache.org/proper/commons-io/)

### Analizzare la stringa di connessione

Questa è la classe principale che implementa il client, il cui costruttore analizza la stringa di connessione:

	public class NotificationHub {

		private static final String APIVERSION = "?api-version=2013-10";
		private static final String CONTENT_LOCATION_HEADER = "Location";
		private String endpoint;
		private String hubPath;
		private String SasKeyName;
		private String SasKeyValue;
	
		private HttpClient httpClient;
	
		public NotificationHub(String connectionString, String hubPath) {
			this.httpClient = HttpClients.createDefault();
			this.hubPath = hubPath;
	
			String[] parts = connectionString.split(";");
			if (parts.length != 3)
				throw new RuntimeException("Error parsing connection string: "
						+ connectionString);
	
			for (int i = 0; i < parts.length; i++) {
				if (parts[i].startsWith("Endpoint")) {
					this.endpoint = "https" + parts[i].substring(11);
				} else if (parts[i].startsWith("SharedAccessKeyName")) {
					this.SasKeyName = parts[i].substring(20);
				} else if (parts[i].startsWith("SharedAccessKey")) {
					this.SasKeyValue = parts[i].substring(16);
				}
			}
		}
	}


### Creare il token di sicurezza
I dettagli della creazione del token di sicurezza sono disponibili [in questa pagina](http://msdn.microsoft.com/it-it/library/dn495627.aspx).
È necessario aggiungere il metodo seguente alla classe **NotificationHub** per creare il token sulla base dell'URI della richiesta corrente e delle credenziali estratte dalla stringa di connessione.

	private String generateSasToken(URI uri) {
		String targetUri;
		try {
			targetUri = URLEncoder
					.encode(uri.toString().toLowerCase(), "UTF-8")
					.toLowerCase();

			long expiresOnDate = System.currentTimeMillis();
			int expiresInMins = 60; // 1 hour
			expiresOnDate += expiresInMins * 60 * 1000;
			long expires = expiresOnDate / 1000;
			String toSign = targetUri + "\n" + expires;

			// Get an hmac_sha1 key from the raw key bytes
			byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
			SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

			// Get an hmac_sha1 Mac instance and initialize with the signing key
			Mac mac = Mac.getInstance("HmacSHA256");
			mac.init(signingKey);

			// Compute the hmac on input data bytes
			byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

			// Convert raw bytes to Hex
			String signature = URLEncoder.encode(
					Base64.encodeBase64String(rawHmac), "UTF-8");

			// construct authorization string
			String token = "SharedAccessSignature sr=" + targetUri + "&sig="
					+ signature + "&se=" + expires + "&skn=" + SasKeyName;
			return token;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

### Inviare una notifica
Definire innanzitutto una classe che rappresenta la notifica.

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import org.apache.http.entity.ContentType;

	public class Notification {
		private Map<String, String> headers = new HashMap<String, String>();
		private String body;
		private ContentType contentType;
	
		public static Notification createWindowsNotification(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windows");
	
			if (body.contains("<toast>"))
				n.headers.put("X-WNS-Type", "wns/toast");
			if (body.contains("<tile>"))
				n.headers.put("X-WNS-Type", "wns/tile");
			if (body.contains("<badge>"))
				n.headers.put("X-WNS-Type", "wns/badge");
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createAppleNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "apple");
			return n;
		}
	
		public static Notification createGcmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "gcm");
			return n;
		}

		public static Notification createAdmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "adm");
			return n;
		}

		public static Notification createMpnsNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windowsphone");
	
			if (body.contains("<wp:Toast>")) {
				n.headers.put("X-WindowsPhone-Target", "toast");
				n.headers.put("X-NotificationClass", "2");
			}
			if (body.contains("<wp:Tile>")) {
				n.headers.put("X-WindowsPhone-Target", "tile");
				n.headers.put("X-NotificationClass", "1");
			}
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createTemplateNotification(
				Map<String, String> properties) {
			Notification n = new Notification();
			StringBuffer buf = new StringBuffer();
			buf.append("{");
			for (Iterator<String> iterator = properties.keySet().iterator(); iterator
					.hasNext();) {
				String key = iterator.next();
				buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
				if (iterator.hasNext())
					buf.append(",");
			}
			buf.append("}");
			n.body = buf.toString();
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "template");
			return n;
		}
	
		public Map<String, String> getHeaders() { return headers; }
	
		public void setHeaders(Map<String, String> headers) { this.headers = headers; }
	
		public String getBody() { return body; }
	
		public void setBody(String body) { this.body = body; }
	
		public ContentType getContentType() { return contentType; }
	
		public void setContentType(ContentType contentType) { this.contentType = contentType; }
	}

Questa classe è un contenitore per un corpo di notifica nativo oppure un set di proprietà nel caso di una notifica modello e un set di intestazioni che contengono il formato (modello o piattaforma nativa) e proprietà specifiche della piattaforma (come la proprietà di scadenza e le intestazioni WNS di Apple). Vengono definiti anche alcuni costruttori di riferimento per generare i tipi di notifiche usati comunemente.

Per tutte le opzioni disponibili fare riferimento alla [documentazione delle API REST di Hub di notifica](http://msdn.microsoft.com/it-it/library/dn495827.aspx) e ai formati delle piattaforme di notifica specifiche.

Una volta definita questa classe, è possibile scrivere i metodi di notifica all'interno della classe **NotificationHub**.

	public void sendNotification(Notification notification) {
		sendNotification(notification, "");
	}

	public void sendNotification(Notification notification, Set<String> tags) {
		if (tags.isEmpty())
			throw new IllegalArgumentException(
					"tags has to contain at least an element");

		StringBuffer exp = new StringBuffer();
		for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
			exp.append(iterator.next());
			if (iterator.hasNext())
				exp.append(" || ");
		}

		sendNotification(notification, exp.toString());
	}

	public void sendNotification(Notification notification, String tagExpression) {
		HttpPost post = null;
		try {
			URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
			post = new HttpPost(uri);
			post.setHeader("Authorization", generateSasToken(uri));

			if (tagExpression != null && !"".equals(tagExpression)) {
				post.setHeader("ServiceBusNotification-Tags", tagExpression);
			}

			for (String header : notification.getHeaders().keySet()) {
				post.setHeader(header, notification.getHeaders().get(header));
			}

			post.setEntity(new StringEntity(notification.getBody()));
			HttpResponse response = httpClient.execute(post);

			if (response.getStatusLine().getStatusCode() != 201) {
				String msg = "";
				if (response.getEntity() != null
						&& response.getEntity().getContent() != null) {
					msg = IOUtils.toString(response.getEntity().getContent());
				}
				throw new RuntimeException("Error: " + response.getStatusLine()
						+ " body: " + msg);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (post != null)
				post.releaseConnection();
		}
	}

I metodi sopra indicati inviano una richiesta POST HTTP all'endpoint /messages dell'hub di notifica, contenenti il corpo e le intestazioni corrette per l'invio della notifica.

##<a name="complete-tutorial"></a>Completare l'esercitazione
È ora possibile completare l'esercitazione introduttiva inviando la notifica da un back-end Java.

Inizializzare il client di Hub di notifica, sostituendo la stringa di connessione e il nome hub come indicato nell'[esercitazione introduttiva]:
	NotificationHub hub = new NotificationHub("{stringa di connessione}", "{nome hub}");

Aggiungere quindi il codice di invio a seconda della piattaforma mobile di destinazione.

### Windows Store e Windows Phone 8.1 (non Silverlight)

	String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
	Notification n = Notification.createWindowsNotification(toast);
	hub.sendNotification(n);

### iOS

	String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
	Notification n = Notification.createAppleNotification(alert);
	hub.sendNotification(n);

### Android
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createGcmNotification(message);
	hub.sendNotification(n);

### Windows Phone 8.0 e 8.1 Silverlight

	String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		        "<wp:Notification xmlns:wp=\"WPNotification\">" +
		           "<wp:Toast>" +
		                "<wp:Text1>Hello from Java!</wp:Text1>" +
		           "</wp:Toast> " +
		        "</wp:Notification>";
	Notification n = Notification.createMpnsNotification(toast);
	hub.sendNotification(n);

### Kindle Fire
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createAdmNotification(message);
	hub.sendNotification(n);

Eseguendo il codice Java dovrebbe essere visualizzata una notifica sul dispositivo di destinazione.


##<a name="next-steps"></a>Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client REST Java per Hub di notifica. A questo punto è possibile:

* Scaricare l'intero [esempio di wrapper REST Java], che contiene tutto il codice sopra indicato oltre alla gestione delle registrazioni.
* Visualizzare altre informazioni sulla funzionalità di aggiunta tag di Hub di notifica nell'[esercitazione sull'invio di ultime notizie]
* Visualizzare altre informazioni sull'invio di notifiche push a singoli utenti nell'[esercitazione sull'invio di notifiche]




[Esempio di wrapper REST Java]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[Esercitazione introduttiva]: http://azure.microsoft.com/it-it/documentation/articles/notification-hubs-ios-get-started/

<!--HONumber=35.1-->
