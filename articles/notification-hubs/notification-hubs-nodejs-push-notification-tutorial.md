---
title: Invio di notifiche push con Hub di notifica di Azure e Node.js
description: Informazioni su come usare Hub di notifica per inviare notifiche push da un&quot;applicazione Node.js.
keywords: notifica push,notifiche push,push node.js,push ios
services: notification-hubs
documentationcenter: nodejs
author: ysxu
manager: dwrede
editor: 
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/25/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40b1c0870bca02fd6d948dfc1f67ba5c76c698aa


---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Invio di notifiche push con Hub di notifica di Azure e Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Overview
> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Questa guida illustra come inviare notifiche push con Hub di notifica di Azure direttamente da un'applicazione Node.js. 

Gli scenari presentati includono l'invio di notifiche push ad applicazioni nelle piattaforme seguenti:

* Android
* iOS
* Windows Phone
* Piattaforma UWP (Universal Windows Platform) 

Per ulteriori informazioni sugli hub di notifica, vedere la sezione [Passaggi successivi](#next) .

## <a name="what-are-notification-hubs"></a>Informazioni su Hub di notifica
Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per informazioni dettagliate sull'infrastruttura del servizio, vedere la pagina [Hub di notifica di Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Creazione di un'applicazione Node.js
Il primo passaggio in questa esercitazione consiste nel creare una nuova applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creare e distribuire un sito Web Microsoft Azure][nodejswebsite], [Creare e distribuire un'applicazione Node.js a un servizio cloud di Azure][Servizio cloud Node.js], se si usa Windows PowerShell, oppure [Sito Web con WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurare l'applicazione per l'uso di Hub di notifica
Per usare Hub di notifica di Azure, è necessario scaricare e usare il [pacchetto Azure](https://www.npmjs.com/package/azure)di Node.js, che comprende un set integrato di librerie di supporto che comunicano con i servizi REST di notifica push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) e passare alla cartella in cui è stata creata l'applicazione vuota.
2. Digitare **npm install azure-sb** nella finestra di comando.
3. È possibile eseguire manualmente il comando **ls** o **dir** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella trovare il pacchetto **azure** , che contiene le librerie necessarie per accedere a Hub di notifica.

> [!NOTE]
> Altre informazioni sull'installazione di NPM sono disponibili nel [blog NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm)ufficiale. 
> 
> 

### <a name="import-the-module"></a>Importare il modulo
Utilizzando un editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurare una connessione all'Hub di notifica di Azure
L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il codice seguente consente di creare un oggetto **NotificationHubService** per l'hub di notifica denominato **hubname**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore **connectionstring** della connessione può essere recuperato dal [portale di Azure] seguendo questa procedura:

1. Nel riquadro di spostamento a sinistra fare clic su **Sfoglia**.
2. Selezionare **Hub di notifica**e quindi individuare l'hub da utilizzare per l'esempio. Per informazioni sulla creazione di un nuovo hub di notifica, vedere l' [esercitazione introduttiva per Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
3. Selezionare **Impostazioni**.
4. Fare clic su **Criteri di accesso**. Verranno visualizzate la stringhe di connessione di accesso completo e condiviso.

![Portale di Azure - Hub di notifica](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> La stringa di connessione può essere recuperata anche usando il cmdlet **Get-AzureSbNamespace** specificato da [Azure PowerShell](../powershell-install-configure.md) o il comando **azure sb namespace show** con l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).
> 
> 

## <a name="general-architecture"></a>Architettura generale
L'oggetto **NotificationHubService** espone le istanze seguenti dell'oggetto per l'invio di notifiche push a specifici dispositivi e specifiche applicazioni:

* **Android**: usare l'oggetto **GcmService**, disponibile in **notificationHubService.gcm**
* **iOS**: usare l'oggetto **ApnsService**, accessibile in **notificationHubService.apns**
* **Windows Phone**: usare l'oggetto **MpnsService**, disponibile in **notificationHubService.mpns**
* **Piattaforma UWP (Universal Windows Platform)**: usare l'oggetto **WnsService** disponibile in **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Procedura: Inviare notifiche push ad applicazioni Android
L’oggetto **GcmService** specifica un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni Android. Di seguito sono illustrati i parametri accettati dal metodo **send** .

* **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload** : il payload JSON o la stringa non elaborata del messaggio.
* **Callback** : la funzione di richiamata.

Per altre informazioni sul formato di payload, vedere la sezione **Payload** del documento relativo all' [implementazione del server GCM](http://developer.android.com/google/gcm/server.html#payload) .

Nel codice seguente viene usata l'istanza **GcmService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push a tutti i client registrati.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Procedura: Inviare notifiche push ad applicazioni iOS
Come per le applicazioni Android descritte sopra, l'oggetto **ApnsService** specifica un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni iOS. Di seguito sono illustrati i parametri accettati dal metodo **send** .

* **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload** : il payload JSON o stringa del messaggio.
* **Callback** : la funzione di richiamata.

Per altre informazioni sul formato di payload, vedere la sezione relativa al **payload di notifica** nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Nel codice seguente viene usata l'istanza **ApnsService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Procedura: Inviare notifiche push ad applicazioni Windows Phone
L'oggetto **MpnsService** specifica un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni Windows Phone. Di seguito sono illustrati i parametri accettati dal metodo **send** .

* **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client.
* **Payload** : il payload XML del messaggio.
* **TargetName** - `toast` per le notifiche di tipo avviso popup. `token` per le notifiche di tipo riquadro.
* **NotificationClass** : la priorità della notifica. Per i valori validi, vedere la sezione relativa agli **elementi dell'intestazione HTTP** nel documento sul [push di notifiche da un server](http://msdn.microsoft.com/library/hh221551.aspx) .
* **Options** : intestazioni delle richieste facoltative.
* **Callback** : la funzione di richiamata.

Per un elenco dei valori validi per **TargetName**, **NotificationClass** e le opzioni di intestazione, vedere la pagina [Pushing Notifications from a Server](http://msdn.microsoft.com/library/hh221551.aspx) (Notifiche push da server).

Nel codice di esempio seguente viene usata l'istanza **MpnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push di tipo avviso popup:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Procedura: Inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform)
L'oggetto **WnsService** specifica un metodo **send** che è possibile usare per inviare notifiche push alle applicazioni della piattaforma UWP (Universal Windows Platform).  Di seguito sono illustrati i parametri accettati dal metodo **send** .

* **Tags** : l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client registrati.
* **Payload** : il payload XML del messaggio.
* **Type** : il tipo di notifica.
* **Options** : intestazioni delle richieste facoltative.
* **Callback** : la funzione di richiamata.

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Nel codice seguente viene usata l'istanza **WnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica push di tipo avviso popup a un'app UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Passaggi successivi
I frammenti di codice di esempio riportati sopra consentono di creare facilmente l'infrastruttura del servizio per recapitare notifiche push a un'ampia gamma di dispositivi. A questo punto, dopo aver appreso le nozioni di base dell'uso di Hub di notifica con Node.js, usare i collegamenti seguenti per scoprire come estendere ulteriormente queste funzionalità.

* Vedere la documentazione MSDN su [Hub di notifica di Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Visitare il repository [Azure SDK for Node] su GitHub per altri esempi e dettagli relativi all'implementazione.

[Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?]: #what-are-service-bus-topics
[Creare uno spazio dei nomi del servizio]: #create-a-service-namespace
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
[Concetti relativi agli argomenti]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[Portale di Azure classico]: http://manage.windowsazure.com
[immagine]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Hub di notifica del bus di servizio di Azure]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Sito Web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Servizio cloud Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Portale di gestione precedente]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Servizio cloud Node.js con Archiviazione]: /develop/nodejs/tutorials/web-app-with-storage/
[Creazione di un'applicazione Web Node.js con Archiviazione]: /develop/nodejs/tutorials/web-site-with-storage/
[portale di Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


