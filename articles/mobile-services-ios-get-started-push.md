<properties 
	pageTitle="Introduzione alle notifiche push (iOS) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app per iOS (push legacy)." 
	services="mobile-services" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Aggiungere notifiche push all'app di Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per iOS. In questa esercitazione si userà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.


>[AZURE.NOTE]L'argomento supporta <em>servizi mobili</em> esistenti <em>che non sono stati ancora aggiornati</em> per l'uso dell'integrazione di Hub di notifica. Quando si crea un <em>nuovo</em> servizio mobile, questa funzionalità integrata è abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).
>
>Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. <em>È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile</em>. Dopo aver eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato]
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
3. [Configurare Servizi mobili]
4. [Aggiungere notifiche push all'app]
5. [Aggiornare gli script per l'invio di notifiche push]
6. [Inserire dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ [Mobile Services SDK per iOS]
+ [Xcode 4.5][Installare Xcode]
+ Dispositivo con iOS 5.0 o versione successiva
+ Iscrizione a iOS Developer Program

   > [AZURE.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Configurare Servizi mobili per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Aggiungere notifiche push all'app

1. In Xcode aprire il file QSAppDelegate.h e aggiungere la proprietà seguente sotto la proprietà ***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [AZURE.NOTE] Se lo schema dinamico è abilitato nel servizio mobile, una nuova colonna  'deviceToken' verrà aggiunta automaticamente alla tabella **TodoItem** quando viene inserito un nuovo elemento contenente tale proprietà.

2. In QSAppDelegate.m sostituire il seguente metodo del gestore all'interno dell'implementazione:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. In QSAppDelegate.m aggiungere il seguente metodo del gestore all'interno dell'implementazione:  

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

5. In QSTodoListViewController.m importare il file QSAppDelegate.h in modo da poter usare il delegato per ottenere il token del dispositivo:

        #import "QSAppDelegate.h"

6. In QSTodoListViewController.m modificare l'azione **(IBAction)onAdd** individuando la riga seguente:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

   Replace this with the following code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	Verrà aggiunto un riferimento a **QSAppDelegate** per ottenere il token del dispositivo e quindi il payload della richiesta verrà modificato per includere quel token del dispositivo.

   	> [AZURE.NOTE] È necessario aggiungere questo codice prima della chiamata al metodo <strong>addItem</strong>.

L'app è ora aggiornata per il supporto delle notifiche push.

## Aggiornare gli script insert registrati nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][21]

2. In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**.

  	![][22]

   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

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

   	Verrà registrato un nuovo script insert, che usa l'[oggetto apns] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta di inserimento.


   	> [AZURE.NOTE] Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.

## Testare le notifiche push nell'app

1. Fare clic sul pulsante **Esegui** per compilare il progetto e avviare l'app in un dispositivo con iOS e quindi fare clic su **OK** per accettare le notifiche push.

  	![][23]

    > [AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app digitare testo significativo, come _Nuova attività Servizi mobili_ e quindi fare clic sull'icona con il segno più (**+**).

  	![][24]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per ignorarla.

  	![][25]

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente avviso popup.

  	![][26]

L'esercitazione è stata completata.

## Passaggi successivi

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo usato da APNS viene fornito al servizio mobile dal client nella richiesta. Nell'esercitazione successiva, [Notifiche push per utenti di app], sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.

<!-- Anchors. -->
[Generare la richiesta di firma del certificato]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Configurare Servizi mobili]: #configure
[Aggiornare gli script per l'invio di notifiche push]: #update-scripts
[Aggiungere notifiche push all'app]: #add-push
[Inserire dati per la ricezione di notifiche]: #test
[Passaggi successivi]:#next-steps

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
[Portale di provisioning per iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333


<!--HONumber=42-->
