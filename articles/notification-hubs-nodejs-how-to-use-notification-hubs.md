<properties urlDisplayName="Notification Hubs" pageTitle="Hub di notifica - Dev Center Node.js" metaKeywords="" description="Informazioni su come usare Hub di notifica per inviare notifiche push. Gli esempi di codice sono scritti per applicazioni Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Come usare Hub di notifica

In questa guida verrà descritto come usare Hub di notifica da applicazioni Node.js. Gli scenari presentati includono **invio di notifiche ad applicazioni per Android, iOS, Windows Phone e Windows Store**. Per altre informazioni sugli hub di notifica, vedere la sezione [Passaggi successivi](#next).

## Sommario

-   [Informazioni su Hub di notifica](#hub)
-   [Creazione di un'applicazione Node.js](#create)
-   [Configurare l'applicazione per l'uso dell'Hub di notifica](#config)
-   [Procedura: Inviare notifiche](#send)
-   [Passaggi successivi](#next)

##<a id="hub"></a> Informazioni su Hub di notifica

Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per altre informazioni, vedere [Hub di notifica di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj927170.aspx).

##<a id="create"></a> Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][nodejswebsite], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sito Web con WebMatrix].

##<a id="config"></a> Configurare l'applicazione per l'uso dell'Hub di notifica

Per usare Hub di notifica di Azure, è necessario scaricare e usare il 
pacchetto Azure Node.js, che include un set di librerie di riferimento che
ccomunicano con i servizi REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (UNIX) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe 
restituire il seguente output:

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

3.  È possibile eseguire manualmente il comando **ls** o **dir** per verificare che sia stata creata una cartella
    **node\_modules**. All'interno di tale cartella individuare il pacchetto 
**azure**, che contiene le librerie necessarie per accedere all'
hub di notifica.

### Importare il modulo

Usando un editor di testo, aggiungere quanto segue nella parte superiore del
file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurare una connessione all'Hub di notifica di Azure

L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il
seguente crea un oggetto **NotificationHubService** per l'hub di notifica denominato **hubname**. Aggiungerlo nella parte
superiore del file **server.js** dopo l'istruzione per l'importazione del 
module azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore **connectionstring** della connessione può essere recuperato dal portale di gestione di Azure, eseguendo la procedura seguente:

1. Nel portale di gestione di Azure selezionare **Bus di servizio** e quindi selezionare lo spazio dei nomi che contiene l'hub di notifica.

2. Selezionare **HUB DI NOTIFICA** e quindi selezionare l'hub che si desidera usare.

3. Selezionare **Visualizza stringa di connessione** nella sezione **riepilogo rapido** e copiare il valore della stringa di connessione.

<div class="dev-callout">
<strong>Nota</strong>
<p>È inoltre possibile recuperare la stringa di connessione usando il cmdlet <b>Get-AzureSbNamespace</b> di Azure PowerShell o il comando <b>azure sb namespace show</b> degli strumenti da riga di comando di Azure.</p>

</div>

##<a id="send"></a> Come inviare notifiche

Di seguito sono illustrate le istanze di oggetto esposte dall'oggetto **NotificationHubService** per l'invio di notifiche a dispositivi e applicazioni specifiche.

* **Android**: usare l'oggetto **GcmService**, disponibile in **notificationHubService.gcm**
* **iOS**: usare l'oggetto **GcmService**, accessibile in **notificationHubService.apns**
* **Windows Phone**: usare l'oggetto **MpnsService**, disponibile in **notificationHubService.mpns**
* **Applicazioni di Windows Store**: usare l'oggetto **WnsService**, disponibile in **notificationHubService.wns**

### Come inviare notifiche alle applicazioni per Android

L'oggetto **GcmService** fornisce un metodo **send** method che è possibile usare per inviare notifiche alle applicazioni per Android. Di seguito sono illustrati i parametri accettati dal metodo **send**:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload JSON o stringa del messaggio
* Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione Payload di [Implementazione del server GCM](http://developer.android.com/google/gcm/server.html#payload).

Nel codice seguente viene usata l'istanza **GcmService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio a tutti i client.

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

L'oggetto **ApnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per iOS. Di seguito sono illustrati i parametri accettati dal metodo **send**:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload JSON o stringa del messaggio
* Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione relativa al payload di notifica nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

Nel codice seguente viene usata l'istanza **ApnsService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Come inviare notifiche alle applicazioni per Windows Phone

L'oggetto **MpnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per Windows Phone. Di seguito sono illustrati i parametri accettati dal metodo **send**:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload XML del messaggio
* TargetName: 'toast' per le notifiche di tipo avviso popup, 'token' per le notifica di tipo riquadro.
* NotificationClass: la priorità della notifica. Per i valori validi, vedere la sezione relativa agli elementi dell'intestazione HTTP di [Push di notifiche da un server](http://msdn.microsoft.com/it-it/library/hh221551.aspx).
* Options: intestazioni delle richieste facoltative
* Callback: la funzione di richiamata

Per un elenco dei valori validi per TargetName, NotificationClass e le opzioni di intestazione, vedere [Push di notifiche da un server](http://msdn.microsoft.com/it-it/library/hh221551.aspx).

Nel codice seguente viene usata l'istanza **MpnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Come inviare notifiche alle applicazioni di Windows Store

L'oggetto **WnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni di Windows Store.  Di seguito sono illustrati i parametri accettati dal metodo **send**:

* Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
* Payload: il payload XML del messaggio
* Type: il tipo di notifica
* Options: intestazioni delle richieste facoltative
* Callback: la funzione di richiamata

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica Push](http://msdn.microsoft.com/it-it/library/windows/apps/hh465435.aspx).

Nel codice seguente viene usata l'istanza **WnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

##<a id="next"></a> Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'uso dell'Hub di notifica, usare i
collegamenti seguenti per altre informazioni.

-   Vedere il riferimento in MSDN: [Hub di notifica di Azure][].
-   Repository [Azure SDK per Node] su GitHub.

  [Azure SDK per Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Passaggi successivi]: #nextsteps
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-are-service-bus-topics
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Creazione di un'applicazione Node.js]: #Create_a_Nodejs_Application
  [Configurare l'applicazione per l'uso del bus di servizio]: #Configure_Your_Application_to_Use_Service_Bus
  [Procedura: Creare un argomento]: #How_to_Create_a_Topic
  [Procedura: Creare sottoscrizioni]: #How_to_Create_Subscriptions
  [Procedura: Inviare messaggi a un argomento]: #How_to_Send_Messages_to_a_Topic
  [Procedura: Ricevere messaggi da una sottoscrizione]: #How_to_Receive_Messages_from_a_Subscription
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Procedura: Eliminare argomenti e sottoscrizioni]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Hub di notifica del bus di servizio di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sito Web con WebMatrix]: /it-it/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-app-with-storage/
  [Applicazione Web Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-site-with-storage/

