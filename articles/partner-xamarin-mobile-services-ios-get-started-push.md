<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.iOS apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.iOS. In questa esercitazione si utilizzerà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

**Nota**

In questa esercitazione viene illustrata una procedura semplificata di invio di notifiche push basata sul collegamento del token del dispositivo per le notifiche push al record inserito. Per un quadro più completo sulle modalità di integrazione delle notifiche push nelle app effettive eseguire anche l'esercitazione successiva.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Generazione della richiesta di firma del certificato](#certificates)
2.  [Registrazione dell'app e abilitazione delle notifiche push](#register)
3.  [Creazione di un profilo di provisioning per l'app](#profile)
4.  [Configurazione di Servizi mobili](#configure)
5.  [Aggiunta di notifiche push all'app](#add-push)
6.  [Aggiornamento degli script per l'invio di notifiche push](#update-scripts)
7.  [Inserimento di dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

-   [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Dispositivo con iOS 5.0 o versione successiva
-   Iscrizione a iOS Developer Program
-   [Xamarin.iOS]
-   [Componente Servizi mobili di Azure](http://components.xamarin.com/view/azure-mobile-services/)

    **Nota**

    Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

Il servizio APNS utilizza i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Generazione del file della richiesta di firma del certificatoGenerazione del file della richiesta di firma del certificato
--------------------------------------------------------------------------------------------------------------------------

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene utilizzato da Apple per la generazione di un certificato firmato.

1.  Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2.  Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

1.  Fare clic su **Indirizzo e-mail utente**, digitare i valori di **Nome comune** e **Indirizzo e-mail CA**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)

	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

Registrazione dell'appRegistrazione dell'app per le notifiche push
------------------------------------------------------------------

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'ulteriore registrazione per abilitare le notifiche push.

1.  Se l'app non è ancora stata registrata, accedere al [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+**.

	![][102] 

2.  Digitare un nome per l'app in **Description**, inserire il valore *MobileServices.Quickstart* in **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**. In questo esempio viene utilizzato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app.

	![][103]
           
	Verrà generato l'ID dell'app e all'utente verrà chiesto di inviare le informazioni. Fare clic su **Submit**
           
	![][104] 
           
	Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.
           
	![][105]

    Nota: Se si sceglie di specificare un valore per **Bundle Identifier** diverso da *MobileServices.Quickstart*, è necessario aggiornare anche il valore dell'identificatore del bundle nel progetto Xcode.

3.  Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

 	![][106]
           
	Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID. Fare clic sul pulsante **Settings**.
           
	![][107] 

4.  Scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

	![][108] 

	Verrà visualizzato l'assistente "Add iOS Certificate".
           
	![][108] 

    Nota: In questa esercitazione viene utilizzato un certificato di sviluppo. La stessa procedura viene utilizzata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5.  Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

 Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    Nota: per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato <strong>aps_development.cer</strong>.

1.  Fare doppio clic sul certificato push scaricato **aps\_development.cer**.

	Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

	![][10]

    Nota: Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Notification Services:**.

Questo certificato verrà utilizzato in un secondo momento per generare un file con estensione p12 e verrà caricato in Servizi mobili per abilitare l'autenticazione con APNS.

Provisioning dell'appCreazione di un profilo di provisioning per l'app
----------------------------------------------------------------------

1.  Nel [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**

	![][112]

2.  Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**

	![][113]

3.  Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**

	![][114]

4.  Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**

	![][115]

5.  In **Devices** selezionare i dispositivi da utilizzare per il test e fare clic su **Continue**

	![][116]

6.  Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**

	![][117]

	Verrà creato un nuovo profilo di provisioning.

1.  In Xcode aprire Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi fare clic su **Refresh** nella parte inferiore del riquadro centrale.

	![][101]

2.  In **Targets** fare clic su **Quickstart**, espandere **Code Signing Identity**, quindi in **Debug** selezionare il nuovo profilo.

	![][17]

Per effetto di questa operazione, il progetto Xcode utilizzerà il nuovo profilo per la firma del codice. A questo punto, è necessario caricare il certificato in Servizi mobili.

## Configurazione del servizioConfigurazione di Servizi mobili per l'invio di richieste push

Dopo aver registrato l'app con APNS e aver configurato il progetto, è necessario configurare il servizio mobile in modo che si integri con APNS.

1.  In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, nominare il file QuickstartPusher, selezionare il formato **.p12**, quindi fare clic su **Salva**.

	![][28]

	Annotare il nome del file e il percorso del certificato esportato.

	<b>Nota</b>
	<p>In questa esercitazione viene creato un file QuickstartPusher.p12. Il nome e il percorso del file potrebbero essere diversi.</p>
    

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

	![][18]

2.  Fare clic sulla scheda **Push** e quindi su **Upload**.

	![][19]

	Verrà visualizzata la finestra di dialogo Upload Certificate.

3.  Fare clic su **File**, selezionare il file QuickstartPusher.p12 del certificato esportato, immettere la password in **Password**, verificare che in **Mode** sia selezionata la modalità corretta, fare clic sull'icona del segno di spunta e quindi su **Save**.

	![][20] 

    **Nota**

	In questa esercitazione vengono utilizzati certificati di sviluppo.

Il servizio mobile è ora configurato per funzionare con APNS.

##Aggiunta di notifiche pushAggiunta di notifiche push all'app

1.  In Xamarin.Studio aprire il file AppDelegate.cs e aggiungere la seguente proprietà:

         public string DeviceToken { get; set; }

2.  Aprire la classe **TodoItem** e aggiungere la seguente proprietà:

         [DataMember(Name = "deviceToken")]
         public string DeviceToken { get; set; }

    **Nota**

    Se lo schema dinamico è abilitato nel servizio mobile, una nuova colonna 'deviceToken' verrà aggiunta automaticamente alla tabella **TodoItem** quando viene inserito un nuovo elemento contenente tale proprietà.

3.  In **AppDelegate** eseguire l'override dell'evento **FinishedLaunching**:

         public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
         {
             UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                 UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
             UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

             return true;
         }

4.  In **AppDelegate** eseguire l'override dell'evento **RegisteredForRemoteNotifications**:

         public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
         {
             string trimmedDeviceToken = deviceToken.Description;
             if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
             {
                 trimmedDeviceToken = trimmedDeviceToken.Trim('<');
                 trimmedDeviceToken = trimmedDeviceToken.Trim('>');
             }
             DeviceToken = trimmedDeviceToken;
         }

5.  In **AppDelegate** eseguire l'override dell'evento **ReceivedRemoteNotification**:

         public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
         {
             Debug.WriteLine(userInfo.ToString());
             NSObject inAppMessage;

             bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

             if (success)
             {
                 var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                 alert.Show();
             }
         }

6.  In **TodoListViewController** modificare l'azione **OnAdd** in modo da ottenere il token del dispositivo archiviato in **AppDelegeate** e archiviarlo nell'elemento **TodoItem** aggiunto.

    string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

             var newItem = new TodoItem() 
             {
                 Text = itemText.Text, 
                 Complete = false,
                 DeviceToken = deviceToken
             };

L'app è ora aggiornata per il supporto delle notifiche push.

Aggiornamento dello script insertAggiornamento dello script insert registrato nel portale di gestione
-----------------------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

	![][21]

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

1.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

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

        Verrà registrato un nuovo script insert, che utilizza l'[oggetto apns] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert. 


	<div class="dev-callout"><b>Nota</b>

    Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.

 

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

1.  Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    <div class="dev-callout"><b>Nota</b>
    <p>È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.</p>
    </div>

1.  Digitare testo significativo nell'app, ad esempio *Nuova attività di Servizi mobili*, quindi fare clic sull'icona con il segno più (**+**).

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente avviso popup.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

L'esercitazione è stata completata.

Download dell'esempio completato
--------------------------------

Scaricare il [progetto di esempio completato](http://go.microsoft.com/fwlink/p/?LinkId=331303). Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

Passaggi successivi
-------------------

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo utilizzato da APNS viene fornito al servizio mobile dal client nella richiesta. Nell'esercitazione successiva, [Notifiche push per utenti di app](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios), sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png






[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303