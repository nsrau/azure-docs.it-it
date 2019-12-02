---
title: Inviare notifiche push alle app iOS con Hub di notifica di Azure | Microsoft Docs
description: In questa esercitazione si apprende come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione iOS.
services: notification-hubs
documentationcenter: ios
keywords: notifica push,notifiche push,notifiche push ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407370"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Esercitazione: Inviare notifiche push alle app iOS con Hub di notifica di Azure

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In questa esercitazione vengono usati Hub di notifica di Azure per inviare notifiche push a un'applicazione iOS. Si crea un'app iOS vuota che riceve notifiche push tramite [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Generare il file della richiesta di firma del certificato
> * Richiedere le notifiche push all'app
> * Creare un profilo di provisioning per l'app
> * Configurare l'hub di notifica per l'invio di notifiche push di iOS
> * Connettere l'app iOS all'hub di notifica
> * Inviare notifiche push di prova
> * Verificare che l'app riceve le notifiche

Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free).
* [Framework di messaggistica di Windows Azure]
* Ultima versione di [Xcode]
* Un dispositivo compatibile con iOS versione 10 (o versioni successive)
* [Apple Developer Program](https://developer.apple.com/programs/) .
  
  > [!NOTE]
  > Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo fisico con iOS, iPhone o iPad, anziché in un simulatore iOS.
  
Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS all'hub di notifica

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .

    ![Xcode - Single View Application][8]

2. Quando si configurano le opzioni per il nuovo progetto, assicurarsi di usare lo stesso **nome prodotto** e lo stesso **identificatore organizzazione** usati quando è stato impostato l'identificatore del bundle nel portale Apple Developer.

3. In Project Navigator (Esplorazione progetto) selezionare il nome del progetto in **Targets** (Destinazioni), quindi selezionare la scheda **Signing & Capabilities** (Firma e funzionalità). Assicurarsi di selezionare il **Team** appropriato per l'account Apple Developer. XCode recupererà automaticamente il profilo di provisioning creato in precedenza in base all'identificatore del bundle.

    Se il profilo di provisioning creato in Xcode non viene visualizzato, provare ad aggiornare i profili per l'identità di firma. Fare clic su **Xcode** nella barra dei menu, fare clic su **Preferences** (Preferenze), selezionare la scheda **Account**, fare clic sul pulsante **View Details** (Visualizza dettagli), selezionare la propria identità di firma e infine fare clic sul pulsante di aggiornamento nell'angolo in basso a destra.

    ![Xcode - Profilo di provisioning][9]

4. Nella scheda **Signing & Capabilities** (Firma e funzionalità) selezionare **+ Capability** (+ Funzionalità).  Fare doppio clic su **Push Notifications**(Notifiche push) per abilitarla.

    ![Xcode - Funzionalità push][12]

5. Aggiungere i moduli dell'SDK di Hub di notifica di Azure.

   È possibile integrare l'SDK di Hub di notifica di Azure nell'app usando [Cocoapods](https://cocoapods.org) o aggiungendo manualmente i file binari al progetto.

   - Integrazione tramite Cocoapods

     Aggiungere le dipendenze seguenti al `podfile` per includere l'SDK di Hub di notifica di Azure nell'app.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Eseguire `pod install` per installare il pod appena definito e aprire il file `.xcworkspace`.

     > [!NOTE]
     > Se viene visualizzato un errore, ad esempio **[!] Impossibile trovare una specifica per AzureNotificationHubs-iOS** durante l'esecuzione di `pod install`, eseguire `pod repo update` per ottenere i pod più recenti dal repository CocoaPods, quindi eseguire `pod install`.

   - Integrazione tramite Carthage

     Aggiungere le dipendenze seguenti al `Cartfile` per includere l'SDK di Hub di notifica di Azure nell'app.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Quindi aggiornare e compilare le dipendenze:

     ```shell
     $ carthage update
     ```

     Per altre informazioni sull'uso di Carthage, vedere il [repository GitHub di Carthage](https://github.com/Carthage/Carthage).

   - Integrazione tramite copia dei file binari nel progetto

     1. Scaricare il framework dell'[SDK di Hub di notifica di Azure](https://github.com/Azure/azure-notificationhubs-ios/releases) fornito come file ZIP e decomprimerlo.

     2. In Xcode fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione **Add Files to** (Aggiungi file a) per aggiungere la cartella **WindowsAzureMessaging.framework** al progetto Xcode. Selezionare **Options** (Opzioni) e assicurarsi che sia selezionato **Copy items if needed** (Copia elementi se necessario), quindi fare clic su **Add** (Aggiungi).

        ![Decomprimere Azure SDK][10]

6. Aggiungere un nuovo file di intestazione denominato **Constants.h** al progetto. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **New File...** (Nuovo file). Selezionare quindi **Header File** (File di intestazione). Questo file include le costanti per l'hub di notifica. Quindi selezionare **Avanti**. Assegnare al file il nome **Constants. h**.

7. Aggiungere il seguente codice al file Constants.h:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Aggiungere il file di implementazione per Constants.h. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **New File...** (Nuovo file). Selezionare **Objective-C File** (File Objective-C) e quindi fare clic su **Next** (Avanti). Assegnare al file il nome **Constants.m**.

    ![Aggiungere il file con estensione .m](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Aprire il file **Constants.m** e sostituirne il contenuto con il codice seguente. Sostituire i segnaposto dei valori letterali stringa `NotificationHubConnectionString` e `NotificationHubConnectionString` rispettivamente con il nome dell'hub e la firma **DefaultListenSharedAccessSignature** ottenuti in precedenza dal portale:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Aprire il file **AppDelegate.h** del progetto e sostituirne il contenuto con il codice seguente:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. Nel file **AppDelegate.m** del progetto aggiungere le istruzioni `import` seguenti:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Nel file **AppDelegate.m** aggiungere anche la riga di codice seguente nel metodo `didFinishLaunchingWithOptions` in base alla versione di iOS. Questo codice registra l'handle di dispositivo nel servizio APNS:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Nello stesso file **AppDelegate.m**, sostituire tutto il codice dopo `didFinishLaunchingWithOptions` con il codice seguente:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Questo codice si connette all'hub di notifica usando le informazioni di connessione specificate in **Constants.h**. Fornisce il token del dispositivo all'hub di notifica in modo che quest'ultimo possa inviare notifiche.

### <a name="notificationdetailviewcontroller"></a>NotificationDetailViewController

1. Analogamente alle istruzioni precedenti, aggiungere un altro file di intestazione denominato **NotificationDetailViewController.h**. Sostituire il contenuto del file di intestazione con il codice seguente:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Aggiungere il file di implementazione **NotificationDetailViewController.m**. Sostituire il contenuto del file con il codice seguente, che implementa i metodi `UIViewController`:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>ViewController

1. Nel file **ViewController.h** del progetto aggiungere le istruzioni `import` seguenti:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. In **ViewController.h** aggiungere inoltre le dichiarazioni di proprietà seguenti dopo la dichiarazione `@interface`:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. Nel file di implementazione **ViewController.m** del progetto, sostituire il contenuto del file con il codice seguente:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Per verificare che non vi siano errori, compilare ed eseguire l'app nel dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova

È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificare che l'app riceve le notifiche push

Per testare le notifiche push in iOS, è necessario distribuire l'app in un dispositivo fisico iOS. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'app e verificare che la registrazione riesca e quindi scegliere **OK**.

    ![Test di registrazione notifica push per app iOS][33]

2. Ora viene inviata una notifica push di prova dal [portale di Azure], come illustrato nella sezione precedente.

3. La notifica push viene inviata a tutti i dispositivi registrati per la ricezione di notifiche dall'hub di notifica specifico.

    ![Test di ricezione notifica push per app iOS][35]

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio le notifiche push sono state trasmesse a tutti i dispositivi iOS registrati. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi iOS specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Framework di messaggistica di Windows Azure]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portale di Azure]: https://portal.azure.com
