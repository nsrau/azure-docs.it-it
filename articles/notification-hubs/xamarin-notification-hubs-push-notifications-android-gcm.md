---
title: Introduzione a Hub di notifica per le app per Xamarin.Android | Documentazione Microsoft
description: Questa esercitazione contiene informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin Android.
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 1cb6fbc82c493e17815dc60ddcff183a47513bc6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Introduzione a Hub di notifica per le app per Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Xamarin.Android. Verrà creata un'app di Xamarin.Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM). Al termine, sarà possibile usare l’hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica.

## <a name="before-you-begin"></a>Prima di iniziare
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione è disponibile su GitHub [qui][GitHub].

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* [Visual Studio con Xamarin] in Windows o [Visual Studio per Mac] in OS X.
* Un account Google attivo

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.Android.

> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Abilitare Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configurare l'hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Scegliere la scheda <b>Configure</b> (Configura) nella parte superiore, immettere il valore di <b>API Key</b> (Chiave API) ottenuto nella sezione precedente e quindi selezionare su <b>Save</b> (Salva).</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

L'hub di notifica è configurato per l'uso con FCM e sono disponibili le stringhe di connessione per registrare l'app in modo da ricevere notifiche e inviare notifiche push.

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica
Creare prima di tutto un nuovo progetto. 

1. In Visual Studio scegliere **Nuova soluzione** > **App Android** e quindi selezionare **Avanti**.
   
      ![Visual Studio - Creare un nuovo progetto Android][22]

2. Compilare i campi **App Name** (Nome app) e **Identifier** (Identificatore). Scegliere le **piattaforme di destinazione** da supportare e quindi fare clic su **Avanti** e su **Crea**.
   
      ![Visual Studio - Configurazione di un'app Android][23]

    In questo modo viene creato un nuovo progetto Android.

3. Aprire le proprietà del progetto facendo clic con il pulsante destro del mouse sul nuovo progetto nella visualizzazione Solution e scegliendo **Options**. Selezionare la voce **Android Application** (Applicazione Android) nella sezione **Build** (Crea).
   
    Verificare che la prima lettera di **Package name** sia minuscola.
   
   > [!IMPORTANT]
   > La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario verranno visualizzati errori del manifesto dell'applicazione quando l'app verrà registrata per le notifiche push più avanti.
   > 
   > 
   
      ![Visual Studio - Opzioni del progetto Android][24]
4. Impostare facoltativamente **Minimum Android version** su un altro livello API.
5. Impostare eventualmente la **versione di destinazione di Android** su un'altra versione dell'API di destinazione (deve essere almeno API livello 8 o superiore).
6. Scegliere **OK** e chiudere la finestra di dialogo delle opzioni del progetto.

### <a name="add-the-required-packages-to-your-project"></a>Aggiungere i pacchetti necessari al progetto

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Aggiungi pacchetti NuGet**
2. Cercare **Xamarin.Azure.NotificationHubs.Android** e aggiungerlo al progetto.
3. Cercare **Xamarin.Firebase.Messaging** e aggiungerlo al progetto.

### <a name="set-up-notification-hubs-in-your-project"></a>Configurare Hub di notifica nel progetto
1. Raccogliere le informazioni seguenti per l'app Android e l'hub di notifica:
   
   * **Stringa di connessione Listen**: nel dashboard del [portale di Azure] scegliere **Visualizza stringhe di connessione**. Copiare la stringa di connessione *DefaultListenSharedAccessSignature* per questo valore.
   * **Nome hub**: si tratta del nome dell'hub indicato nel [portale di Azure]. Ad esempio, *mynotificationhub2*.
     
2. Creare una classe **Constants.cs** per il progetto Xamarin e definire i valori costanti seguenti nella classe. Sostituire i segnaposto con i valori.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```

3. Aggiungere le istruzioni using seguenti a **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```

4. Aggiungere a **MainActivity.cs** una variabile di istanza che verrà usata per visualizzare una finestra di dialogo di avviso quando l'app è in esecuzione:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```

5. Aggiungere il codice seguente a `OnCreate` in **MainActivity.cs** dopo `base.OnCreate(savedInstanceState)`:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```

6. Fare clic con il pulsante destro del mouse sul progetto e aggiungere il file `google-services.json` scaricato in precedenza dal progetto Firebase. Fare clic con il pulsante destro del mouse sul file aggiunto e impostare l'azione di compilazione su `GoogleServicesJson`

    ![Visual Studio - Configurare google-services.json][25]

7. Creare una nuova classe, **MyFirebaseIIDService**.

8. Aggiungere le istruzioni using seguenti a **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. In **MyFirebaseIIDService.cs** aggiungere il codice seguente sopra la dichiarazione **class** e definire la classe in modo che erediti da **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```

10. In **MyFirebaseIIDService.cs** aggiungere il codice seguente:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```

11. Creare un'altra nuova classe per il progetto e assegnarle il nome **MyFirebaseMessagingService**.

12. Aggiungere le istruzioni using seguenti a **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. Aggiungere il codice seguente sopra la dichiarazione class e definire la classe in modo che erediti da **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
    
14. Aggiungere il codice seguente a **MyFirebaseMessagingService.cs**:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

15. Eseguire l'app nel dispositivo o nell'emulatore caricato

## <a name="send-notifications-from-the-portal"></a>Inviare notifiche dal portale
È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà così inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

Di seguito è riportato un elenco di altre esercitazioni, che è possibile esaminare per l'invio di notifiche:

* ASP.NET: vedere [Usare Hub di notifica per inviare notifiche push agli utenti].
* Azure Notification Hubs Java SDK: vedere [Come usare Hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato testato in Eclipse per lo sviluppo per Android.
* PHP: vedere [Come usare Hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche sono state trasmesse a tutti i dispositivi Android. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere [Panoramica dell'Hub di notifica] e [Notification Hubs How-To for Android.] (Procedura di Hub di notifica per Android).

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio con Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio per Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[portale di Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Panoramica dell'Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android.]: http://msdn.microsoft.com/library/dn282661.aspx

[Usare Hub di notifica per inviare notifiche push agli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
