<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per iOS. In questa esercitazione si utilizzerà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Questo argomento riguarda i servizi mobili *esistenti* che *non sono stati ancora aggiornati* per l'integrazione di Hub di notifica. Quando si crea un *nuovo* servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push][Introduzione alle notifiche push].
>
> Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. *È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile*. Una volta eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push][Introduzione alle notifiche push].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Generazione della richiesta di firma del certificato][Generazione della richiesta di firma del certificato]
2.  [Registrazione dell'app e abilitazione delle notifiche push][Registrazione dell'app e abilitazione delle notifiche push]
3.  [Creazione di un profilo di provisioning per l'app][Creazione di un profilo di provisioning per l'app]
4.  [Configurazione di Servizi mobili][Configurazione di Servizi mobili]
5.  [Aggiunta di notifiche push all'app][Aggiunta di notifiche push all'app]
6.  [Aggiornamento degli script per l'invio di notifiche push][Aggiornamento degli script per l'invio di notifiche push]
7.  [Inserimento di dati per la ricezione di notifiche][Inserimento di dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario disporre di:

-   [Mobile Services SDK per iOS][Mobile Services SDK per iOS]
-   [Xcode 4.5][Xcode 4.5]
-   Dispositivo con iOS 5.0 o versione successiva
-   Iscrizione a iOS Developer Program

> [WACOM.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Configurazione di Servizi mobili per l'invio di richieste push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Aggiungere notifiche push all'app

1.  In Xcode aprire il file QSAppDelegate.h e aggiungere la proprietà seguente sotto la proprietà \***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Se lo schema dinamico è abilitato nel servizio mobile, una nuova colonna 'deviceToken' verrà aggiunta automaticamente alla tabella **TodoItem** quando viene inserito un nuovo elemento contenente tale proprietà.

2.  In QSAppDelegate.m sostituire il seguente metodo del gestore all'interno dell'implementazione:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  In QSTodoListViewController.m importare il file QSAppDelegate.h in modo da poter utilizzare il delegato per ottenere il token del dispositivo:

        #import "QSAppDelegate.h"

7.  In QSTodoListViewController.m modificare l'azione **(IBAction)onAdd** individuando la riga seguente:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Sostituirlo con il codice seguente:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

L'app è ora aggiornata per il supporto delle notifiche push.

## Aggiornamento dello script insert registrato nel portale di gestione

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][0]

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][1]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

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

    Verrà registrato un nuovo script insert, che usa l'[oggetto apns][oggetto apns] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert.

    > [WACOM.NOTE] Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.

## Esecuzione del test delle notifiche push nell'app

1.  Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

    ![][2]

    > [WACOM.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2.  Digitare testo significativo nell'app, ad esempio *Nuova attività di Servizi mobili*, quindi fare clic sull'icona con il segno più (**+**).

    ![][3]

3.  Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

    ![][4]

4.  Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente avviso popup.

    ![][5]

L'esercitazione è stata completata.

## Passaggi successivi

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo utilizzato da APNS viene fornito al servizio mobile dal client nella richiesta. Nell'esercitazione successiva, [Notifiche push per utenti di app][Notifiche push per utenti di app], sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.



  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Generazione della richiesta di firma del certificato]: #certificates
  [Registrazione dell'app e abilitazione delle notifiche push]: #register
  [Creazione di un profilo di provisioning per l'app]: #profile
  [Configurazione di Servizi mobili]: #configure
  [Aggiunta di notifiche push all'app]: #add-push
  [Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Notifiche push per utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
