---
title: Effettuare il push di notifiche alle app Xamarin.Android con Hub di notifica di Azure | Microsoft Docs
description: Questa esercitazione contiene informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 94e8e813b537d304e62854b81979d433d0645115
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920745"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Esercitazione: effettuare il push di notifiche alle app Xamarin.Android con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Xamarin.Android. Viene creata un'app di Xamarin.Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM). È possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare un progetto Firebase e attivare Firebase Cloud Messaging
> * Creare un hub di notifica
> * Creare un'app Xamarin.Android e connetterla all'hub di notifica
> * Inviare notifiche di prova dal portale di Azure

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- [Visual Studio con Xamarin] in Windows o [Visual Studio per Mac] in OS X.
- Un account Google attivo

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Creare un progetto Firebase e attivare Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Creare un hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Configurare le impostazioni di GCM per l'hub di notifica

1. Selezionare **Google (GCM)** nella sezione **IMPOSTAZIONI DI NOTIFICA**. 
2. Immettere la **chiave del server legacy** annotata dalla Google Firebase Console. 
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

L'hub di notifica è configurato per l'uso con FCM e sono disponibili le stringhe di connessione per registrare l'app in modo da ricevere notifiche e inviare notifiche push.

## <a name="create-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Creare un'app Xamarin.Android e connetterla all'hub di notifica

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Creare un progetto di Visual Studio e aggiungere i pacchetti NuGet
1. In Visual Studio andare su **File**, selezionare **New** (Nuovo), quindi fare clic su **Project** (Progetto). 
   
      ![Visual Studio - Creare un nuovo progetto Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. Nella finestra **Esplora soluzioni** espandere **Proprietà** e fare clic su **AndroidManifest.xml**. Aggiornare il nome del pacchetto in modo che corrisponda al nome del pacchetto immesso durante l'aggiunta di Firebase Cloud Messaging al progetto in Google Firebase Console. 

      ![Nome del pacchetto in GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**. 
4. Selezionare la scheda **Sfoglia**. Cercare **Xamarin.GooglePlayServices.Base**. Selezionare **Xamarin.GooglePlayServices.Base** nell'elenco dei risultati. Quindi, selezionare **Installa**. 

      ![NuGet Google Play Services](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. Nella finestra **Gestione pacchetti NuGet** cercare il pacchetto **Xamarin.Firebase.Messaging**. Selezionare **Xamarin.Firebase.Messaging** nell'elenco dei risultati. Quindi, selezionare **Installa**. 
6. A questo punto, cercare **Xamarin.Azure.NotificationHubs.Android**. Selezionare **Xamarin.Azure.NotificationHubs.Android** nell'elenco dei risultati. Quindi, selezionare **Installa**. 

### <a name="add-the-google-services-json-file"></a>Aggiungere il file JSON di Google Services

1. Copia **google services.json** scaricato da Google Firebase Console nella cartella del progetto.
2. Aggiungere **google-services.json** al progetto.
3. Selezionare **google-services.json** nella finestra **Esplora soluzioni**.
4. Nel riquadro **Proprietà** impostare l'azione di compilazione **GoogleServicesJson**. Se non viene visualizzato **GoogleServicesJson**, chiudere Visual Studio, riavviarlo, riaprire il progetto e riprovare. 

      ![Azione di compilazione GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurare Hub di notifica nel progetto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrazione con Firebase Cloud Messaging

Aprire il file **AndroidManifest.xml** e inserire gli elementi `<receiver>` seguenti nell'elemento `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Raccogliere le informazioni seguenti per l'app Android e l'hub di notifica:
   
   * **Stringa di connessione Listen**: nel dashboard del [portale di Azure] scegliere **Visualizza stringhe di connessione**. Copiare la stringa di connessione *DefaultListenSharedAccessSignature* per questo valore.
   * **Nome hub**: nome dell'hub indicato nel [portale di Azure]. Ad esempio, *mynotificationhub2*.
     
2. Fare clic con il pulsante destro del mouse sul **progetto** in **Esplora soluzioni**, scegliere **Aggiungi** e selezionare **Classe**. 
4. Creare una classe **Constants.cs** per il progetto Xamarin e definire i valori costanti seguenti nella classe. Sostituire i segnaposto con i valori.
    
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
7. Creare una nuova classe **MyFirebaseIIDService** come è stata creata la classe **Costanti**. 
8. Aggiungere le istruzioni using seguenti a **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.App;
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. In **MyFirebaseIIDService.cs** aggiungere la dichiarazione **class** seguente e definire la classe in modo che erediti da **FirebaseInstanceIdService**:
   
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
        using Android.App;
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
15. **Compilare** il progetto. 
16. **Eseguire** l'app nel dispositivo o nell'emulatore caricato

## <a name="send-test-notification-from-the-azure-portal"></a>Inviare notifiche di prova dal portale di Azure
È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione, le notifiche sono state trasmesse a tutti i dispositivi Android registrati con il back-end. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi Android specifici, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


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

[Portale di Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
