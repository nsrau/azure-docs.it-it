---
title: Eseguire il push di notifiche alle app Kindle con Hub di notifica di Azure | Microsoft Docs
description: In questa esercitazione, sono presenti informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927021"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introduzione ad Hub di notifica per le app per Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione Kindle. Viene creata un'app Kindle vuota che riceve notifiche push tramite Amazon Device Messaging (ADM).

In questa esercitazione si crea/aggiorna il codice per eseguire le attività seguenti:

> [!div class="checklist"]
> * Aggiunta di una nuova app al portale per sviluppatori
> * Creazione di una chiave API
> * Creare e configurare un hub di notifica
> * Configurazione dell'applicazione
> * Creazione del gestore di messaggi ADM
> * Aggiunta della chiave API all'app
> * Esecuzione dell'app
> * Invio di una notifica di prova

## <a name="prerequisites"></a>Prerequisiti

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Seguire i passaggi in [Configurazione dell’ambiente di sviluppo](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) per impostare l'ambiente di sviluppo per Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Aggiunta di una nuova app al portale per sviluppatori

1. Accedere al [portale per sviluppatori Amazon](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Selezionare **Add New App** (Aggiungi nuova app) e quindi **Android**.  

    ![Pulsante Add New App](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Nella pagina **New App Submission** (Invio nuova app) seguire questa procedura per ottenere la **chiave dell'applicazione**:
    1. Immettere un nome in **App title** (Titolo dell'app).
    2. Selezionare una qualsiasi **categoria**, ad esempio education (istruzione)
    4. Immettere un indirizzo di posta elettronica nel campo **Customer support email address** (Indirizzo di posta elettronica del supporto clienti). 
    5. Selezionare **Salva**.

        ![Pagina New App Submission](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  Nella parte superiore passare alla scheda **Mobile Ads** (Annunci app per dispositivi mobili) e seguire questa procedura: 
    1. Specificare se l'app è destinata principalmente a ragazzi di età inferiore a 13 anni. Per questa esercitazione selezionare **No**.
    2. Selezionare **Submit** (Invia). 

        ![Pagina Mobile Ads](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Copiare la **chiave dell'applicazione** dalla pagina **Mobile Ads** (Annunci app per dispositivi mobili). 

        ![Chiave applicazione](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Selezionare **Apps & Services** (App e servizi) nella parte superiore e quindi selezionare l'applicazione nell'elenco. 

    ![Selezionare l'app nell'elenco](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Passare alla scheda **Device Messaging** (Messaggi dispositivo) e seguire questa procedura: 
    1. Selezionare **Create a New Security Profile** (Crea un nuovo profilo di sicurezza).
    2. Immettere un **nome** per il profilo di sicurezza. 
    3. Immettere la **descrizione** per il profilo di sicurezza. 
    4. Selezionare **Salva**. 
    5. Selezionare **View Security Profile** (Visualizza profilo di sicurezza) nella pagina dei risultati. 
5. A questo punto, nella pagina **Security Profile** (Profilo di sicurezza) seguire questa procedura: 
    1. Passare alla scheda **Web Settings** (Impostazioni Web) e copiare i valori di **Client ID** (ID client) e **Client Secret** (Segreto client) per usarli in seguito. 

        ![Ottenere ID client e chiave privata](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Passare alla pagina **Android/Kindle Settings** (Impostazioni Android/Kindle) e mantenere aperta la pagina. Questi valori verranno immessi nella sezione successiva. 

## <a name="create-an-api-key"></a>Creazione di una chiave API
1. Aprire un prompt dei comandi con privilegi di amministratore.
2. Passare alla cartella Android SDK.
3. Immettere il comando seguente:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Per la password **keystore**, digitare **android**.
5. Copiare le impronte digitali **MD5** e **SHA256**. 
6. Sempre nel portale per sviluppatori seguire questa procedura nella scheda **Android/Kindle Settings** (Impostazioni Android/Kindle): 
    1. Immettere un **nome per la chiave API**. 
    2. Immettere il **nome del pacchetto** per l'app (ad esempio **com.fabrikam.mykindleapp**) e il valore **MD5**.
        
        >[!IMPORTANT]
        > Quando si crea un'app in Android Studio, usare lo stesso nome di pacchetto specificato qui. 
    1. Incollare la **firma MD5** copiata in precedenza. 
    2. Incollare la **firma SHA256** copiata in precedenza.  
    3. Selezionare **Generate New Key** (Genera nuova chiave).

        ![Impostazioni Android/Kindle - generazione della chiave](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. A questo punto, selezionare **Show** (Mostra) nell'elenco per visualizzare la chiave API. 

        ![Impostazioni Android/Kindle - visualizzazione della chiave API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. Nella finestra **API Key Details** (Dettagli chiave API) copiare la chiave API e salvarla altrove. Fare clic sulla **X** nell'angolo in alto a destra per chiudere la finestra. 


## <a name="create-and-configure-a-notification-hub"></a>Creare e configurare un hub di notifica

1. Seguire la procedura nell'articolo [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md) per creare un hub di notifica. 
2. Selezionare **Amazon (ADM)** nel menu **Settings** (Impostazioni).
3. Incollare l'**ID client** e il **segreto client** salvati in precedenza. 
4. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare le impostazioni di ADM per un hub di notifica](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Selezionare **Access Policies** (Criteri di accesso) nel menu a sinistra e quindi il pulsante **copy** (copia) per la stringa di connessione del criterio **DefaultListenSharedAccessSignature**. Salvare il file in un punto qualsiasi. Verrà usato più avanti nel codice sorgente. 

    ![Hub di notifica - stringa di connessione del listener](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Configurazione dell'applicazione

1. Avviare Android Studio. 
2. Selezionare **File**, scegliere **New** (Nuovo) e quindi **New Project** (Nuovo progetto). 
3. Nella finestra **Choose your project** (Scegli il progetto) selezionare **Empty Activity** (Attività vuota) nella scheda **Phone and Tablet** (Telefono e tablet) e quindi selezionare **Next** (Avanti). 
4. Nella finestra **Configure your project** (Configura il progetto) seguire questa procedura:
    1. Immettere un **nome per l'applicazione**. È consigliabile specificare lo stesso nome dell'applicazione creata nel portale per sviluppatori di Amazon. 
    2. Immettere un **nome per il pacchetto**. 
        
        >[!IMPORTANT]
        >Il nome del pacchetto deve essere uguale a quello specificato nel portale per sviluppatori di Amazon.
    3. Esaminare e aggiornare i valori rimanenti in base alle necessità. 
    4. Selezionare **Fine**. 

        ![Configurare il progetto Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Scaricare la libreria [Amazon Developer SDK for Android](https://developer.amazon.com/sdk-download) nel disco rigido. Estrarre il file zip SDK.
6. In Android Studio modificare la struttura delle cartelle da **Android** a **Project** se non è già impostata su **Project**. 

    ![Android Studio - passaggio alla struttura del progetto](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Espandere **app** per visualizzare la cartella **libs** nella visualizzazione struttura ad albero.     
8. In una finestra di Esplora file passare alla cartella in cui sono stati scaricati gli Amazon Android SDK.
9. Premere **CTRL** e trascinare il file **amazon-device-messaging-1.0.1.jar** nel nodo **lib** nella visualizzazione struttura ad albero. 

    ![Android Studio - aggiunta del file JAR di Amazon Device Messaging](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. Nella finestra **Copy** (Copia) selezionare **OK**. Se viene visualizzata la finestra **Move** (Sposta) invece della finestra **Copy** (Copia), chiuderla e ripetere l'operazione di trascinamento tenendo premuto il tasto **CTRL**. 

    ![Android Studio - copia del file JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Aggiungere l'istruzione seguente al file **build.gradle dell'app** nella sezione **dependencies**: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio - aggiunta di ADM al file build.gradle dell'app](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. Nel file `Build.Gradle` relativo all'**app** aggiungere le righe seguenti nella sezione **dependencies**: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Aggiungere il repository seguente **dopo** la sezione **dependencies**:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. Nell'editor relativo al file **build.gradle** per l'**app** selezionare **Sync now** (Sincronizza ora) sulla barra degli strumenti. 

    ![Android Studio - sincronizzazione del file build.gradle dell'app](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Tornare alla struttura Android nella visualizzazione struttura ad albero.  Aggiungere lo spazio dei nomi Amazon nell'elemento manifesto radice:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Spazio dei nomi Amazon nel manifesto](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Aggiungere le autorizzazioni come primo elemento sotto l'elemento manifesto. Sostituire **[YOUR PACKAGE NAME]** con il pacchetto usato per creare l'app.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Inserire l'elemento seguente come primo figlio dell'elemento dell'applicazione. Sostituire **[YOUR PACKAGE NAME]** con il nome del pacchetto con cui è stata creata l'app. La classe MyADMMessageHandler verrà creata nel passaggio successivo. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Creazione del gestore di messaggi ADM

1. Aggiungere al pacchetto `com.fabrikam.mykindleapp` del progetto una nuova classe che eredita da `com.amazon.device.messaging.ADMMessageHandlerBase` e assegnare alla classe il nome `MyADMMessageHandler`, come illustrato nell'immagine seguente:

    ![Creazione della classe MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Aggiungere le istruzioni `import` seguenti alla classe `MyADMMessageHandler`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Aggiungere il codice seguente alla classe creata. Sostituire `[HUB NAME]` e `[LISTEN CONNECTION STRING]` con il nome dell'hub di notifica e la stringa di connessione del listener: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Aggiunta della chiave API all'app
1. Seguire questa procedura per aggiungere una cartella assets al progetto. 
    1. Passare alla visualizzazione **Project** (Progetto). 
    2. Fare clic con il pulsante destro del mouse su **app**.
    3. Selezionare **Nuovo**.
    4. Selezionare **Folder** (Cartella). 
    5. Selezionare **Assets Folder** (Cartella assets). 

        ![Menu per l'aggiunta della cartella assets](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Nella pagina **Configure Component** (Configura componente) seguire questa procedura:
        1. Selezionare **Change folder location** (Cambia percorso della cartella).
        2. Confermare che la cartella sia impostata su `src/main/assets`.
        3. Selezionare **Fine**. 
        
            ![Configurare la cartella assets](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Aggiungere un file denominato **api_key.txt** alla cartella **assets**. Nella visualizzazione struttura ad albero espandere **app**, **src**, **main** e quindi fare clic con il pulsante destro del mouse su **assets**, scegliere **New** (Nuovo) e selezionare **File**. Immettere **api_key.txt** come nome file. 3. 
5. Copiare nel file api_key.txt la chiave API generata nel portale per sviluppatori Amazon. 
6. Compilare il progetto. 

## <a name="run-the-app"></a>Esecuzione dell'app
1. Nel dispositivo Kindle scorrere dall'alto e fare clic su **Settings** (Impostazioni), quindi fare clic su **My account** (Account personale) ed effettuare la registrazione usando un account Amazon valido.
2. Eseguire l'app in un dispositivo Kindle da Android Studio. 

> [!NOTE]
> Se si verifica un problema, controllare l'ora dell'emulatore (o del dispositivo). Il valore dell'ora deve essere accurato. Per modificare l'ora dell'emulatore Kindle è possibile eseguire il comando seguente dalla directory di strumenti della piattaforma Android SDK:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Inviare un messaggio di notifica

Per inviare un messaggio usando .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Per il codice di esempio vedere [questo esempio in GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, le notifiche sono state trasmesse a tutti i dispositivi Kindle registrati con il back-end. Per informazioni sulle procedure per effettuare il push di notifiche a dispositivi specifici, passare all'esercitazione seguente:  L'esercitazione seguente illustra come effettuare il push di notifiche a dispositivi Android specifici, ma è possibile usare la stessa logica con dispositivi Kindle specifici.

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
