<properties 
	pageTitle="Come usare Hub di notifica con Node.js" 
	description="Informazioni su come usare Hub di notifica per inviare notifiche push da un'applicazione Node.js." 
	services="notification-hubs" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="mwasson"/>

# Come usare Hub di notifica da Node.js
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js" class="current">Node.js</a>
</div>

##Informazioni generali

In questa guida verrà descritto come usare Hub di notifica da applicazioni Node.js. Gli scenari presentati includono **invio di notifiche ad applicazioni per Android, iOS, Windows Phone e Windows Store**. Per altre informazioni sugli hub di notifica, vedere la sezione [Passaggi successivi](#next) .

##Informazioni su Hub di notifica

Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per altre informazioni, vedere [Hub di notifica di Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Creare un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un sito Web Node.js in Azure][nodejswebsite], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Web Site with WebMatrix].

##Configurare l'applicazione per l'uso dell'Hub di notifica

Per usare Hub di notifica di Azure, è necessario scaricare e utilizzare il pacchetto Azure Node.js, che contiene un set di librerie efficaci che
comunicano con i servizi REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella  finestra di comando, che dovrebbe restituire il seguente output:

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  È possibile eseguire manualmente il comando **ls** o **dir** per verificare che sia stata creata una cartella **node_modules**. All'internon di tale cartella trovare il pacchetto **azure**, che contiene le librerie necessarie per accedere a Hub di notifica.

### Importare il modulo

Usando un editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurare una connessione all'Hub di notifica di Azure

L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il seguente codice consente di creare un oggetto **NotificationHubService** per l'hub di notifica denominato **hubname**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore **connectionstring** della connessione può essere recuperato dal portale di gestione di Azure, eseguendo la seguente procedura:

1. Nel portale di gestione di Azure selezionare **Bus di servizio**, quindi selezionare lo spazio dei nomi che contiene l'hub di notifica.

2. Selezionare **HUB DI NOTIFICA** e quindi selezionare l'hub che si desidera usare.

3. Selezionare **Visualizza stringa di connessione** nella sezione **riepilogo rapido** e copiare il valore della stringa di connessione.

> [AZURE.NOTE] È inoltre possibile recuperare la stringa di connessione usando il cmdlet **Get-AzureSbNamespace** di Azure PowerShell o il comando **azure sb namespace show** degli strumenti da riga di comando di Azure.

</div>

##Come inviare notifiche

Di seguito sono illustrate le istanze di oggetto esposte dall'oggetto **NotificationHubService** per l'invio di notifiche a dispositivi e applicazioni specifiche:

* **Android**: usare l'oggetto **GcmService**, disponibile in **notificationHubService.gcm**
* **iOS**: usare l'oggetto **ApnsService**, disponibile in **notificationHubService.apns**
* **Windows Phone**: usare l'oggetto **MpnsService**, disponibile in **notificationHubService.mpns**
* **Applicazioni di Windows Store**: usare l'oggetto **WnsService**, disponibile in **notificationHubService.wns**

### Come inviare notifiche alle applicazioni per Android

L'oggetto **GcmService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per Android. Il metodo **send** accetta i parametri seguenti:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload JSON o stringa del messaggio
* Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione Payload dell'articolo relativo all'[implementazione del server GCM](http://developer.android.com/google/gcm/server.html#payload).

Nel seguente codice viene usata l'istanza **GcmService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio a tutti i client.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Come inviare notifiche alle applicazioni per iOS

L'oggetto **ApnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per iOS. Il metodo **send** accetta i parametri seguenti:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload JSON o stringa del messaggio
* Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione relativa al payload di notifica nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

Nel seguente codice viene usata l'istanza **ApnsService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Come inviare notifiche alle applicazioni per Windows Phone

L'oggetto **MpnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per Windows Phone. Il metodo **send** accetta i parametri seguenti:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload XML del messaggio
* TargetName: "toast" per le notifiche di tipo avviso popup. "token" per le notifiche di tipo riquadro.
* NotificationClass: la priorità della notifica. Vedere la sezione relativa agli elementi dell'intestazione HTTP di [Push di notifiche da un server](http://msdn.microsoft.com/library/hh221551.aspx) per valori validi.
* Options: intestazioni delle richieste facoltative
* Callback: la funzione di richiamata

Per un elenco dei valori validi per TargetName, NotificationClass e le opzioni di intestazione, vedere [Push di notifiche da un server](http://msdn.microsoft.com/library/hh221551.aspx).

Nel seguente codice viene usata l'istanza **MpnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Come inviare notifiche alle applicazioni di Windows Store

L'oggetto **WnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni di Windows Store.  Il metodo **send** accetta i parametri seguenti:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload XML del messaggio
* Type: il tipo di notifica
* Options: intestazioni delle richieste facoltative
* Callback: la funzione di richiamata

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica Push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Nel seguente codice viene usata l'istanza **WnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'uso di Hub di notifica, usare i seguenti collegamenti per altre informazioni.

-   Vedere le informazioni di riferimento in MSDN: [Hub di notifica di Azure][].
-   Visitare il repository per [Azure SDK per Node] su GitHub.

  [Azure SDK per Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Passaggi successivi]: #nextsteps
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-are-service-bus-topics
  [Creare uno spazio dei nomi del servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Creare un'applicazione Node.js]: #Create_a_Nodejs_Application
  [Configurare l'applicazione per l'uso del bus di servizio]: #Configure_Your_Application_to_Use_Service_Bus
  [Procedura: Creare un argomento]: #How_to_Create_a_Topic
  [Procedura: Creare sottoscrizioni]: #How_to_Create_Subscriptions
  [Procedura: Inviare messaggi a un argomento]: #How_to_Send_Messages_to_a_Topic
  [Procedura: Ricevere messaggi da una sottoscrizione]: #How_to_Receive_Messages_from_a_Subscription
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Procedura: Eliminare argomenti e sottoscrizioni]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Concetti relativi agli argomenti]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [immagine]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Hub di notifica del bus di servizio di Azure]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Portale di gestione precedente]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js con archiviazione]: /develop/nodejs/tutorials/web-app-with-storage/
  [Applicazione Web Node.js con archiviazione]: /develop/nodejs/tutorials/web-site-with-storage/


<!--HONumber=49-->