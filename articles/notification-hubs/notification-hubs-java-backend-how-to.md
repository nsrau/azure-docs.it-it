<properties 
	pageTitle="Come usare Hub di notifica con Java" 
	description="Informazioni su come usare Hub di notifica di Azure da un back-end Java." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="java" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="yuaxu"/>

# Come usare Hub di notifica da Java
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js">Node.js</a>
</div>

Questo argomento descrive le funzionalità principali del nuovo ufficiale SDK per Java di Hub di notifica di Azure, completamente supportato. Si tratta di un progetto open source ed è possibile visualizzare il codice SDK completo nell'articolo relativo all'[SDK per Java].

In generale, è possibile accedere a tutte le funzionalità di Hub di notifica da un back-end Java/PHP/Python/Ruby tramite l'interfaccia REST di Hub di notifica, come descritto nell'argomento [API REST degli hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx) di MSDN. L'SDK per Java fornisce un semplice wrapper per le interfacce REST in Java.

L'SDK attualmente supporta:

- CRUD in Hub di notifica 
- CRUD in Registrazioni
- Gestione dell'installazione
- Importazione/esportazione di registrazioni
- Invii regolari
- Invii pianificati
- Operazioni asincrone tramite Java NIO
- Piattaforme supportate: APNS (iOS), GCM (Android), WNS (app di Windows Store), MPNS(Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android senza servizi Google) 

## Uso dell'SDK

### Compilazione e creazione

Usare [Maven]

Per creare:

	mvn package

## Codice

### CRUD di Hub di notifica

**Creare un oggetto NamespaceManager:**
	
	NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Creare un hub di notifica:**
	
	NotificationHubDescription hub = new NotificationHubDescription("hubname");
	hub.setWindowsCredential(new WindowsCredential("sid","key"));
	hub = namespaceManager.createNotificationHub(hub);
	
 OPPURE

	hub = new NotificationHub("connection string", "hubname");

**Ottenere un hub di notifica:**
	
	hub = namespaceManager.getNotificationHub("hubname");

**Aggiornare un hub di notifica:**
	
	hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
	hub = namespaceManager.updateNotificationHub(hub);

**Eliminare un hub di notifica:**
	
	namespaceManager.deleteNotificationHub("hubname");

### CRUD di registrazione
**Creare un client di Hub di notifica:**

	hub = new NotificationHub("connection string", "hubname");

**Creare una registrazione per Windows:**

	WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");    
	hub.createRegistration(reg);

**Creare una registrazione per iOS:**

	AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.createRegistration(reg);

Analogamente, è possibile creare registrazioni per Android (GCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Creare registrazioni modello:**

	WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
	reg.getHeaders().put("X-WNS-Type", "wns/toast");
	hub.createRegistration(reg);

**Creare registrazioni usando il modello create registrationid + upsert**

Rimuove i duplicati causati dalle risposte perse se gli ID di registrazione sono stati archiviati nel dispositivo:

	String id = hub.createRegistrationId();
	WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
	hub.upsertRegistration(reg);

**Aggiornare registrazioni:**
	
	hub.updateRegistration(reg);

**Eliminare registrazioni:**
	
	hub.deleteRegistration(regid);

**Effettuare query di registrazioni:**

* 	**Ottenere una singola registrazione:**
	
		hub.getRegistration(regid);
	
* 	**Ottenere tutte le registrazioni nell'hub:**
	
		hub.getRegistrations();
	
* 	**Ottenere registrazioni con tag:**
	
		hub.getRegistrationsByTag("myTag");
	
* 	**Ottenere registrazioni per canale:**
	
		hub.getRegistrationsByChannel("devicetoken");

Tutte le query di raccolta supportano i token $top e di continuazione.

### Uso dell'API di installazione
L'API di installazione rappresenta un meccanismo alternativo per la gestione delle registrazioni. Il mantenimento di più registrazioni non è semplice e può facilmente essere eseguito in maniera errata o inefficace, pertanto ora è possibile usare un SINGOLO oggetto di installazione. L'installazione contiene tutti gli elementi necessari: il canale push (token del dispositivo), tag, modelli, riquadri secondari (per WNS e APN). Non è più necessario chiamare il servizio per ottenere l'Id. È sufficiente generare un GUID o qualsiasi altro identificatore, mantenerlo nel dispositivo e inviarlo al back-end con il canale push (token del dispositivo). Nel backend, effettuare una singola chiamata: CreateOrUpdateInstallation, del tutto idempotente, per cui se necessario è possibile ritentare.

Un esempio per Amazon Kindle Fire è simile al seguente:

	Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
	hub.createOrUpdateInstallation(installation);

Per aggiornarlo:

	installation.addTag("foo");
	installation.addTemplate("template1", new InstallationTemplate("{"data":{"key1":"$(value1)"}}","tag-for-template1"));
	installation.addTemplate("template2", new InstallationTemplate("{"data":{"key2":"$(value2)"}}","tag-for-template2"));
	hub.createOrUpdateInstallation(installation);

Per scenari avanzati è possibile usare la funzionalità di aggiornamento parziale, che consente di modificare solo determinate proprietà dell'oggetto di installazione. L'aggiornamento parziale è essenzialmente un subset di operazioni Patch JSON che è possibile eseguire in un oggetto di installazione.

	PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
	PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
	PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{"data":{"key3":"$(value3)"}}","tag-for-template1")).toJson());
	hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Eliminare l'installazione:

	hub.deleteInstallation(installation.getInstallationId());

Le operazioni CreateOrUpdate, Patch e Delete saranno coerenti con l'operazione Get. L'operazione richiesta passa alla coda di sistema durante la chiamata e verrà eseguita in background. Si noti che l'operazione Get non è progettata per lo scenario di runtime principale, ma solo per il debug e la risoluzione dei problemi ed è strettamente limitata dal servizio.

Il flusso di invio per le installazioni è identico a quello delle registrazioni. È stata introdotta un'opzione per indirizzare la notifica all'installazione specifica: per farlo, usare il tag "InstallationId: {desired-id}". Nel caso riportato sopra l'aspetto dovrebbe essere simile al seguente:

	Notification n = Notification.createWindowsNotification("WNS body");
	hub.sendNotification(n, "InstallationId:{installation-id}");

Per uno dei diversi modelli:

	Map<String, String> prop =  new HashMap<String, String>();
	prop.put("value3", "some value");
	Notification n = Notification.createTemplateNotification(prop);
	hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### Pianificare le notifiche (disponibile per il livello STANDARD)

Si tratta di un invio normale ma con un parametro aggiuntivo, scheduledTime, che indica quando deve essere recapitata la notifica. Il servizio accetta qualsiasi data e ora da 5 minuti a sette giorni a partire dalla data e ora correnti.

**Pianificare una notifica nativa di Windows:**

	Calendar c = Calendar.getInstance();
	c.add(Calendar.DATE, 1);    
	Notification n = Notification.createWindowsNotification("WNS body");
	hub.scheduleNotification(n, c.getTime());

### Importazione/esportazione (disponibile per il livello STANDARD)
Talvolta è necessario eseguire un'operazione di massa nelle registrazioni. In genere si tratta dell'integrazione con un altro sistema o di una correzione di grandi dimensioni, ad esempio per l'aggiornamento di tag. Se si ha a che fare con migliaia di registrazioni, non è consigliabile usare il flusso Get/Update. La soluzione appropriata per tale scenario è la funzionalità di importazione/esportazione. Essenzialmente è necessario fornire un accesso a un contenitore BLOB dell'account di archiviazione come origine dei dati in ingresso e come percorso per l'output.

**Inviare il processo di esportazione:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ExportRegistrations);
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);


