<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>


# Introduzione alle notifiche push in Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android">Android</a>
<!-- <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come usare Servizi mobili di Azure per inviare notifiche push a un'app per iOS. In questa esercitazione si userà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.


In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generazione della richiesta di firma del certificato][Generazione della richiesta di firma del certificato]
2. [Registrazione dell'app e abilitazione delle notifiche push][Registrazione dell'app e abilitazione delle notifiche push]
3. [Creazione di un profilo di provisioning per l'app][Creazione di un profilo di provisioning per l'app]
4. [Scaricare il servizio mobile in locale][Scaricare il servizio mobile in locale]
5. [Testare il servizio mobile][Testare il servizio mobile]
6. [Aggiornare il server per l'invio di notifiche push][Aggiornare il server per l'invio di notifiche push]
7. [Pubblicare il servizio mobile in Azure][Pubblicare il servizio mobile in Azure]
8. [Aggiunta di notifiche push all'app][Aggiunta di notifiche push all'app]
9. [Aggiornamento degli script per l'invio di notifiche push][Aggiornamento degli script per l'invio di notifiche push]
10. [Abilitare le notifiche push per test locali][Abilitare le notifiche push per test locali]
11. [Eseguire il test dell'app sul servizio mobile pubblicato][Eseguire il test dell'app sul servizio mobile pubblicato]

Per completare questa esercitazione, è necessario disporre di:

+ [Mobile Services SDK per iOS][Mobile Services SDK per iOS]
+ [XCode 4,5][XCode 4,5]
+ Dispositivo con iOS 6,0 o versione successiva
+ Iscrizione a iOS Developer Program

   > [WACOM.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].


[WACOM.INCLUDE [Abilitare le notifiche push per Apple][Abilitare le notifiche push per Apple]]


## Configurazione di Servizi mobili per l'invio di richieste push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

<h2><a name="download-the-service"></a><span class="short-header">Scaricare il servizio</span>Scaricare il servizio mobile nel computer locale</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][mobile-services-ios-download-service-locally]]

<h2><a name="test-the-service"></a><span class="short-header">Eseguire il test del servizio</span>Eseguire il test del servizio mobile</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <a id="update-server"></a>Aggiornare il server per l'invio di notifiche push

1. In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiungere l'istruzione `using` seguente all'inizio del file:


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

    Questo codice invierà una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Logs** del servizio mobile nel portale di gestione.


<h2><a name="publish-the-service"></a><span class="short-header">Pubblicare il servizio</span>Pubblicare il servizio mobile in Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Aggiungere notifiche push all'app

1. In QSAppDelegate.m sostituire il seguente metodo del gestore all'interno dell'implementazione:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
			client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

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

5. In QSTodoListViewController.m importare il file QSAppDelegate.h in modo da poter usare il delegato per ottenere il token del dispositivo:

        #import "QSAppDelegate.h"

6.  In QSTodoListViewController.m modificare l'azione **(IBAction)onAdd** individuando la riga seguente:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 
 
   Sostituirlo con il codice seguente:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

   This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

   > [WACOM.NOTE] You must add this code before to the call to the **addItem** method.

L'app è ora aggiornata per il supporto delle notifiche push.

## <a id="local-testing"></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## Esecuzione del test delle notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

    ![][]

    > [WACOM.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Digitare testo significativo nell'app, ad esempio *Nuova attività di Servizi mobili*, quindi fare clic sull'icona con il segno più (**+**).

    ![][1]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

    ![][2]

4.  Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente push.

    ![][3]

L'esercitazione è stata completata.

## Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di usare Servizi mobili e Hub di notifica per inviare notifiche push. In seguito, è consigliabile eseguire l'esercitazione [Inviare notifiche push agli utenti autenticati][Inviare notifiche push agli utenti autenticati], che illustra come usare i tag per inviare notifiche push da un servizio mobile a un solo utente autenticato.

+ [Send push notifications to authenticated users]     
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  

+ [Send broadcast notifications to subscribers]     
	<br/>Learn how users can register and receive push notifications for categories they're interested in.  

+ [Send template-based notifications to subscribers]     
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. 


Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione ai dati][Introduzione ai dati] 
  <br/>Altre informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili.

* [Introduzione all'autenticazione][Introduzione all'autenticazione] 
  <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

* [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
  <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

<!-- Anchors.  -->

[Windows Store C\#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows Store C#"
[Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows Store JavaScript"
[Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
[iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
[Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
[Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET backend"
[Back-end JavaScript]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/ "JavaScript backend"
[Generazione della richiesta di firma del certificato]: #certificates
[Registrazione dell'app e abilitazione delle notifiche push]: #register
[Creazione di un profilo di provisioning per l'app]: #profile
[Scaricare il servizio mobile in locale]: #download-the-service-locally
[Testare il servizio mobile]: #test-the-service
[Aggiornare il server per l'invio di notifiche push]: #update-server
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service
[Aggiunta di notifiche push all'app]: #add-push
[Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
[Abilitare le notifiche push per test locali]: #local-testing
[Eseguire il test dell'app sul servizio mobile pubblicato]: #test-app
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Abilitare le notifiche push per Apple]: ../includes/enable-apple-push-notifications.md
[mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
[mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
[]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[Inviare notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
[Send push notifications to authenticated users]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
[Send broadcast notifications to subscribers]: /it-it/documentation/articles/notification-hubs-ios-send-breaking-news/
[Send template-based notifications to subscribers]: /it-it/documentation/articles/notification-hubs-ios-send-localized-breaking-news/