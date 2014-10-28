<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Hub di notifica di Azure - Notifiche agli utenti

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/it-it/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push di facile utilizzo, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end di API Web ASP.NET, come illustrato nell'argomento [Registrazione dal back-end dell'app][Registrazione dal back-end dell'app]. Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione **Introduzione ad Hub di notifica**.

È inoltre propedeutica all'esercitazione **Push sicuro**. Dopo avere eseguito i passaggi indicati in questa esercitazione **Notifica agli utenti**, è possibile passare all'esercitazione **Push sicuro**, che illustra come modificare il codice di **Notifica agli utenti** per inviare una notifica push in modo sicuro.

> [AZURE.NOTE] In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (iOS)][Introduzione ad Hub di notifica (iOS)].

## Creare e configurare l'hub di notifica

Prima di iniziare questa esercitazione, è necessario creare un profilo di provisioning iOS e un certificato push di sviluppo, creare un hub di notifica di Azure e connetterlo all'applicazione. Seguire la procedura indicata in [Introduzione ad Hub di notifica (iOS)][Introduzione ad Hub di notifica (iOS)], in particolare le sezioni da 1 a 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Modificare l'app per iOS

1.  Attenersi alla procedura indicata in [Introduzione ad Hub di notifica (iOS)][Introduzione ad Hub di notifica (iOS)], sezioni da 1 a 5, per creare un'app di visualizzazione Single Page in grado di ricevere notifiche push da un hub di notifica.

> [AZURE.NOTE] In questa sezione si presuppone che il progetto sia stato configurato con un nome di organizzazione vuoto. In caso contrario, è necessario anteporre il nome dell'organizzazione a tutti i nomi di classi nel codice seguente.

1.  In Main.storyboard aggiungere i componenti seguenti dalla libreria di oggetti:

    -   Un UITextField con testo segnaposto **Username**
    -   Un UITextField con testo segnaposto **Password** e selezionare l'opzione **Secure Text Entry** nel gruppo di proprietà TextField nel riquadro di destra
    -   Un UIButton con etichetta **1. Log in**, quindi deselezionare l'opzione **Enabled** nel gruppo di proprietà Accessibility nel riquadro di destra
    -   Un UIButton con etichetta **2. Send Push** e deselezionare l'opzione **Enabled** nel gruppo di proprietà Accessibility nel riquadro di destra

    L'aspetto dello storyboard dovrebbe essere simile al seguente:

    ![][]

2.  Creare outlet per UITextField e UIButton nella porzione di interfaccia di ViewController.m

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Creare azioni per entrambi i pulsanti nell'implementazione di ViewController.m:
     - (IBAction)LogInAction:(id)sender { }
     - (IBAction)SendPushAction:(id)sender { }

4.  Prima di tutto si creerà una classe RegisterClient per l'interfaccia con il back-end. Creare una classe Objective-C denominata RegisterClient che eredita da NSObject. Aggiungere quindi il codice seguente nella sezione di interfaccia di RegisterClient.h:

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  In RegisterClient.m aggiungere la sezione di interfaccia seguente:

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Aggiungere quindi il codice seguente nella sezione di implementazione e sostituire il segnaposto *{backend endpoint}* con l'endpoint usato per distribuire il back-end dell'app nella sezione precedente.

        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
        NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

        - (instancetype)init
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
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
                        NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
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

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    Il codice riportato sopra implementa la logica illustrata nell'articolo di istruzioni [Registrazione dal back-end dell'app][Registrazione dal back-end dell'app] usando NSURLSession per eseguire chiamate REST al back-end dell'app e NSUserDefaults per archiviare in locale l'ID registrazione restituito dall'hub di notifica.

    Si noti che per il corretto funzionamento di questa classe è necessario che sia impostata la relativa proprietà **authorizationHeader**. Questa proprietà viene impostata tramite la classe **ViewController** dopo l'accesso.

7.  In ViewController.h aggiungere la proprietà seguente, che conterrà il token del dispositivo:

        @property (strong, nonatomic) NSData* deviceToken;

8.  In ViewController.m impostare la classe ViewController come UITextFieldDelegate, aggiungere una proprietà per fare riferimento a un'istanza di RegisterClient e aggiungere una dichiarazione di metodo privato. La sezione di interfaccia dovrebbe risultare:

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    Il metodo privato verrà usato per creare l'intestazione Authorization per eseguire l'autenticazione di base con il back-end dell'app.

> [AZURE.NOTE] Non si tratta di uno schema di autenticazione sicuro; è consigliabile sostituire l'implementazione di **createAndSetAuthenticationHeaderWithUsername:AndPassword:** con il meccanismo di autenticazione specifico che genera un token di autenticazione che deve essere usato dalla classe client di registrazione, ad esempio OAuth o Active Directory.

1.  Aggiungere quindi il codice seguente nella sezione di implementazione di ViewController.m:

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    Si noti come impostando il token del dispositivo viene abilitato il pulsante di accesso. Questo dipende dal fatto che, nell'ambito dell'azione di accesso, il controller di visualizzazione esegue la registrazione per le notifiche push con il back-end dell'app. Per questo motivo è preferibile impedire all'utente di selezionare il pulsante per l'accesso prima che il token del dispositivo sia stato configurato correttamente. Nell'app è possibile separare l'accesso della registrazione, purché la prima azione avvenga prima della seconda.

2.  In ViewController.m aggiungere una costante per l'endpoint back-end e specificare le implementazioni dei metodi di azione per gli oggetti UIButton. Ricordarsi di sostituire il segnaposto con l'endpoint back-end usato nella sezione precedente.

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        selfie.SendPushButton.enabled = YES;

                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
                                              @"Registered successfully!" delegate:nil cancelButtonTitle:
                                              @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    });
                }
            }];
        }

        NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

        - (IBAction)SendPushAction:(id)sender {
            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
                }
            }];
            [dataTask resume];
        }

3.  Infine, in **ViewDidLoad** aggiungere il codice seguente per creare l'istanza di RegisterClient e impostare il delegato per i campi di testo.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  In **AppDelegate.m** rimuovere tutto il contenuto del metodo **application:didRegisterForPushNotificationWithDeviceToken:** e sostituirlo con:

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    In questo modo il controller di visualizzazione conterrà il token del dispositivo più recente recuperato dagli APN.

5.  Sempre in **AppDelegate.m**, verificare che sia presente il metodo seguente:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Eseguire l'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1.  Assicurarsi che il progetto **AppBackend** sia distribuito in Azure. Se si usa Visual Studio, eseguire l'applicazione API Web **AppBackend**. Verrà visualizzata una pagina Web ASP.NET.

2.  In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).

3.  Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

4.  Nell'interfaccia utente dell'app per iOS fare clic su **Log in**. Fare clic su **Invia notifica push**.

  [Windows Universal]: /it-it/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /it-it/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /it-it/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Registrazione dal back-end dell'app]: http://msdn.microsoft.com/it-it/library/dn743807.aspx
  [Introduzione ad Hub di notifica (iOS)]: http://azure.microsoft.com/it-it/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
