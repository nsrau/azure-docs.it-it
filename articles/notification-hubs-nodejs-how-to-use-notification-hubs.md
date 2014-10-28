<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Come usare Hub di notifica

In questa guida verrà descritto come usare Hub di notifica da applicazioni Node.js. Gli scenari presentati includono **invio di notifiche ad applicazioni per Android, iOS, Windows Phone e Windows Store**. Per altre informazioni sugli hub di notifica, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
-   [Creazione di un'applicazione Node.js][Creazione di un'applicazione Node.js]
-   [Configurare l'applicazione per l'uso dell'Hub di notifica][Configurare l'applicazione per l'uso dell'Hub di notifica]
-   [Procedura: Inviare notifiche][Procedura: Inviare notifiche]
-   [Passaggi successivi][Passaggi successivi]

## <span id="hub"></span></a> Informazioni su Hub di notifica

Hub di notifica di Azure offre un'infrastruttura scalabile, multipiattaforma e di semplice utilizzo per l'invio di notifiche push ai dispositivi mobili. Per altre informazioni, vedere [Hub di notifica di Azure][Hub di notifica di Azure].

## <span id="create"></span></a>Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Servizio cloud Node.js] (usando Windows PowerShell) o [Sito Web con WebMatrix][Sito Web con WebMatrix].

## <span id="config"></span></a> Configurare l'applicazione per l'uso dell'Hub di notifica

Per usare Hub di notifica di Azure, è necessario scaricare e usare il pacchetto Azure Node.js che include un set di librerie di riferimento che comunicano con i servizi REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire il seguente output:

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

3.  È possibile eseguire manualmente il comando **ls** o **dir** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella individuare il pacchetto **azure**, che contiene le librerie necessarie per accedere all'Hub di notifica.

### Importare il modulo

Usando un editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurare una connessione all'Hub di notifica di Azure

L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il seguente crea un oggetto **NotificationHubService** per l'hub di notifica denominato **hubname**. Aggiungerlo nella parte superiore del file **server.js** dopo l'istruzione per l'importazione del modulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore **connectionstring** della connessione può essere recuperato dal portale di gestione di Azure, eseguendo la procedura seguente:

1.  Nel portale di gestione di Azure selezionare **Service Bus** e quindi selezionare lo spazio dei nomi che contiene l'hub di notifica.

2.  Selezionare **NOTIFICATION HUBS** e quindi selezionare l'hub che si desidera usare.

3.  Selezionare **View Connection String** nella sezione **quick glance** e copiare il valore della stringa di connessione.

<div class="dev-callout">
<strong>Nota</strong>
<p>&Egrave; inoltre possibile recuperare la stringa di connessione usando il cmdlet <b>Get-AzureSbNamespace</b> di Azure PowerShell o il comando <b>azure sb namespace show</b> degli strumenti da riga di comando di Azure.</p>

</div>

## <span id="send"></span></a> Come inviare notifiche

Di seguito sono illustrate le istanze di oggetto esposte dall'oggetto **NotificationHubService** per l'invio di notifiche a dispositivi e applicazioni specifiche.

-   **Android**: usare l'oggetto **GcmService**, disponibile in **notificationHubService.gcm**
-   **iOS**: usare l'oggetto **GcmService**, accessibile in **notificationHubService.apns**
-   **Windows Phone**: usare l'oggetto **MpnsService**, disponibile in **notificationHubService.mpns**
-   **Applicazioni di Windows Store**: usare l'oggetto **WnsService**, disponibile in **notificationHubService.wns**

### Come inviare notifiche alle applicazioni per Android

L'oggetto **GcmService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per Android. Di seguito sono illustrati i parametri accettati dal metodo **send**.

-   Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
-   Payload: il payload JSON o stringa del messaggio
-   Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione Payload di [Implementazione del server GCM][Implementazione del server GCM].

Nel codice seguente viene utilizzata l'istanza **GcmService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio a tutti i client.

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

L'oggetto **ApnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per iOS. Di seguito sono illustrati i parametri accettati dal metodo **send**.

-   Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
-   Payload: il payload JSON o stringa del messaggio
-   Callback: la funzione di richiamata

Per altre informazioni sul formato di payload, vedere la sezione relativa al payload di notifica nella [guida alla programmazione di notifiche push e locali][guida alla programmazione di notifiche push e locali].

Nel codice seguente viene utilizzata l'istanza **ApnsService** esposta dall'oggetto **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### Come inviare notifiche alle applicazioni per Windows Phone

L'oggetto **MpnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni per Windows Phone. Di seguito sono illustrati i parametri accettati dal metodo **send**.

-   Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
-   Payload: il payload XML del messaggio
-   TargetName: 'toast' per le notifiche di tipo avviso popup, 'token' per le notifica di tipo riquadro
-   NotificationClass: la priorità della notifica. Per i valori validi, vedere la sezione relativa agli elementi dell'intestazione HTTP di [Push di notifiche da un server][Push di notifiche da un server].
-   Options: intestazioni delle richieste facoltative
-   Callback: la funzione di richiamata

Per un elenco dei valori validi per TargetName, NotificationClass e le opzioni di intestazione, vedere [Push di notifiche da un server][Push di notifiche da un server].

Nel codice seguente viene utilizzata l'istanza **MpnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### Come inviare notifiche alle applicazioni di Windows Store

L'oggetto **WnsService** fornisce un metodo **send** che è possibile usare per l'invio di notifiche alle applicazioni di Windows Store. Di seguito sono illustrati i parametri accettati dal metodo **send**.

-   Tags: l'identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata a tutti i client
-   Payload: il payload XML del messaggio
-   Type: il tipo di notifica
-   Options: intestazioni delle richieste facoltative
-   Callback: la funzione di richiamata

Per un elenco dei valori validi per i tipi e le intestazioni delle richieste, vedere [Intestazioni delle richieste e delle risposte per il servizio di notifica Push][Intestazioni delle richieste e delle risposte per il servizio di notifica Push].

Nel codice seguente viene utilizzata l'istanza **WnsService** esposta dall'oggetto **NotificationHubService** per inviare una notifica di tipo avviso popup.

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <span id="next"></span></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'uso dell'Hub di notifica, usare i collegamenti seguenti per altre informazioni.

-   Riferimento in MSDN: [Hub di notifica di Azure][Hub di notifica di Azure].
-   Repository [Azure SDK for Node][Azure SDK for Node] su GitHub.

  [Passaggi successivi]: #next
  [Informazioni su Hub di notifica]: #hub
  [Creazione di un'applicazione Node.js]: #create
  [Configurare l'applicazione per l'uso dell'Hub di notifica]: #config
  [Procedura: Inviare notifiche]: #send
  [Hub di notifica di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj927170.aspx
  [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Sito Web con WebMatrix]: /it-it/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Implementazione del server GCM]: http://developer.android.com/google/gcm/server.html#payload
  [guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html
  [Push di notifiche da un server]: http://msdn.microsoft.com/it-it/library/hh221551.aspx
  [Intestazioni delle richieste e delle risposte per il servizio di notifica Push]: http://msdn.microsoft.com/it-it/library/windows/apps/hh465435.aspx
  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
