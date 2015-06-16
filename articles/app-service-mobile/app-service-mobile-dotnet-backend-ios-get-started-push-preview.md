<properties 
	pageTitle="Aggiungere notifiche push all'app iOS tramite il servizio app di Azure" 
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app iOS." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Aggiungere notifiche push all'app iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

Questo argomento descrive come usare il servizio app di Azure per inviare notifiche push a un'app iOS con il servizio di notifiche push di Apple (Apple Push Notification Service, APNS). Al termine dell'esercitazione, il back-end .NET invierà una notifica push all'app di guida introduttiva ToDo ogni volta che viene inserito un record. L'app è compatibile con iOS 8 e versioni precedenti.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato iOS]
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
4. [Configurare il back-end mobile per l'invio di richieste push]
5. [Aggiornamento del server per l'invio di notifiche push](#update-server)
6. [Pubblicare il back-end mobile in Azure]
7. [Aggiungere notifiche push all'app]
8. [Test dell'app]

Per completare questa esercitazione, è necessario disporre di:

+ [SDK iOS delle app per dispositivi mobili di Azure]
+ [Pacchetto Nuget degli hub di notifica di Azure]
+ [XCode 6.0][Install Xcode]
+ Dispositivo con iOS 6,0 o versione successiva
+ Iscrizione a iOS Developer Program

   >[AZURE.NOTE]Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sulla guida introduttiva delle app per dispositivi mobili del servizio app. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione alle app per dispositivi mobili del servizio app].

[AZURE.INCLUDE [Abilitare le notifiche push per Apple](../../includes/enable-apple-push-notifications.md)]

## Configurare il servizio per app per dispositivi mobili per l'invio di richieste push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Aggiornare il server per l'invio di notifiche push

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**.

2. Cercare **Microsoft.Azure.NotificationHubs** e fare clic su **Installa** per tutti i progetti nella soluzione.

3. In Esplora soluzioni di Visual Studio espandere la cartella **Controller** nel progetto di back-end mobile. Open TodoItemController.cs. Aggiungere le istruzioni `using` seguenti all'inizio del file:

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Aggiungere il frammento seguente al metodo `PostTodoItem` dopo la chiamata **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    Questo codice indicherà all'hub di notifica associato a questa app mobile di inviare una notifica push dopo l'inserimento di un elemento ToDo.


## <a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Aggiungere notifiche push all'app
1. Scaricare l'SDK per client delle app per dispositivi mobili del servizio app e aggiungervi un riferimento in xcode.

2. In **QSAppDelegate.m** aggiungere il codice seguente in **application:didFinishLaunchingWithOptions** per registrare il client con Apple Push Notification Service:

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. Nello stesso file aggiungere il metodo del gestore seguente all'interno dell'implementazione **QSAppDelegate**:

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Aggiungere quindi il metodo di gestione degli errori all'interno dell'implementazione:

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

L'app è ora aggiornata per il supporto delle notifiche push.

## Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

  	![][23]

    > [AZURE.NOTE]È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Digitare testo significativo nell'app, ad esempio _Nuova attività di Servizi mobili_ e quindi fare clic sull'icona con il segno più (**+**).

  	![][24]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

  	![][25]

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente push.

  	![][26]

L'esercitazione è stata completata.

<!-- Anchors.  -->
[Generare la richiesta di firma del certificato iOS]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Aggiungere notifiche push all'app]: #add-push
[Configurare il back-end mobile per l'invio di richieste push]: #configure
[Update the server to send push notifications]: #update-server
[Pubblicare il back-end mobile in Azure]: #publish-mobile-service
[Test dell'app]: #test-the-service

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push1-ios.png
[24]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push2-ios.png
[25]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push3-ios.png
[26]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK iOS delle app per dispositivi mobili di Azure]: https://go.microsoft.com/fwLink/?LinkID=529823
[Pacchetto Nuget degli hub di notifica di Azure]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54--> 