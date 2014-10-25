<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

Questo argomento descrive come usare Servizi mobili di Windows Azure per inviare notifiche push ad app per iOS e Android sviluppate con Appcelerator Titanium Studio. In questa esercitazione si useranno il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) e Google Cloud Messaging per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE] Servizi mobili è ora integrato con Hub di notifica di Azure e supporta ulteriori funzionalità di notifica push, ad esempio modelli, più piattaforme e scalabilità migliorata. In questo argomento sono supportati servizi mobili esistenti che non sono stati ancora aggiornati per l'uso dell'integrazione di Hub di notifica. Quando si crea un nuovo servizi mobile, questa funzionalità integrata è abilitata automaticamente. È consigliabile aggiornare il servizio per l'uso di Hub di notifica quando possibile. **A breve verrà resa disponibile un'esercitazione per il push di Hub di notifica con Appcelerator.**

1.  [Generazione della richiesta di firma del certificato][Generazione della richiesta di firma del certificato]
2.  [Registrazione dell'app e abilitazione delle notifiche push][Registrazione dell'app e abilitazione delle notifiche push]
3.  [Creazione di un profilo di provisioning per l'app][Creazione di un profilo di provisioning per l'app]
4.  [Abilitazione di Google Cloud Messaging][Abilitazione di Google Cloud Messaging]
5.  [Creazione del modulo GCM per Titanium][Creazione del modulo GCM per Titanium]
6.  [Configurazione di Servizi mobili][Configurazione di Servizi mobili]
7.  [Aggiunta di notifiche push all'app][Aggiunta di notifiche push all'app]
8.  [Aggiornamento degli script per l'invio di notifiche push][Aggiornamento degli script per l'invio di notifiche push]
9.  [Inserimento di dati per la ricezione di notifiche][Inserimento di dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario disporre di:

-   Modulo per Servizi mobili di Azure di Appcelerator
-   Appcelerator Titanium Studio 3.2.1 o versione successiva
-   Dispositivo con iOS 7.0 (o versione successiva) e Android 4.3 (o versione successiva)
-   Iscrizione a iOS Developer Program
-   Un account Google attivo

> [WACOM.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

[WACOM.INCLUDE [Abilitare le notifiche push per Apple][Abilitare le notifiche push per Apple]]

## <a name="register-gcm"></a>Abilitazione di Google Cloud Messaging

> [WACOM.NOTE]Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

## <a name="gcm-module"></a>Creazione del modulo GCM per Titanium

### Preparazione di Appcelerator Titanium Studio per la creazione di moduli

Se si intende creare moduli per Android, è necessario installare il supporto Java all'interno di Appcelerator Titanium Studio. Se questa operazione non è stata ancora completata, per una breve procedura, vedere l'argomento relativo all'[installazione degli strumenti di sviluppo Java][installazione degli strumenti di sviluppo Java] di Appcelerator.

È necessario installare Android NDK. Scaricare il file ZIP appropriato da [][]<http://developer.android.com/sdk/ndk/index.html></a> ed estrarlo in una directory del disco. Prendere nota di tale directory.

### Creazione di un nuovo modulo

1.  Aprire Appcelerator Titanium Studio.

2.  Fare clic su File -\> New -\> Mobile Module Project.

    ![][]

3.  Nella finestra successiva immettere i dati relativi alle impostazioni del progetto:

    -   **Project name:** in questo caso verrà usato "notificationhub" (può essere uguale).

    -   **Module Id:** in questo caso verrà usato "com.winwire.notificationhub". Questa impostazione deve essere uguale al valore dell'ID applicazione.

    -   **Deployment Targets:** in questo caso verrà selezionato Android.

    > [WACOM.NOTE] È importante che il nome dell'area di lavoro non contenga spazi; in caso contrario, si verificheranno problemi durante la creazione della compilazione.

    ![][1]

4.  Fare clic su next e completare le informazioni per il modulo.

    ![][2]

5.  Infine fare clic su finish.

6.  Aprire il file timodule.xml. Aggiungere le seguenti modifiche nel tag android.

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

> [WACOM.NOTE] Nel codice precedente è necessario sostituire tutte le istanze del testo *com.winwire.notificationhub* con il nome del pacchetto dell'applicazione (Module Id).

1.  Nel modulo Hub di notifica fare clic con il pulsante destro del mouse sulla cartella "src" e passare a "Nuovo", quindi selezionare "cartella". Assegnare alla cartella il nome com.google.android.gcm.

> [WACOM.NOTE] Se l'opzione "cartella" non è visibile in "Nuovo", selezionare "Altro" ed espandere Generale, quindi selezionare "Cartella".

1.  Scaricare ora i file ".java" (gcm.zip) da questa posizione e copiarli nella nuova cartella creata (com.google.android.gcm).

2.  Individuare ora la cartella denominata come Module Id ed espanderla. In tale cartella è presente l'elenco dei file ".java". In questi file aprire il file il cui nome è nomeprogetto+module.java (ad esempio, se il nome del progetto è notificationhub, il nome sarà simile a "NotificationhubModule.java") e aggiungere le righe di codice seguenti nella parte iniziale.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  Nello stesso file individuare il costruttore e sostituirlo con il codice seguente.

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  Nello stesso file aggiungere le righe di codice seguenti nella parte finale.

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  Scaricare ora module.zip e copiare i file nella cartella il cui nome è uguale a quello indicato in Module Id.

> [WACOM.NOTE] Nei file precedenti è necessario sostituire tutte le istanze del testo *com.winwire.notificationhub* con il nome del pacchetto dell'applicazione (Module Id). Sostituire anche "NotificationhubModule" con NomeProgetto+Module (ad esempio "NotificationhubModule").

### Compilazione/Creazione del pacchetto di un modulo

Scegliere **Deploy \> Package - Android Module**. Non è possibile compilare il modulo BlackBerry con Studio; usare gli strumenti CLI di BlackBerry NDK o Momentics IDE.

![][3]

È quindi possibile scegliere di distribuire il modulo per tutti i progetti o per un progetto specifico. Per questa procedura vengono usate le regole di installazione indicate nell'articolo sull'[uso di moduli Titanium][uso di moduli Titanium], ma per riepilogare:

-   Per tutti i progetti: il file module .zip viene rilasciato nella radice del percorso di installazione di Titanium SDK.

-   Per un progetto specifico: il file module .zip viene rilasciato nella radice del progetto.

## <a name="configure"></a>Configurazione di Servizi mobili per l'invio di richieste push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

1.  Immettere il valore Chiave API ottenuto da GCM nella procedura precedente, quindi fare clic su Salva.

    ![][4]

Il servizio mobile è ora configurato per funzionare con APNS e GCM.

## <a name="add-push"></a>Aggiungere notifiche push all'app

1.  In tiapp.xml selezionare la scheda tiapp.xml (presente nella parte inferiore accanto alla scheda della panoramica) e individuare il tag `<android>`. Sotto il tag aggiungere il codice seguente:

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

> [WACOM.NOTE] Nel codice precedente è necessario sostituire **ModuleId** e **ApplicationId**, ovvero il valore di Module ID, assegnato durante la creazione del modulo GCM, e il valore di Application Id, immesso durante la creazione del progetto. Assicurarsi inoltre che i valori di **ModuleId** e **ApplicationId** siano uguali. È inoltre necessario cambiare **ApplicationId.AppNameActivity** in modo che sia simile a com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.

1.  Copiare il modulo GCM creato in precedenza e inserirlo nel percorso indicato di seguito.

    <table>
    <tr>
    <td>
    OSX

    </td>
    <td>
    /Library/Application Support/Titanium o ~/Library/Application Support/Titanium

    </td>
    </tr>
    <td>
    Windows 7

    </td>
    <td>
    C:\\Utenti\\nomeutente\\AppData\\Roaming (o C:\\ProgramData\\Titanium in Titanium Studio 1.0.1 e versioni precedenti)

    </td>
    </tr>
    <td>
    Windows XP

    </td>
    <td>
    C:\\Documents and Settings\\nomeutente\\Application Data (o C:\\Documents and Settings\\All Users\\Application Data\\Titanium in Titanium Studio 1.0.1 e versioni precedenti)

    </td>
    </tr>
    <td>
    Linux

    </td>
    <td>
    ~/.titanium

    </td>
    </tr>
    </tr>
    </table>

> [WACOM.NOTE] In Mac OS Library è una cartella nascosta. Per renderla visibile è necessario eseguire il comando seguente e quindi riavviare il Finder: `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  In Appcelerator Titanium Studio aprire il file index.js e aggiungere il codice seguente nella parte finale. Questo codice consentirà di registrare il dispositivo per le notifiche push.

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  Aprire ora il file TableData.js e individuare le righe seguenti nella funzione **addOrUpdateDataFromAndroid**

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Sostituire il codice sopra riportato solo nella condizione else (per inserire il nuovo elemento).

3.  Per Android sostituire il codice precedente con quello seguente:

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'handle' : Alloy.Globals.tempRegId
    };

4.  Individuare ora le righe seguenti nella funzione **updateOrAddData**

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Sostituire il codice sopra riportato solo nella condizione else (per inserire il nuovo elemento).

5.  Per iOS sostituire il codice precedente con quello seguente:

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'deviceToken' : Alloy.Globals.tempRegId
    };

L'app è ora aggiornata per il supporto delle notifiche push nelle piattaforme iOS e Android.

## <a name="update-scripts"></a>Aggiornamento dello script insert registrato nel portale di gestione

1.  Nel portale di gestione fare clic sulla scheda Dati e quindi sulla tabella TodoItem.

    ![][5]

2.  In todoitem fare clic sulla scheda Script, quindi selezionare Inserisci.

    ![][6]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella TodoItem.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

**Per iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**Per Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

Verrà registrato un nuovo script insert, che usa l'[oggetto push di Servizi mobili][oggetto push di Servizi mobili] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert.

<!-- Images. -->
<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C\#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Generazione della richiesta di firma del certificato]: #certificates
  [Registrazione dell'app e abilitazione delle notifiche push]: #register
  [Creazione di un profilo di provisioning per l'app]: #profile
  [Abilitazione di Google Cloud Messaging]: #register-gcm
  [Creazione del modulo GCM per Titanium]: #gcm-module
  [Configurazione di Servizi mobili]: #configure
  [Aggiunta di notifiche push all'app]: #add-push
  [Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [Abilitare le notifiche push per Apple]: ../includes/enable-apple-push-notifications.md
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [installazione degli strumenti di sviluppo Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  []: http://developer.android.com/sdk/ndk/index.html
  []: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [uso di moduli Titanium]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [oggetto push di Servizi mobili]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
