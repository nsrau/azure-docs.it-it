<properties
	pageTitle="Push avanzato degli hub di notifica di Azure"
	description="Informazioni su come inviare notifiche push complesse a un'app per iOS da Azure. Gli esempi di codice sono scritti in Objective-C e C#."
	documentationCenter="ios"
	services="notification-hubs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

#Push avanzato degli hub di notifica di Azure


##Panoramica

Per offrire agli utenti contenuti immediati avanzati, in un'applicazione potrebbe essere necessario effettuare il push non solo del testo normale. Queste notifiche promuovono le interazioni con l'utente e presentano contenuti come URL, immagini/buoni e altro ancora. Questa esercitazione è basata sull'argomento [Usare Hub di notifica per inviare notifiche agli utenti](notification-hubs-aspnet-backend-ios-notify-users.md) e spiega come inviare notifiche push che incorporano payload (ad esempio, immagini).


Questa esercitazione è compatibile con iOS 7 e 8.

  ![][IOS1]

In generale:

1. Il back-end dell'app:
    - Archivia il payload avanzato (in questo caso, un'immagine) nell'archivio locale/database back-end
    - Invia l'ID di questa notifica avanzata al dispositivo
2. L'app sul dispositivo:
    - Contatta il back-end che richiede il payload avanzato con l'ID ricevuto
    - Invia agli utenti notifiche sul dispositivo al termine del recupero dei dati e mostra immediatamente il payload quando gli utenti toccano per avere altre informazioni


## Progetto WebAPI

1. In Visual Studio aprire il progetto **AppBackend** creato nell'esercitazione sulla [creazione di notifiche per gli utenti](notification-hubs-aspnet-backend-ios-notify-users.md).
2. Acquisire un'immagine con cui inviare una notifica agli utenti e inserirla in una cartella **img** nella directory del progetto.
3. In Esplora soluzioni fare clic su **Mostra tutti i file**, quindi fare clic con il pulsante destro del mouse sulla cartella e selezionare **Includi nel progetto**.
4. Con l'immagine selezionata, nella finestra Proprietà impostare Azione di compilazione su **Risorsa incorporata**.

    ![][IOS2]

5. In **Notifications.cs** aggiungere la seguente istruzione using:

        using System.Reflection;

6. Aggiornare l'intera classe **Notifications** con il seguente codice. Assicurarsi di sostituire i segnaposto con le credenziali dell'hub di notifica e il nome file di immagine.

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
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Management Portal
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

> [AZURE.NOTE](optional) Fare riferimento a [Come incorporare le risorse e accedervi usando Visual C#](http://support.microsoft.com/kb/319292) per altre informazioni su come aggiungere e ottenere le risorse del progetto.

7. In **NotificationsController.cs** ridefinire **NotificationsController** con i frammenti di codice riportati di seguito. Questo codice invia un ID notifica avanzata automatica iniziale al dispositivo e consente il recupero lato client dell'immagine:

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
            var aboutUser = "{"aps": {"content-available": 1, "sound":""}, "richId": "" + richNotificationInTheBackend.Id.ToString() + "",  "richMessage": "" + richNotificationInTheBackend.Message + "", "richType": "" + richNotificationInTheBackend.RichType + ""}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. L'app verrà ora nuovamente distribuita in un sito Web di Azure in modo da renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.

9. Selezionare un sito Web Azure come destinazione di pubblicazione. Accedere con l'account di Azure e selezionare un sito Web nuovo o esistente, quindi prendere nota della proprietà **URL di destinazione** nella scheda **Connessione**. Si farà riferimento a quest'URL come *endpoint back-end* più avanti in questa esercitazione. Fare clic su **Pubblica**.

## Modifica del progetto iOS

Dopo avere modificato il back-end del app per poter inviare solo l'*id* di una notifica, si modificherà l'app per iOS per poter gestire tale ID e recuperare il messaggio avanzato dal back-end.

1. Aprire il progetto iOS e abilitare le notifiche remote andando alla destinazione dell'app principale nella sezione **Targets**.

2. Fare clic su **Capabilities**, attivare **Background Modes** e selezionare la casella di controllo **Remote Notifications**.

    ![][IOS3]

3. Passare a **Main.storyboard** e verificare che sia presente un elemento View Controller (in questa esercitazione, Home View Controller) creato nell'esercitazione relativa all’[invio di notifiche per gli utenti](notification-hubs-aspnet-backend-ios-notify-users.md).

4. Aggiungere un elemento **Navigation Controller** allo storyboard e trascinarlo tenendo premuto CTRL sull'elemento Home View Controller per impostarlo come **visualizzazione radice** della navigazione. Assicurarsi che **Is Initial View Controller** in Attributes inspector sia selezionato solo per Navigation Controller.

5. Aggiungere un elemento **View Controller** allo storyboard e aggiungere un elemento **Image View**. Questa sarà la pagina visualizzata dagli utenti quando faranno clic sulla notifica per avere altre informazioni. L'aspetto dello storyboard dovrebbe essere simile al seguente:

    ![][IOS4]

6. Fare clic sull'elemento **Home View Controller** nello storyboard e verificare che abbia **homeViewController** come **Custom Class** e **Storyboard ID** in Identity inspector.

7. Fare lo stesso per Image View Controller come **imageViewController**.

8. Creare quindi una nuova classe View Controller denominata **imageViewController** per gestire l'interfaccia utente appena creata.

9. In **imageViewController.h**, aggiungere quanto segue alle dichiarazioni di interfaccia del controller. Trascinare tenendo premuto CTRL dalla visualizzazione immagine dello storyboard a queste proprietà per collegarle:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. In **imageViewController.m** aggiungere quanto segue alla fine di **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. In **AppDelegate.m** importare il controller immagine creato:

        #import "imageViewController.h"

12. Aggiungere una sezione dell'interfaccia con la seguente dichiarazione:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. In **AppDelegate** assicurarsi che l'app esegua la registrazione per le notifiche automatiche in **application: didFinishLaunchingWithOptions**:

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

14. Sostituire la seguente implementazione per **application:didRegisterForRemoteNotificationsWithDeviceToken** per tenere in considerazione le modifiche apportate all'interfaccia utente dello storyboard:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Aggiungere quindi i metodi seguenti ad **AppDelegate.m** per recuperare l'immagine dall'endpoint e inviare una notifica locale al termine del recupero. Assicurarsi di sostituire il segnaposto `{backend endpoint}` con l'endpoint back-end:

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

16. Gestire la notifica locale precedente aprendo il controller di visualizzazione immagine in **AppDelegate.m** con i seguenti metodi:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
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

## Eseguire l'applicazione

1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).

2. Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password con lo stesso valore per l'autenticazione e fare clic su **Log In**.

3. Fare clic su **Send push**. Verrà visualizzato un avviso nell'app. Se si fa clic su **More**, si passerà all'immagine che si è scelto di includere nel back-end dell'app.

4. È anche possibile fare clic su **Send push** e premere subito il pulsante home del dispositivo. Dopo alcuni istanti si riceverà una notifica push. Se la si tocca o si fa clic su More, si passerà all'app e al contenuto avanzato dell'immagine.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png

<!---HONumber=September15_HO1-->