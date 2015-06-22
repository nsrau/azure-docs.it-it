<properties 
	pageTitle="Introduzione ad Hub di notifica di Azure" 
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/15/2014" 
	ms.author="ricksal"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android. 
In questa esercitazione verrà creata un'app per Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

* [Abilitare Google Cloud Messaging](#register)
* [Configurare l'hub di notifica](#configure-hub)
* [Connettere l'app all'hub di notifica](#connecting-app)
* [Come inviare notifiche all'app](#send)
* [Testare l'app](#run-app)

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. 

Per completare questa esercitazione, è necessario disporre di:

+ Android SDK (si presuppone che verrà usato Eclipse), che è possibile scaricare facendo clic su <a href="http://go.microsoft.com/fwlink/?LinkId=389797">questo collegamento</a>
+ [Mobile Services Android SDK]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android. 

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).

##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure-hub"></a>Configurare l'hub di notifica

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

###Creare un nuovo progetto Android

1. Nel plug-in ADT per Eclipse, creare un nuovo progetto Android (File, New, Android Application).

   	![][13]

2. Assicurarsi che l'**SDK minimo richiesto** sia impostato su  *API 8: Android 2.2 (Froyo)* e che le successive due voci di SDK siano impostate sulla versione più recente disponibile. Scegliere Next e seguire le istruzioni della procedura guidata, verificando che sia selezionata l'opzione **Create activity** per creare un'attività vuota. Nella finestra successiva accettare l'icona Launcher predefinita e fare clic su **Finish** nell'ultima finestra.

   	![][14]

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Add Play Services](../../includes/mobile-services-add-Google-play-services.md)]

###Aggiungere codice

1. Scaricare Notification Hubs Android SDK da <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">questo collegamento</a>. Estrarre il file ZIP e copiare il file notificationhubs\notification-hubs-0.1.jar nella directory \libs del progetto in Package Explorer.

2. Scaricare e decomprimere [Android SDK per Servizi mobili], aprire la cartella delle **notifiche**, copiare il file **notifications-1.0.1.jar** nella cartella delle  *librerie* del progetto Eclipse e aggiornare la cartella delle  *librerie*.

    > [AZURE.NOTE] È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.

	Configurare quindi l'applicazione per ottenere un  *registrationId* da GCM e usare questo ID per registrare l'istanza dell'app nell'hub di notifica.

3. Nel file AndroidManifest.xml aggiungere la seguente riga di codice sotto l'elemento <uses-sdk/> Assicurarsi di sostituire `<your package>` con il pacchetto selezionato per l'app nel passaggio 1 (`com.yourCompany.wams_notificationhubs` in questo esempio).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. Nella classe **MainActivity** aggiungere le seguenti istruzioni.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Aggiungere i membri privati seguenti nella parte superiore della classe.

	> [AZURE.NOTE] Assicurarsi di impostare SENDER_ID sul numero di progetto ottenuto in precedenza.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Nel metodo **OnCreate** aggiungere il codice seguente e assicurarsi di sostituire i segnaposto con la stringa di connessione con accesso Listen ottenuta nella sezione precedente e con il nome dell'hub di notifica visualizzato nella parte superiore di Azure per l'hub (**non** l'URL completo).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. In MainActivity.java creare il metodo seguente:

		@SuppressWarnings("unchecked")
		private void registerWithNotificationHubs() {
		   new AsyncTask() {
		      @Override
		      protected Object doInBackground(Object... params) {
		         try {
		            String regid = gcm.register(SENDER_ID);
		            hub.register(regid);
		         } catch (Exception e) {
		            return e;
		         }
		         return null;
		     }
		   }.execute(null, null, null);
		}

8. Poiché il sistema operativo Android non consente la visualizzazione di notifiche, è necessario scrivere un apposito ricevitore. In **AndroidManifest.xml** aggiungere l'elemento seguente all'interno dell'elemento `<application/>`.

	> [AZURE.NOTE] Sostituire il segnaposto con il nome del pacchetto.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. In Esplora pacchetti, fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), fare clic su **Nuovo**, quindi su **Classe**.

10. In **Nome** digitare  `MyHandler`, in **Superclasse** digitare  `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Fine**

	![][6]

	Verrà creata la nuova classe MyHandler.

11. Aggiungere le seguenti istruzioni import:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		

12. Aggiungere il codice seguente alla classe:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;
	
		
		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("msg");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, MainActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	

##<a name="send"></a>Come inviare una notifica all'app

>[AZURE.NOTE]È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio di come inviare notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere [Introduzione alle notifiche push in Servizi mobili](../mobile-services-javascript-backend-android-get-started-push.md).  Per un esempio di come inviare notifiche tramite le API REST, vedere [Come usare Hub di notifica da Java](notification-hubs-java-backend-how-to.md) oppure [Come usare Hub di notifica da PHP](notification-hubs-php-backend-how-to.md).

1. In Visual Studio dal menu **File** scegliere **Nuovo** e quindi **Progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine su **OK**.  

   	![][20]

	Verrà creato un nuovo progetto di applicazione console.

2. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**. 

	Verrà visualizzata la Console Gestione pacchetti.

3. Nella finestra della console eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus
    
	Verrà aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto WindowsAzure.ServiceBus NuGet</a>. 

4. Aprire il file Program.cs e aggiungere la seguente istruzione  `using`:

        using Microsoft.ServiceBus.Notifications;

5. Nella classe **Program** aggiungere il seguente metodo:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"msg":"Hello from Azure!"}}");
        }

   	Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurare l'hub di notifica". 

	>[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

5. Aggiungere quindi le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Test dell'app

Prima di testare l'app in un emulatore, sarà necessario completare queste procedure di configurazione dell'emulatore. Se si esegue il test in un dispositivo fisico, è possibile ignorare questi passaggi:

1. assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

2. In **Window** fare clic su **Android Virtual Device Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

   	![][18]

3. Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

   	![][19]

4. 	Per ricevere notifiche push, è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo <strong>Settings</strong> e quindi <strong>Add Account</strong>. Assicurarsi inoltre che l'emulatore sia connesso a Internet.


Per eseguire l'app in un dispositivo o nell'emulatore, attenersi alla procedura seguente:

1. Nella barra degli strumenti superiore di Eclipse fare clic su **Run** e quindi selezionare l'app. 
 
	Verrà avviato l'emulatore, se usato, e l'app verrà caricata ed eseguita. L'app recupera la proprietà  *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

3. Premere F5 in Visual Studio per eseguire l'app della console. 

	Una notifica verrà inviata all'app.  
 
5. Quando viene visualizzata un'icona nell'area di notifica (angolo superiore sinistro), aprire la cassetta per visualizzare la notifica.  

   	![][21]

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per inviare notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera suddividere gli utenti per gruppi di interesse, consultare [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere le [informazioni aggiuntive su Hub di notifica].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK per Servizi mobili]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Riferimento a un progetto di libreria]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx

[Usare Hub di notifica per inviare notifiche push agli utenti]: notification-hubs-aspnet-backend-android-notify-users.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-aspnet-backend-android-breaking-news.md


<!--HONumber=49--> 