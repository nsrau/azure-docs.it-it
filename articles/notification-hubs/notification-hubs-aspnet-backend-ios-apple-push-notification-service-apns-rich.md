---
title: Push avanzato degli hub di notifica di Azure
description: Informazioni su come inviare notifiche push complesse a un'app per iOS da Azure. Gli esempi di codice sono scritti in Objective-C e C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090364"
---
# <a name="azure-notification-hubs-rich-push"></a>Push avanzato degli hub di notifica di Azure

## <a name="overview"></a>Panoramica

Per offrire agli utenti contenuti immediati avanzati, in un'applicazione potrebbe essere necessario effettuare il push non solo del testo normale. Queste notifiche promuovono le interazioni degli utenti e presentano contenuti come URL, suoni, immagini/buoni e altro ancora. Questa esercitazione si basa sull'esercitazione [Notify Users](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e Mostra come inviare notifiche push che incorporano payload (ad esempio, immagini).

Questa esercitazione è compatibile con iOS 7 e 8.

  ![Tre schermate: una schermata dell'app con un pulsante Invia push, una schermata Start in un dispositivo e un logo Windows con un pulsante indietro.][IOS1]

In generale:

1. Il back-end dell'app:
   * Archivia il payload completo (in questo caso, image) nel database back-end o nell'archivio locale.
   * Invia l'ID di questa notifica avanzata al dispositivo.
2. L'app sul dispositivo:
   * Contatta il back-end richiedendo il payload completo con l'ID ricevuto.
   * Invia notifiche agli utenti sul dispositivo quando il recupero dei dati è completo e visualizza immediatamente il payload quando gli utenti toccano per saperne di più.

## <a name="webapi-project"></a>Progetto WebAPI

1. In Visual Studio aprire il progetto **AppBackend** creato nell'esercitazione sulla [creazione di notifiche per gli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Acquisire un'immagine con cui inviare una notifica agli utenti e inserirla in una cartella **img** nella directory del progetto.
3. In Esplora soluzioni fare clic su **Mostra tutti i file**, quindi fare clic con il pulsante destro del mouse sulla cartella e selezionare **Includi nel progetto**.
4. Con l'immagine selezionata, modificare la relativa **azione di compilazione** nella finestra **Proprietà** in **risorsa incorporata**.

    ![Screenshot del Esplora soluzioni. Il file di immagine è selezionato e nel riquadro proprietà la risorsa incorporata è elencata come azione di compilazione.][IOS2]
5. In `Notifications.cs` aggiungere l'istruzione seguente `using` :

    ```csharp
    using System.Reflection;
    ```

6. Sostituire la classe `Notifications` con il codice seguente. Assicurarsi di sostituire i segnaposto con le credenziali dell'hub di notifica e il nome del file di immagine:

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. In `NotificationsController.cs` , ridefinire `NotificationsController` con il codice seguente. In questo modo viene inviato al dispositivo un ID notifica avanzata invisibile all'utente e viene consentito il recupero lato client dell'immagine:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. A questo punto, ridistribuire l'app in un sito Web di Azure per renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.
9. Selezionare **sito Web di Azure** come destinazione di pubblicazione. Accedere con l'account Azure e selezionare un sito Web esistente o nuovo e prendere nota della proprietà URL di **destinazione** nella scheda **connessione** . Si fa riferimento a questo URL come *endpoint back-end* più avanti in questa esercitazione. Selezionare **Pubblica**.

## <a name="modify-the-ios-project"></a>Modifica del progetto iOS

Ora che il back-end dell'app è stato modificato per inviare solo l' *ID* di una notifica, modificare l'app iOS in modo che gestisca tale ID e recuperare il messaggio RTF dal back-end:

1. Aprire il progetto iOS e abilitare le notifiche remote andando alla destinazione dell'app principale nella sezione **Targets** .
2. Selezionare **funzionalità**, Abilita **modalità in background**e selezionare la casella di controllo **notifiche remote** .

    ![Screenshot del progetto iOS che mostra la schermata delle funzionalità. La modalità in background è attivata e la casella di controllo notifiche remote è selezionata.][IOS3]
3. Aprire `Main.storyboard` e assicurarsi di disporre di un controller di visualizzazione (denominato Home View Controller in questa esercitazione) dall'esercitazione per la [notifica degli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Aggiungere un **controller di spostamento** allo storyboard e trascinare il controller della visualizzazione Home per impostarlo come **visualizzazione radice** della navigazione. Verificare che il **controller di visualizzazione iniziale** nel controllo attributi sia selezionato solo per il controller di spostamento.
5. Aggiungere un **controller di visualizzazione** allo storyboard e aggiungere una **visualizzazione immagine**. Questa sarà la pagina visualizzata dagli utenti quando faranno clic sulla notifica per avere altre informazioni. L'aspetto dello storyboard dovrebbe essere simile al seguente:

    ![Screenshot di uno storyboard. Sono visibili tre schermate dell'app: una visualizzazione di navigazione, una visualizzazione Home e una visualizzazione immagine.][IOS4]
6. Fare clic sul **controller di visualizzazione Home** nello storyboard e verificare che abbia **HomeViewController** come **classe personalizzata** e **ID storyboard** in Identity Inspector.
7. Eseguire la stessa operazione per il controller di visualizzazione immagine, come **imageViewController**.
8. Quindi, creare una nuova classe del controller di visualizzazione denominata **imageViewController** per gestire l'interfaccia utente appena creata.
9. In **imageViewController. h**aggiungere il codice seguente alle dichiarazioni di interfaccia del controller. Trascinare tenendo premuto CTRL dalla visualizzazione immagine dello storyboard a queste proprietà per collegarle:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. In `imageViewController.m` aggiungere quanto segue alla fine di `viewDidload`:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. In `AppDelegate.m` importare il controller immagine creato:

    ```objc
    #import "imageViewController.h"
    ```

12. Aggiungere una sezione dell'interfaccia con la seguente dichiarazione:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. In `AppDelegate` verificare che l'app esegua la registrazione per le notifiche automatiche in `application: didFinishLaunchingWithOptions`:

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Sostituire l'implementazione seguente per per `application:didRegisterForRemoteNotificationsWithDeviceToken` prendere in considerazione le modifiche dell'interfaccia utente dello storyboard:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. Aggiungere quindi i metodi seguenti ad `AppDelegate.m` per recuperare l'immagine dall'endpoint e inviare una notifica locale al termine del recupero. Assicurarsi di sostituire il segnaposto `{backend endpoint}` con l'endpoint back-end:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. Gestire la notifica locale precedente aprendo il controller di visualizzazione immagini `AppDelegate.m` con i metodi seguenti:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).
2. Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password con lo stesso valore per l'autenticazione e fare clic su **Log In**.
3. Fare clic su **Send push** (Invia push). Verrà visualizzato un avviso nell'app. Se si fa clic su **More** (Altro), si passerà all'immagine che si è scelto di includere nel back-end dell'app.
4. È anche possibile fare clic su **Send push** e premere subito il pulsante home del dispositivo. Dopo alcuni istanti si riceverà una notifica push. Se la si tocca o si fa clic su More, si passerà all'app e al contenuto avanzato dell'immagine.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
