
<properties urlDisplayName="Get Started" pageTitle="Introduzione ad Hub di notifica di Azure" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="yuaxu" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS.
In questa esercitazione si creerà un'app per iOS vuota che riceve notifiche push tramite il Servizio di notifica Push di Apple. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato]
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
4. [Configurare l'hub di notifica]
5. [Connettere l'app all'hub di notifica]
6. [Inviare notifiche dal back-end]

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

+ [Mobile Services SDK per iOS]
+ [XCode 4.5][Install Xcode]
+ Dispositivo con iOS 5.0 o versione successiva
+ Iscrizione a iOS Developer Program

   <div class="dev-callout"><b>Nota</b>
   <p>Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.</p>
   </div>

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per iOS.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

##<a name="configure-hub"></a>Configurare l'hub di notifica

1. Nell'Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato **Certificati**. dell'app di guida introduttiva. Fare clic su **Esporta**, assegnare un nome al file, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][26]

  Annotare il nome del file e il percorso del certificato esportato.

>[WACOM.NOTE] In questa esercitazione viene creato un file QuickStart.p12. Il nome e il percorso del file potrebbero essere diversi.

2. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

3. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub di notifica** e infine **Creazione rapida**.

   	![][27]

4. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**..

   	![][28]

5. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configura** nella parte superiore.￼￼

   	![][29]

6. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][210]

7. Selezionare la scheda **Configura** nella parte superiore e quindi fare clic su **Carica** per le impostazioni di notifica di Apple. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera usare il servizio push **Produzione** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

   	![][211]

8. Fare clic sulla scheda **Dashboard** nella parte superiore e quindi su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

   	![][212]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

##<a name="connecting-app"></a>Connettere l'app all'hub di notifica

1. In XCode creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

   	![][31]

2. In **Targets** fare clic sul nome del progetto, espandere **Code Signing Identity**, quindi in **Debug** selezionare il profilo di identità di firma del codice. Inoltre, se si usa una versione più recente di XCode, cambiare l'impostazione di **Levels** da **Basic** a **All** e impostare la voce **Provisioning Profile** sul profilo di provisioning.

   	![][32]

3. Scaricare Azure Mobile SDK per iOS. Aprire il file ZIP e trascinare la cartella WindowsAzureMessaging.framework nella cartella Framework nel progetto XCode. Selezionare **Copy items in destination group's folder**, quindi fare clic su **OK**.

   	![][33]

4. Nel file AppDelegate.h aggiungere la direttiva import seguente:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. Nel file AppDelegate.m aggiungere il codice seguente nel metodo `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6. Nello stesso file aggiungere il metodo seguente:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(Facoltativo)* Sempre nello stesso file, aggiungere il metodo seguente per visualizzare un avviso **UIAlert** se si riceve una notifica mentre l'app è attiva:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. Eseguire l'app sul dispositivo.

##<a name="send"></a>Inviare notifiche dal back-end

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio relativo all'invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([Back-end .NET](/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/) | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)).  Per un esempio relativo all'invio di notifiche tramite le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java](/it-it/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/it-it/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Dal menu **File** di Visual Studio selezionare **Nuovo** e quindi **Progetto...**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine su **OK**.  

   	![][20]

	Verrà creato un nuovo progetto di applicazione console.

2. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

	Verrà visualizzata la Console Gestione pacchetti.

6. Nella finestra della console eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus

	Verrà aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto WindowsAzure.ServiceBus NuGet</a>.

5. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

6. Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurare l'hub di notifica".

	>[WACOM.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

5. Aggiungere quindi le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Premere F5 per eseguire l'applicazione console.

	Si dovrebbe ricevere un avviso sul dispositivo. Se si usa una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali]. di Apple.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica].

<!-- Anchors. -->
[Generare la richiesta di firma del certificato]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Configurare l'hub di notifica]: #configure-hub
[Connettere l'app all'hub di notifica]: #connecting-app
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services SDK per iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning di iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usare Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
[Usare Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet

[Guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
