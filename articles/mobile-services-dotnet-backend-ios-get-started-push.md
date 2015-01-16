<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="Introduzione alle notifiche push (iOS) | Mobile Developer Center" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app per iOS." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />


# Aggiungere notifiche push all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app per iOS. In questa esercitazione si userà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.


In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato]
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
4. [Scaricare il servizio mobile in locale]
5. [Testare il servizio mobile]
6. [Aggiornare il server per l'invio di notifiche push](#update-server)
7. [Pubblicare il servizio mobile in Azure]
8. [Aggiungere notifiche push all'app]
9. [Aggiornare gli script per l'invio di notifiche push]
10. [Abilitare le notifiche push per test locali](#local-testing)
11. [Eseguire il test dell'app sul servizio mobile pubblicato]

Per completare questa esercitazione, è necessario disporre di:

+ [Mobile Services SDK per iOS]
+ [XCode 4.5][Installare Xcode]
+ Dispositivo con iOS 6.0 o versione successiva
+ Iscrizione a iOS Developer Program

   > [WACOM.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].


[WACOM.INCLUDE [Abilitare le notifiche push Apple](../includes/enable-apple-push-notifications.md)]


## Configurare Servizi mobili per l'invio di richieste push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

<h2><a name="download-the-service"></a>Scaricare il servizio nel computer locale</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

<h2><a name="test-the-service"></a>Testare il servizio mobile</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Aggiornare il server per l'invio di notifiche push

1. In Esplora soluzioni di Visual Studio espandere la cartella **Controller**s nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiungere l'istruzione `using` seguente all'inizio del file:


		using System;
		using System.Collections.Generic;

2. Aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Questo codice invierà una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Log** del servizio mobile nel portale di gestione.


<h2><a name="publish-the-service"></a>Pubblicare il servizio mobile in Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Aggiungere notifiche push all'app

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

3. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione. Assicurarsi di copiare i valori relativi all'URL e alla chiave applicazione del servizio mobile e sostituirli ai segnaposto:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

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

   > [WACOM.NOTE] È necessario aggiungere questo codice prima di chiamare il metodo <strong>addItem</strong>.

L'app è ora aggiornata per il supporto delle notifiche push.

##<a id="local-testing"></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

  	![][23]

    > [WACOM.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Digitare testo significativo nell'app, ad esempio _Nuova attività di Servizi mobili_, quindi fare clic sull'icona con il segno più (**+**).

  	![][24]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

  	![][25]

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente push.

  	![][26]

L'esercitazione è stata completata.

## Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di usare Servizi mobili e Hub di notifica per inviare notifiche push. In seguito, è consigliabile eseguire l'esercitazione [Inviare notifiche push agli utenti autenticati], che illustra come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

<!--+ [Send push notifications to authenticated users]
	<br/>Informazioni su come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

+ [Inviare notifiche ai sottoscrittori]
	<br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

+ [Inviare notifiche basate su modelli ai sottoscrittori]
	<br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.
-->
Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

* [Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni dell'Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Informazioni sulla risoluzione dei problemi e sul debug di soluzioni dell'Hub di notifica. 

<!-- Anchors.  -->
[Generare la richiesta di firma del certificato]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Configurare Servizi mobili]: #configure
[Aggiornare gli script per l'invio di notifiche push]: #update-scripts
[Aggiungere notifiche push all'app]: #add-push
[Inserire dati per la ricezione di notifiche]: #test
[Eseguire il test dell'app sul servizio mobile pubblicato]: #test-app
[Passaggi successivi]:#next-steps
[Scaricare il servizio mobile in locale]: #download-the-service-locally
[Testare il servizio mobile]: #test-the-service
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service

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

<!-- URLs. -->
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning di iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servizio notifiche push Apple (APN)]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
[Inviare notifiche ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-ios-send-breaking-news/
[Inviare notifiche basate su modelli ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-ios-send-localized-breaking-news/