**Inviare il processo di importazione:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
	job.setImportFileUri("input file uri with SAS signature");
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);

**Attendere fino al completamento del processo:**

	while(true){
	    Thread.sleep(1000);
	    job = hub.getNotificationHubJob(job.getJobId());
	    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
	        break;
	}       

**Ottenere tutti i processi:**

	List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI con firma SAS**: l'URL di un file BLOB o di un contenitore BLOB con un insieme di parametri come le autorizzazioni e l'ora di scadenza e con la firma di tutte questi elementi effettuata usando la chiave della firma di accesso condiviso dell'account. L'SDK per Java di Archiviazione di Azure dispone di funzionalità avanzate, compresa la creazione di tale tipo di URI. In alternativa è possibile esaminare la classe di test ImportExportE2E (dal percorso Github) che include un'implementazione molto semplice e compatta dell'algoritmo di firma.

###Inviare notifiche
L'oggetto notifica è semplicemente un corpo con intestazioni ed esistono alcuni metodi di utilità che consentono la creazione di notifiche modello e notifiche native.

* **Windows Store e Windows Phone 8.1 (non Silverlight)**

		String toast = "<toast><visual><binding template="ToastText01"><text id="1">Hello from Java!</text></binding></visual></toast>";
		Notification n = Notification.createWindowsNotification(toast);
		hub.sendNotification(n);

* **iOS**

		String alert = "{"aps":{"alert":"Hello from Java!"}}";
		Notification n = Notification.createAppleNotification(alert);
		hub.sendNotification(n);

* **Android**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createGcmNotification(message);
		hub.sendNotification(n);

* **Windows Phone 8.0 e 8.1 Silverlight**

		String toast = "<?xml version="1.0" encoding="utf-8"?>" +
			        "<wp:Notification xmlns:wp="WPNotification">" +
			           "<wp:Toast>" +
			                "<wp:Text1>Hello from Java!</wp:Text1>" +
			           "</wp:Toast> " +
			        "</wp:Notification>";
		Notification n = Notification.createMpnsNotification(toast);
		hub.sendNotification(n);

* **Kindle Fire**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createAdmNotification(message);
		hub.sendNotification(n);

* **Inviare a tag**

		Set<String> tags = new HashSet<String>();
		tags.add("boo");
		tags.add("foo");
		hub.sendNotification(n, tags);

* **Inviare a espressione tag**

		hub.sendNotification(n, "foo && ! bar");

* **Inviare una notifica modello**

		Map<String, String> prop =  new HashMap<String, String>();
		prop.put("prop1", "v1");
		prop.put("prop2", "v2");
		Notification n = Notification.createTemplateNotification(prop);
		hub.sendNotification(n);

Quando si esegue il codice Java, dovrebbe essere visualizzata una notifica sul dispositivo di destinazione.

##<a name="next-steps"></a>Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client REST Java per Hub di notifica. A questo punto è possibile:

* Scaricare la versione completa di [SDK per Java] contenente l'intero codice SDK. 
* Consultare gli esempi:
	- [Introduzione ad Hub di notifica]
	- [Inviare le ultime notizie]
	- [Inviare le ultime notizie localizzate]
	- [Inviare notifiche agli utenti autenticati]
	- [Inviare notifiche multipiattaforma agli utenti autenticati]

[SDK per Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Introduzione ad Hub di notifica]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Inviare le ultime notizie]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Inviare le ultime notizie localizzate]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Inviare notifiche agli utenti autenticati]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Inviare notifiche multipiattaforma agli utenti autenticati]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/
 

<!---HONumber=July15_HO2-->