<properties 
	pageTitle="Aggiungere notifiche push all'app Xamarin iOS tramite il servizio app di Azure" 
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app Xamarin iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="yuaxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="02/22/2015" 
	ms.author="yuaxu"/>

# Aggiungere notifiche push all'app Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Questo argomento descrive come usare il servizio app di Azure per inviare notifiche push a un'app Xamarin iOS 8. In questa esercitazione si userà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto [Introduzione alle app mobili del servizio app]. Al termine dell'esercitazione, il back-end mobile invierà una notifica push ogni volta che viene inserito un record.

Per completare questa esercitazione, è necessario disporre di:

+ Dispositivo iOS 8
+ Iscrizione a iOS Developer Program
+ [Xamarin.iOS Studio]
+ [Componente Servizi mobili di Azure]

   > [AZURE.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Il servizio APNS usa i certificati per autenticare l'app mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nell'app mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service].

## <a name="certificates"></a>Generare il file di richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo **strumento Accesso Portachiavi**.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

    ![][5]

3. Immettere l'**Indirizzo e-mail utente**, digitare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

    ![][6]

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

    ![][7]
  
    Ricordare il percorso scelto.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register"></a>Registrare l'app per le notifiche push

Per poter inviare notifiche push a un dispositivo iOS dall'app mobile, è necessario registrare l'applicazione con Apple ed eseguire la registrazione per abilitare le notifiche push. 

1. Se l'app non è ancora stata registrata, passare al portale di provisioning iOS</a> su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per creare un ID per l'app.
    
    ![][102]

2. Digitare un nome per l'app in **Description**, immettere un valore univoco per **Bundle Identifier**, e ricordarlo, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**. In questo esempio viene usato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app. 

    ![][103]
   
    Verrà generato l'ID dell'app e all'utente verrà chiesto di **inviare** le informazioni. Fare clic su **Invia**.
   
    ![][104] 
   
    Dopo aver fatto clic su **Submit**, verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.
   
    ![][105]    

3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga. 

    ![][106]
   
    Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID dell'app. Fare clic sul pulsante **Impostazioni**.
   
    ![][107] 
   
4. Scorrere fino alla fine della schermata e fare clic su **Create Certificate** nella sezione **Development Push SSL Certificate**.

    ![][108] 

    Verrà visualizzato l'assistente "Add iOS Certificate".
   
    Nota: In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato nell'app mobile.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR in precedenza, quindi fare clic su **Generate**. 

    ![][110]
  
6. Al termine della creazione del certificato nel portale, fare clic sul pulsante **Download** e quindi su **Done**.
 
    ![][111]  

    Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download. 

    ![][9] 

    Nota: Per impostazione predefinita, il nome del file scaricato di un certificato di sviluppo è <strong>aps_development.cer</strong>.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

    Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

    ![][10]

    Nota: Il nome del certificato potrebbe essere diverso, ma verrà preceduto da <strong>Apple Development iOS Push Notification Services:</strong>.

Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e per caricarlo nell'app mobile per abilitare l'autenticazione con il servizio APN.

## <a name="profile"></a>Creare un profilo di provisioning per l'app
 
1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**.

    ![][112]

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

3. Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

    ![][113]

4. Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

    ![][114]

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.
  
    ![][115]

6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

    ![][116]

    Verrà creato un nuovo profilo di provisioning.

    ![][117]

## <a name="configure-appServiceMobile"></a>Configurare il back-end mobile del servizio app per l'invio di richieste push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Aggiornare il server per l'invio di notifiche push

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**.

2. Cercare **Microsoft.Azure.NotificationHubs** e fare clic su **Installa** per tutti i progetti nella soluzione.

3. In Esplora soluzioni di Visual Studio espandere la cartella **Controller** nel progetto di back-end mobile. Aprire TodoItemController.cs. Aggiungere le istruzioni `using` seguenti all'inizio del file:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Aggiungere il frammento seguente al metodo `PostTodoItem` dopo la chiamata **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Questo codice indicherà all'hub di notifica associato a questa app mobile di inviare una notifica push dopo l'inserimento di un elemento ToDo.


<h2><a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Configurare l'applicazione Xamarin.iOS

1. In Xamarin.Studio aprire **Info.plist** e aggiornare il valore in **Bundle Identifier** con l'ID creato in precedenza.

    ![][121]

2. Scorrere verso il basso fino a **Background Modes** e selezionare la casella **Enable Background Modes** e la casella **Remote notifications**. 

    ![][122]

3. Fare doppio clic sul progetto nel riquadro delle soluzioni per aprire le **opzioni del progetto**.

4.  Scegliere **iOS Bundle Signing** in **Build** e selezionare i valori corrispondenti per **Identity** e **Provisioning profile** appena configurati per questo progetto. 

    ![][120]

    Per effetto di questa operazione, il progetto Xamarin userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].

## <a name="add-push"></a>Aggiungere notifiche push all'app

1. In **QSTodoService**, eseguire l'override della dichiarazione di client esistente in modo che **AppDelegate** possa acquisire il client mobile:
        
        public MobileServiceClient client { get; private set; }

2. In **AppDelegate**, eseguire l'override dell'evento **FinishedLaunching**: 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Nello stesso file eseguire l'override dell'evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Quindi, sovrascrivere l'evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L'app è ora aggiornata per il supporto delle notifiche push.


<h2><a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Testare le notifiche push nell'app

1. Premere il pulsante **Run** per creare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.
	
	> [AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app digitare un'attività e fare clic sull'icona con il segno PIÙ (**+**).

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per ignorarla.

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzata una notifica push.

L'esercitazione è stata completata.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Introduzione alle app mobili del servizio app]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

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

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning per iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[provisioning del dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331303


<!--HONumber=49-->