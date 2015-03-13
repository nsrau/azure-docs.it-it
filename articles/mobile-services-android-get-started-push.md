<properties 
	pageTitle="Introduzione alle notifiche push (Android) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app per Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding"> <a href="/it-it/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a> <a href="/it-it/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a> <a href="/it-it/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a> <a href="/it-it/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a> <a href="/it-it/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite il servizio Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

>[AZURE.NOTE]L'argomento supporta <em>servizi mobili</em> esistenti <em>che non sono stati ancora aggiornati</em> per l'uso dell'integrazione di Hub di notifica. Quando si crea un <em>nuovo</em> servizio mobile, questa funzionalità integrata è abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).
>
>Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. <em>È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile</em>. Dopo aver eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

* [Abilitare Google Cloud Messaging](#register)
* [Configurare Servizi mobili](#configure)
* [Aggiungere notifiche push all'app](#add-push)
* [Aggiornare gli script per l'invio di notifiche push](#update-scripts)
* [Inserire dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ [Mobile Services Android SDK]
+ Un account Google attivo

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

##<a id="register"></a>Abilitare Google Cloud Messaging


[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave API verrà usato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

##<a id="configure"></a>Configurare Servizi mobili per l'invio di richieste push

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![][18]

2. Fare clic sulla scheda **Push** e immettere il valore **Chiave API** ottenuto da GCM nella procedura precedente, quindi fare clic su **Salva**.

   	![][19]

Il servizio mobile è ora configurato per l'uso con GCM per l'invio di notifiche push.

##<a id="add-push"></a>Aggiungere notifiche push all'App


###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Aggiungere codice

1. Aprire il file di progetto **AndroidManifest.xml**. Google Cloud Messaging impone alcuni requisiti minimi a livello di API per lo sviluppo e il testing. La proprietà **minSdkVersion** nel file manifesto deve essere conforme a tali requisiti. Se è necessario impostare un valore inferiore a 16 perché si usa un dispositivo meno recente, per determinare il livello minimo su cui è possibile impostare tale valore, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK]. Impostare la proprietà in modo appropriato.

2. Verificare che nell'elemento  `uses-sdk` il valore di **targetSdkVersion** sia impostato sul numero di una piattaforma SDK installata (passaggio 1). È consigliabile impostare tale valore sulla versione più recente disponibile. 

3. Il tag **uses-sdk** potrebbe avere un aspetto analogo al seguente, in base alle scelte effettuate nei passaggi precedenti:

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. Nel codice per i due passaggi successivi sostituire _`**my_app_package**`_ con il nome del pacchetto dell'app per il progetto, ovvero il valore dell'attributo  `package` del tag  `manifest`. 

5. Aggiungere le nuove autorizzazioni seguenti dopo l'elemento  `uses-permission` esistente:
     <permission android:name="**my_app_package**.permission.C2D_MESSAGE"          android:protectionLevel="signature" />     <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />      <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />     <uses-permission android:name="android.permission.GET_ACCOUNTS" />     <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Aggiungere il codice seguente dopo il tag di apertura  `application`: 
     <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"         android:permission="com.google.android.c2dm.permission.SEND">         <intent-filter>             <action android:name="com.google.android.c2dm.intent.RECEIVE" />             <category android:name="**my_app_package**" />         </intent-filter>     </receiver>



7. Aprire il file ToDoItem.java, quindi aggiungere il codice seguente alla classe **TodoItem**:

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	Tale codice consente di creare una nuova proprietà che include l'ID di registrazione.
 > [AZURE.NOTE] Se lo schema dinamico è abilitato nel servizio mobile, una nuova colonna **handle** verrà aggiunta automaticamente alla tabella **TodoItem** quando viene inserito un nuovo elemento contenente tale proprietà.

8. Scaricare e decomprimere [Mobile Services Android SDK], aprire la cartella **notifications** copiare il file **notifications-1.0.1.jar** nella cartella  *libs* del progetto Eclipse, quindi aggiornare la cartella  *libs*.
 > [AZURE.NOTE] È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.

9.  Aprire il file  *ToDoItemActivity.java*, quindi aggiungere l'istruzione import seguente:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Aggiungere la variabile privata seguente alla classe, dove _`<PROJECT_NUMBER>`_ è il numero di progetto assegnato da Google all'app nella procedura precedente:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. Nel metodo **onCreate** prima della creazione di istanze di MobileServiceClient, aggiungere il codice seguente per la registrazione del gestore delle notifiche per il dispositivo:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Aggiungere la riga di codice seguente al metodo **addItem** prima dell'inserimento dell'elemento nella tabella:

		item.setHandle(MyHandler.getHandle());

	Questo codice consente di impostare la proprietà  `handle` dell'elemento sull'ID di registrazione del dispositivo.

13. In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo  `src`), quindi scegliere **New** e fare clic su **Class**.

14. In **Nome** digitare  `MyHandler`, in **Superclass** digitare  `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Fine**

	![][6]

	Verrà creata la nuova classe MyHandler.

15. Aggiungere le istruzioni import seguenti:

		import android.content.Context;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

16. Aggiungere il codice seguente:

		@com.google.gson.annotations.SerializedName("handle")
		private static String mHandle;
	
		public static String getHandle() {
			return mHandle;
		}
	
		public static final void setHandle(String handle) {
			mHandle = handle;
		}
	

17. Sostituire l'override del metodo **onRegistered** con il codice seguente:

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

L'app è ora aggiornata per il supporto delle notifiche push.


##<a id="update-scripts"></a>Aggiornare gli script insert registrati nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**. 

   	![][21]

2. In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**.
   
  	![][22]

   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   	Verrà registrato un nuovo script insert, che usa l'[oggetto gcm] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert. 

##<a id="test"></a>Testare le notifiche push nell'app

> [AZURE.NOTE] Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1. Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e fare clic su **OK**.

	![][23]

  	Il progetto farà quindi riferimento a Google APIs.

2. Da **Window** selezionare **Android Virtual Device Manager**, selezionare il dispositivo e fare clic su **Edit**.

	![][24]

3. Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

   	![][25]

	AVD sarà quindi configurato per l'uso di Google APIs.

4. Scegliere **Run** dal menu **Run** per avviare l'app.

5. Digitare testo significativo nell'app, ad esempio _Nuova attività Servizi mobili_ e quindi fare clic sul pulsante **Add**.

  	![][26]

6. Una casella nera di notifica verrà visualizzata brevemente nella parte inferiore dello schermo. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

L'esercitazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo usato da GCM viene fornito al servizio mobile dal client nella richiesta. Nell'esercitazione successiva, [Notifiche push per utenti di app], sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento. 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google Cloud Console]: https://cloud.google.com/console
[Google APIs]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portale di provisioning di Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[configurazione di Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Riferimento a un progetto di libreria]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-android
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
[Notifiche push per utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-android
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-android

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Concetti relativi a Servizi mobili per Android]: /it-it/develop/mobile/how-to-guides/work-with-android-client-library/
[oggetto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645



<!--HONumber=42-->
