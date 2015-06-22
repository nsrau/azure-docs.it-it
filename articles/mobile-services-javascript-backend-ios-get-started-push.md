<properties 
	pageTitle="Aggiungere notifiche push all'app di Servizi mobili (iOS) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app per iOS." 
	services="mobile-services,notification-hubs" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="krisragh"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per iOS usando APNS (Apple Push Notification Service). Questa esercitazione consente di usare Hub di notifica di Azure per abilitare le notifiche push nel [progetto di guida introduttiva](http://azure.microsoft.commobile-services-ios-get-started.md/). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato](#certificates)
2. [Registrare l'app e abilitare le notifiche push](#register)
3. [Creare un profilo di provisioning per l'app](#profile)
4. [Configurare Servizi mobili](#configure)
5. [Aggiungere notifiche push all'app](#add-push)
6. [Aggiornare gli script per l'invio di notifiche push](#update-scripts)
7. [Inserire dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

+ [Mobile Services SDK per iOS]
+ [XCode 4.5][Install Xcode]
+ Dispositivo con iOS 6.0 o versione successiva
+ Iscrizione a iOS Developer Program

   >[AZURE.NOTE]Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente][Get started with data].


[AZURE.INCLUDE [Abilitare le notifiche push per Apple](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>Configurare Servizi mobili per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>Aggiornare lo script insert registrato nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

   	![][21]

2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

  	![][22]

   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	Verrà registrato un nuovo script insert, che usa l'[oggetto apns] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert.


   	> [AZURE.NOTE]Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica push.

## <a id="add-push"></a>Aggiungere notifiche push all'app

1. In QSAppDelegate.m inserire il frammento di codice seguente per importare Mobile Services SDK per iOS:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. In QSAppDelegate.m sostituire il seguente metodo del gestore all'interno dell'implementazione:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. In QSAppDelegate.m aggiungere il metodo gestore seguente all'interno dell'implementazione. Assicurarsi di copiare i valori relativi all'URL e alla chiave applicazione del servizio mobile e di sostituirli ai segnaposto:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

L'app è ora aggiornata per il supporto delle notifiche push.

## <a id="test"></a>Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

  	![][23]

    > [AZURE.NOTE]È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app digitare un testo significativo, ad esempio _Nuova attività di servizi mobili_ e quindi fare clic sull'icona con il segno più (**+**).

  	![][24]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

  	![][25]

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente push.

  	![][26]

L'esercitazione è stata completata.

## <a id="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per iOS di usare Servizi mobili e Hub di notifica per inviare notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

+ [Inviare notifiche push agli utenti autenticati] <br/>Informazioni sull'uso dei tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

+ [Inviare notifiche di trasmissione ai sottoscrittori] <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie a cui sono interessati. <!---
+ [Inviare notifiche basate su modelli ad altri sottoscrittori] <br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end. --> Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati] <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite servizi mobili.

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi tramite Servizi mobili.

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni di Hub di notifica.

* [Informazioni di riferimento sui concetti e sulle procedure di Servizi mobili con Objective-C] <br/>Altre informazioni su come usare Servizi mobili con Objective-C e iOS.

* [Informazioni di riferimento sugli script del server di Servizi mobili] <br/>Altre informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. -->


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
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
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

<!-- URLs.   -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introduzione a Servizi mobili]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Introduzione ai dati]: mobile-services-ios-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-ios-get-started-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293

[Inviare notifiche push agli utenti autenticati]: mobile-services-javascript-backend-ios-push-notifications-app-users.md

[Informazioni su Hub di notifica]: notification-hubs/notification-hubs-overview.md
[Inviare notifiche di trasmissione ai sottoscrittori]: notification-hubs/notification-hubs-ios-send-breaking-news.md
[Inviare notifiche basate su modelli ad altri sottoscrittori]: notification-hubs/notification-hubs-ios-send-localized-breaking-news.md

[Informazioni di riferimento sui concetti e sulle procedure di Servizi mobili con Objective-C]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->