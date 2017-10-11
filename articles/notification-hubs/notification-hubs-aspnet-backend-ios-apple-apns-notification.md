---
title: Uso di Hub di notifica di Azure per inviare notifiche agli utenti per iOS con back-end .NET
description: Informazioni su come inviare notifiche push agli utenti in Azure. Gli esempi di codice sono scritti in Objective-C e nell'API .NET per il back-end.
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Uso di Hub di notifica di Azure per inviare notifiche agli utenti per iOS con back-end .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Panoramica
Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end di API Web ASP.NET, come illustrato nell'argomento [Registering from your app backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) (Registrazione dal back-end dell'app).

> [!NOTE]
> In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). È inoltre propedeutica all'esercitazione [Push sicuro (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
> Se si desidera usare le app per dispositivi mobili come servizio back-end, vedere [Introduzione alle app per dispositivi mobili con notifiche push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificare l'app per iOS
1. Aprire l'app di visualizzazione Pagina singola creata nell'esercitazione [Introduzione ad Hub di notifica (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .
   
   > [!NOTE]
   > In questa sezione si presuppone che il progetto sia configurato con un nome di organizzazione vuoto. In caso contrario, sarà necessario anteporre il nome dell'organizzazione a tutti i nomi di classi.
   > 
   > 
2. In Main.storyboard aggiungere i componenti illustrati nella schermata seguente dalla libreria di oggetti.
   
    ![][1]
   
   * **Nome utente**: oggetto UITextField con testo segnaposto, *Enter Username*, immediatamente sotto l'etichetta per l'invio dei risultati e limitato dai margini sinistro e destro.
   * **Password**: oggetto UITextField con testo segnaposto, *Enter Password*, immediatamente sotto il campo di testo del nome utente e limitato dai margini sinistro e destro. Selezionare l'opzione **Secure Text Entry** in Attribute Inspector sotto *Return Key*.
   * **Accesso**: oggetto UIButton con etichetta immediatamente sotto il campo di testo della password; deselezionare l'opzione **Abilitato** in Attributes Inspector sotto *Control-Content*.
   * **WNS**: etichetta e opzione per consentire l'invio della notifica al servizio di notifica Windows se è stato configurato nell'hub. Vedere l'esercitazione [Introduzione a Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: etichetta e opzione per consentire l'invio della notifica a Google Cloud Messaging se è stato configurato nell'hub. Vedere l'esercitazione [Introduzione ad Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: etichetta e opzione per consentire l'invio della notifica al servizio di notifica della piattaforma di Apple.
   * **Nome utente del destinatario**: oggetto UITextField con testo segnaposto, *Recipient username tag*, immediatamente sotto l'etichetta GCM e limitato dai margini sinistro e destro.

    Alcuni componenti sono stati aggiunti nell'esercitazione [Introduzione ad Hub di notifica (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) esercitazione.

1. **CTRL** dai componenti nella visualizzazione a ViewController.h e aggiungere questi nuovi outlet.
   
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
2. In ViewController.h aggiungere l'elemento `#define` seguente sotto le istruzioni di importazione. Sostituire il segnaposto *<Enter Your Backend Endpoint>\>* con l'URL di destinazione usato per distribuire il back-end dell'app nella sezione precedente. Ad esempio, *http://you_backend.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. Nel progetto creare una nuova **classe Cocoa Touch** denominata **RegisterClient** da usare come interfaccia con il back-end ASP.NET creato. Creare la classe che eredita da `NSObject`. Aggiungere quindi il codice seguente in RegisterClient.h.
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. In RegisterClient.m aggiornare la sezione `@interface` :
   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
5. Sostituire la sezione `@implementation` in RegisterClient.m con il codice seguente.

        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end

    Il codice riportato sopra implementa la logica illustrata nell'articolo di istruzioni [Registrazione dal back-end dell'app](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) usando NSURLSession per eseguire chiamate REST al back-end dell'app e NSUserDefaults per archiviare in locale l'ID registrazione restituito dall'hub di notifica.

    Si noti che per il corretto funzionamento di questa classe è necessario che sia impostata la relativa proprietà **authorizationHeader** . Questa proprietà viene impostata tramite la classe **ViewController** dopo l'accesso.

1. In ViewController.h aggiungere un'istruzione `#import` per RegisterClient.h. Aggiungere quindi una dichiarazione per il token del dispositivo e fare riferimento a un'istanza di `RegisterClient` nella sezione `@interface`:
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. In ViewController.m aggiungere una dichiarazione di metodo privato nella sezione `@interface` :
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> Il frammento seguente non è uno schema di autenticazione sicuro. È consigliabile sostituire l'implementazione di **createAndSetAuthenticationHeaderWithUsername:AndPassword:** con il meccanismo di autenticazione specifico che genera un token di autenticazione che deve essere usato dalla classe client di registrazione, ad esempio OAuth o Active Directory.
> 
> 

1. Aggiungere quindi nella sezione `@implementation` di ViewController.m il codice seguente che aggiunge l'implementazione per l'impostazione del token del dispositivo e dell'intestazione di autenticazione.
   
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
   
    Si noti come impostando il token del dispositivo viene abilitato il pulsante di accesso. Questo dipende dal fatto che, nell'ambito dell'azione di accesso, il controller di visualizzazione esegue la registrazione per le notifiche push con il back-end dell'app. L'azione di accesso deve quindi essere accessibile solo dopo che il token del dispositivo è stato correttamente configurato. È possibile separare l'accesso dalla registrazione push, purché la prima azione avvenga prima della seconda.
2. In ViewController.m usare i frammenti di codice seguenti per implementare il metodo di azione per il pulsante **Log In** e un metodo per inviare il messaggio di notifica con il back-end ASP.NET.
   
       - (IBAction) LogInAction: mittente (id) {/ / crea l'intestazione di autenticazione e impostate nel registro client NSString * username = self. UsernameField.text;   Password NSString * = self. PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController * selfie = self;   [self.registerClient registerWithDeviceToken:self.deviceToken tag: nil andCompletion:^(NSError* error) {Se (! errore) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = YES;               [self MessageBox:@"Success" message:@"Registered correttamente!"];});}}];}

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag            Message:(NSString*)message {    NSURLSession* session = [NSURLSession        sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil        delegateQueue:nil];

            Passare il tag pns e il nome utente come parametri con l'URL di REST per il back-end ASP.NET NSURL * requestURL = [NSURL URLWithString: [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @", BACKEND_ENDPOINT, pns, usernameTag]];

            Richiesta NSMutableURLRequest * = [NSMutableURLRequest requestWithURL:requestURL];    [richiesta setHTTPMethod:@"POST"];

            Ottenere la simulazione authenticationheader dal client register NSString * authorizationHeaderValue = [NSString stringWithFormat:@"Basic % @", self.registerClient.authenticationHeader];    [richiesta setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            Aggiungere il corpo del messaggio di notifica [richiesta setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];    [richiesta setHTTPBody: [messaggi dataUsingEncoding:NSUTF8StringEncoding]];

            Eseguire l'API REST di inviare una notifica su dataTask NSURLSessionDataTask back-end ASP.NET * = [completionHandler:^(NSData *data, NSURLResponse *response, NSError  *di dataTaskWithRequest:request sessione Errore) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) risposta.        Se (errore | | HttpResponse. StatusCode! = 200) {NSString* stato = [NSString stringWithFormat:@"Error stato per % @: % d\nError: %@\n", pns, HttpResponse. StatusCode, errore];            dispatch_async(dispatch_get_main_queue(), ^ {/ / aggiungere testo, in quanto tutte le chiamate PNS 3 potrebbero inoltre disporre delle informazioni alla visualizzazione [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];            });            NSLog(status);        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask resume]; }


1. Aggiornare l'azione per il pulsante **Send Notification** per usare il back-end ASP.NET e inviare a qualsiasi PNS abilitato da un'opzione.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }


        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }



1. Nella funzione **ViewDidLoad**aggiungere quanto segue per creare l'istanza di RegisterClient e impostare il delegato per i campi di testo.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. In **AppDelegate.m** rimuovere tutto il contenuto del metodo **application:didRegisterForPushNotificationWithDeviceToken:** e sostituirlo con il seguente per assicurarsi che il controller di visualizzazione contenga il token del dispositivo più recente recuperato dagli APN:
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Infine in **AppDelegate.m**verificare che sia presente il metodo seguente:
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Testare l'applicazione
1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).
2. Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere entrambi lo stesso valore di stringa. Quindi fare clic su **Log In**.
   
    ![][2]
3. Verrà visualizzata una finestra popup che informa che la registrazione è stata completata. Fare clic su **OK**.
   
    ![][3]
4. Nel campo di testo **Recipient username tag* (Tag nome utente destinatario) immettere il tag del nome utente usato con la registrazione da un altro dispositivo.
5. Immettere un messaggio di notifica e fare clic su **Send Notification**.  Solo i dispositivi che hanno una registrazione con il tag del nome utente del destinatario riceveranno il messaggio di notifica.  Viene inviato solo a tali utenti.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
