<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

# Introduzione alle notifiche push in Servizi mobili

 
<div  class="dev-center-tutorial-selector sublanding">
	<a  href="/en-us/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a  href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

 In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un\'app per Android. L\'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite il servizio Google Cloud Messaging (GCM). Al termine dell\'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.


> [WACOM.NOTE] In questo argomento viene illustrato come abilitare le
> notifiche push utilizzando il supporto delle versioni precedenti
> offerto da Servizi mobili. Grazie all\'integrazione tra Hub di
> notifica di Azure e Servizi mobili, è possibile inviare a milioni di
> dispositivi notifiche push basate su modello e idonee a piattaforme
> diverse. Per impostazione predefinita, l\'utilizzo di Hub di notifica
> non è abilitato e non è attualmente disponibile supporto di Hub di
> notifica per Android nelle librerie di Servizi mobili. È tuttavia
> possibile inviare notifiche push dal proprio servizio mobile
> utilizzando le librerie di Hub di notifica. Per ulteriori
> informazioni, vedere [Introduzione ad Hub di
> notifica](/en-us/documentation/articles/notification-hubs-android-get-started/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

* [Abilitazione di Google Cloud Messaging](#register)
* [Configurazione di Servizi mobili](#configure)
* [Aggiunta di notifiche push all\'app](#add-push)
* [Aggiornamento degli script per l\'invio di notifiche
  push](#update-scripts)
* [Inserimento di dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

* [Mobile Services Android SDK][1]
* Un account Google attivo

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-android).

## <a  id="register" ></a>Abilitazione di Google Cloud Messaging

[WACOM.INCLUDE [Abilitazione di GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave dell\'API verrà utilizzato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

## <a  id="configure" ></a>Configurazione di Servizi mobili per l'invio di richieste push

1.  Accedere al [portale di gestione di Azure][2], fare clic su **Mobile Services** e quindi sull'app.
    
	![][18]

2.  Fare clic sulla scheda **Push**, immettere il valore **API Key** ottenuto da GCM nella procedura precedente, quindi fare clic su **Save**.
    
	![][19]

Il servizio mobile è ora configurato per l'utilizzo con GCM per l'invio di notifiche push.

## <a  id="add-push" ></a>Aggiunta di notifiche push all\'app

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Aggiunta di Play
Services](../includes/mobile-services-add-Google-play-services.md)]

### Aggiungere codice

1.  Aprire il file del progetto **AndroidManifest.xml**. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti. Se è necessario impostare un valore inferiore a 16 perché si utilizza un dispositivo meno recente, per determinare il livello minimo su cui è possibile impostare tale valore, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK][3]. Impostare la proprietà in modo appropriato.

2.  Verificare che nell\'elemento `uses-sdk` il valore di **targetSdkVersion** sia impostato sul numero di una piattaforma SDK installata (Passaggio 1). È consigliabile impostare tale valore sulla versione più recente disponibile.

3.  Il tag **uses-sdk** potrebbe avere un aspetto analogo al seguente, in base alle scelte effettuate nei passaggi precedenti:
    
         <uses -sdk
             android:minSdkVersion="17"
             android:targetSdkVersion="19" />

4.  Nel codice per i due passaggi successivi sostituire *`**my_app_package**`* con il nome del pacchetto dell'app per il progetto, ovvero il valore dell'attributo `package` del tag `manifest`.

5.  Aggiungere le nuove autorizzazioni seguenti dopo l'elemento `uses-permission`:
    
         <permission  android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses -permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses -permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses -permission android:name="android.permission.GET_ACCOUNTS" />
         <uses -permission android:name="android.permission.WAKE_LOCK" />

6.  Aggiungere il codice seguente dopo il tag di apertura `application`:
    
         <receiver  android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent -filter>
                 <action  android:name="com.google.android.c2dm.intent.RECEIVE" ></a>
                 <category  android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

7.  Aprire il file ToDoItem.java, quindi aggiungere il codice seguente alla classe **TodoItem**:
    
             	@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
    
    Tale codice consente di creare una nuova proprietà che include l'ID di registrazione.
    
    <div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>Se lo schema dinamico è abilitato nel servizio mobile, una nuova colonna <b>handle</b> verrà aggiunta
	automaticamente alla tabella <b>TodoItem</b> quando viene inserito un nuovo elemento contenente tale proprietà.</p>


    </div>


8.  Scaricare e decomprimere [Mobile Services Android SDK][1], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.
    
    <div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.
	</p>

    </div>


9.  Aprire il file *ToDoItemActivity.java*, quindi aggiungere l'istruzione import seguente:
    
        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Aggiungere la variabile privata seguente alla classe, dove *`<PROJECT _NUMBER>`* è il numero di progetto assegnato da Google all\'app nella procedura precedente:
    
        public static final String SENDER_ID = "<PROJECT _NUMBER>";

11. Nel metodo **onCreate**, prima della creazione di istanze di MobileServiceClient, aggiungere il codice seguente per la registrazione del gestore delle notifiche per il dispositivo:
    
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Aggiungere la riga di codice seguente al metodo **addItem**, prima dell'inserimento dell'elemento nella tabella:
    
        item.setHandle(MyHandler.getHandle());
    
    Questo codice consente di impostare la proprietà `handle` dell'articolo nell'ID di registrazione del dispositivo.

13. In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

14. In **Name** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Finish**
    
    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)
    
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

17. Sostituire l\'override del metodo **onRegistered** con il codice seguente:
    
        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
        		
            setHandle(gcmRegistrationId);?}?

L'app è ora aggiornata per il supporto delle notifiche push.

## <a  id="update-scripts" ></a>Aggiornamento dello script insert registrato nel portale di gestione

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.
    
	![][21]

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

1.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:
    
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

	Verrà registrato un nuovo script insert, che utilizza l'oggetto [gcm object] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert. 

## <a  id="test" ></a>Esecuzione del test delle notifiche push nell\'app

 
<div  class="dev-callout"><b>Nota</b>
	<p>Quando si esegue l'app nell'emulatore, assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.</p>
</div>

 1.  Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e infine fare clic su **OK**.
    
	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

 	Il progetto farà quindi riferimento a Google APIs.

1.  Da **Window** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Edit**.
    
	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  Selezionare **Google APIs** in **Target**, quindi fare clic su OK.
    
	![][25]
    
    AVD sarà quindi configurato per l\'utilizzo di Google APIs.

3.  Dal menu **Run** scegliere **Run** per avviare l\'app.

4.  Digitare testo significativo nell\'app, ad esempio *Nuova attività di Servizi mobili*, quindi fare clic sul pulsante **Add**.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  Una casella nera di notifica verrà visualizzata brevemente nella parte inferiore dello schermo.

	![](./media/mobile-services-android-get-started-push/mobile-push-icon.png) 

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

	![][27]-->

L'esercitazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo utilizzato da GCM viene fornito al servizio mobile dal client nella richiesta. Nell\'esercitazione successiva, [Notifiche push per utenti di app](/en-us/develop/mobile/tutorials/push-notifications-to-users-android), sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.

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



[1]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[2]: https://manage.windowsazure.com/
[3]: http://go.microsoft.com/fwlink/?LinkId=389801

[Google Cloud Console]: https://cloud.google.com/console
[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services android conceptual]: /en-us/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645