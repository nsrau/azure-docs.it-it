<properties
	pageTitle="Invio di notifiche push con Hub di notifica di Azure e Node.js"
	description="Informazioni su come usare Hub di notifica per inviare notifiche push da un'applicazione Node.js."
    keywords="notifica push,notifiche push,push node.js,push ios"
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/08/2015"
	ms.author="wesmc"/>

# Invio di notifiche push con Hub di notifica di Azure e Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##Panoramica

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Questa guida illustra come inviare notifiche push con Hub di notifica di Azure direttamente da un'applicazione Node.js.

Gli scenari presentati includono l'invio di notifiche push ad applicazioni nelle piattaforme seguenti:

* Android
* iOS
* Windows Phone
* Piattaforma UWP (Universal Windows Platform) 

Per ulteriori informazioni sugli hub di notifica, vedere la sezione [Passaggi successivi](#next).

##Informazioni su Hub di notifica

Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per informazioni dettagliate sull'infrastruttura del servizio, vedere la pagina [Hub di notifica di Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Creare un'applicazione Node.js

Il primo passaggio in questa esercitazione consiste nel creare una nuova applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creare e distribuire un'applicazione Node.js in un sito Web di Azure][nodejswebsite], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sito Web con WebMatrix].

##Configurare l'applicazione per l'uso di Hub di notifica

Per usare Hub di notifica di Azure, è necessario scaricare e usare il [pacchetto Azure](https://www.npmjs.com/package/azure) di Node.js, che comprende un set integrato di librerie di supporto che comunicano con i servizi REST di notifica push.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) e passare alla cartella in cui è stata creata l'applicazione vuota.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire un output simile al seguente (i numeri di versione possono variare):

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

3.  È possibile eseguire manualmente il comando **ls** o **dir** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella trovare il pacchetto **azure**, che contiene le librerie necessarie per accedere a Hub di notifica.

>[AZURE.NOTE] Altre informazioni sull'installazione di NPM sono disponibili nel [blog NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm) ufficiale.

### Importare il modulo

Utilizzando un editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurare una connessione all'Hub di notifica di Azure

L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il codice seguente consente di creare un oggetto **NotificationHubService** per l'hub di notifica denominato **hubname**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore **connectionstring** della connessione può essere recuperato dal [portale di Azure] seguendo questa procedura:

1. Nel riquadro di spostamento a sinistra fare clic su **Sfoglia**.

2. Selezionare **Hub di notifica** e quindi individuare l'hub da usare per l'esempio. Per informazioni sulla creazione di un nuovo hub di notifica, vedere l'[esercitazione introduttiva per Windows Store](notification-hubs-windows-store-dotnet-get-started.md).

3. Selezionare **Impostazioni**.

4. Fare clic su **Criteri di accesso**. Verranno visualizzate la stringhe di connessione di accesso completo e condiviso.

![Portale di Azure - Hub di notifica](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] La stringa di connessione può essere recuperata anche usando il cmdlet **Get-AzureSbNamespace** fornito da [Azure PowerShell](../powershell-install-configure.md) o il comando **azure sb namespace show** con l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

##Architettura generale

L'oggetto **NotificationHubService** espone le istanze seguenti dell'oggetto per l'invio di notifiche push a specifici dispositivi e specifiche applicazioni:

* **Android**: utilizzare l'oggetto **GcmService**, disponibile in **notificationHubService.gcm**
* **iOS**: utilizzare l'oggetto **GcmService**, accessibile in **notificationHubService.apns**
* **Windows Phone**: utilizzare l'oggetto **MpnsService**, disponibile in **notificationHubService.mpns**
* **Piattaforma UWP (Universal Windows Platform)**: usare l'oggetto **WnsService** disponibile in **notificationHubService.wns**

### Procedura: Inviare notifiche push ad applicazioni Android

L'oggetto **GcmService** fornisce un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni Android. Di seguito sono illustrati i parametri accettati dal metodo **send**.

* **Tags**: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload**: il payload JSON o stringa non elaborata del messaggio.
* **Callback**: la funzione di richiamata.

Per altre informazioni sul formato di payload, vedere la sezione **Payload** del documento relativo all'[implementazione del server GCM](http://developer.android.com/google/gcm/server.html#payload).

Nel codice seguente viene usata l'istanza **GcmService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push a tutti i client registrati.

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

### Procedura: Inviare notifiche push ad applicazioni iOS

Come per le applicazioni Android descritte sopra, l'oggetto **ApnsService** fornisce un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni iOS. Di seguito sono illustrati i parametri accettati dal metodo **send**.

* **Tags**: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload**: il payload JSON o stringa del messaggio.
* **Callback**: la funzione di richiamata.

Per altre informazioni sul formato di payload, vedere la sezione relativa al **payload di notifica** nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

Nel codice seguente viene utilizzata l'istanza **ApnsService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Procedura: Inviare notifiche push ad applicazioni Windows Phone

L'oggetto **MpnsService** fornisce un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni Windows Phone. Di seguito sono illustrati i parametri accettati dal metodo **send**.

* **Tags**: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload**: il payload XML del messaggio.
* **TargetName**: `toast` per le notifiche di tipo avviso popup, `token` per le notifiche di tipo riquadro.
* **NotificationClass**: la priorità della notifica. Per i valori validi, vedere la sezione relativa agli **elementi dell'intestazione HTTP** nel documento sul [push di notifiche da un server](http://msdn.microsoft.com/library/hh221551.aspx).
* **Options**: intestazioni delle richieste facoltative.
* **Callback**: la funzione di richiamata.

Per un elenco dei valori validi per **TargetName**, **NotificationClass** e le opzioni di intestazione, vedere la pagina relativa al [push di notifiche da un server](http://msdn.microsoft.com/library/hh221551.aspx).

Nel codice di esempio seguente viene usata l'istanza **MpnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push di tipo avviso popup:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Procedura: Inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform)

L'oggetto **WnsService** fornisce un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform). Di seguito sono illustrati i parametri accettati dal metodo **send**.

* **Tags**: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client registrati.
* **Payload**: il payload XML del messaggio.
* **Type**: il tipo di notifica.
* **Options**: intestazioni delle richieste facoltative.
* **Callback**: la funzione di richiamata.

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Nel codice seguente viene usata l'istanza **WnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push di tipo avviso popup a un'app UWP:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Passaggi successivi

I frammenti di codice di esempio riportati sopra consentono di creare facilmente l'infrastruttura del servizio per recapitare notifiche push a un'ampia gamma di dispositivi. A questo punto, dopo aver appreso le nozioni di base dell'uso di Hub di notifica con Node.js, usare i collegamenti seguenti per scoprire come estendere ulteriormente queste funzionalità.

-   Vedere la documentazione MSDN su [Hub di notifica di Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visitare il repository [Azure SDK for Node] su GitHub per altri esempi e dettagli relativi all'implementazione.

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sito Web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [portale di Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0309_2016-->