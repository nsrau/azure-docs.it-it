<properties 
	pageTitle="Uso di Hub di notifica di Azure per inviare notifiche agli utenti" 
	description="Informazioni su come inviare notifiche push sicure in Azure. Gli esempi di codice sono scritti in Objective-C mediante l'API .NET." 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="yuaxu"/>

#Uso di Hub di notifica di Azure per inviare notifiche agli utenti

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end WebAPI ASP.NET, come illustrato nell'argomento [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx).

> [AZURE.NOTE] Questa esercitazione presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (iOS)](http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started/). È inoltre propedeutica all'esercitazione [Push sicuro (iOS)](http://azure.microsoft.com/ documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/).
> Se si usa Servizi mobili come servizio back-end, vedere la [versione per Servizi mobili](/it-it/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/).


## Creare e configurare l'hub di notifica

Seguire le sezioni da 1 a 5 di [Introduzione ad Hub di notifica (iOS)](http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started/). Per risorse aggiuntive sul provisioning di dispositivi iOS, vedere la guida [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modificare l'app per iOS

1. Aprire l'app di visualizzazione Single Page creata seguendo le sezioni da 1 a 5 in [Introduzione ad Hub di notifica (iOS)](http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started/).

> [AZURE.NOTE] In questa sezione si presuppone che il progetto sia configurato con un nome di organizzazione vuoto. In caso contrario, sarà necessario anteporre il nome dell'organizzazione a tutti i nomi di classi.

2. In Main.storyboard aggiungere i seguenti componenti dalla libreria di oggetti:
	+ Un UITextField con testo segnaposto **Username**
	+ Un UITextField con testo segnaposto **Password** e selezionare l'opzione **Secure** in Attribute Inspector, sotto Textfield, Return Key
	+ Un UIButton con etichetta **1. Log in** e deselezionare l'opzione **Enabled** in Attributes Inspector, sotto Control, quindi Content
	+ Un UIButton con etichetta **2. Send Push** e deselezionare l'opzione **Enabled**

	L'aspetto dello storyboard dovrebbe essere simile al seguente:

    ![][IOS1]

3. Creare outlet per UITextField e UIButton nella porzione di interfaccia di ViewController.h

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Prima di tutto si creerà una classe RegisterClient per l'interfaccia con il back-end. Creare una classe Objective-C denominata RegisterClient che eredita da NSObject. Aggiungere quindi il seguente codice nella sezione di interfaccia di RegisterClient.h:

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. In RegisterClient.m aggiungere la seguente sezione di interfaccia:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Aggiungere il seguente codice nella sezione di implementazione RegisterClient.m e sostituire il segnaposto *{backend endpoint}* con l'URL di destinazione usato per distribuire il back-end dell'app nella sezione precedente.

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

	Il codice riportato sopra implementa la logica illustrata nell'articolo di istruzioni [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx) usando NSURLSession per eseguire chiamate REST al back-end dell'app e NSUserDefaults per archiviare in locale l'ID registrazione restituito dall'hub di notifica.

	Si noti che per il corretto funzionamento di questa classe è necessario che sia impostata la relativa proprietà **authorizationHeader**. Questa proprietà viene impostata tramite la classe **ViewController** dopo l'accesso.

7. In ViewController.h aggiungere la seguente dichiarazione per il token del dispositivo e fare riferimento a un'istanza di RegisterClient:

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. In ViewController.m impostare la classe ViewController come UITextFieldDelegate. Aggiungere quindi una dichiarazione di metodo privato:

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] Il seguente frammento non è uno schema di autenticazione sicuro. È consigliabile sostituire l'implementazione di **createAndSetAuthenticationHeaderWithUsername:AndPassword:** con il meccanismo di autenticazione specifico che genera un token di autenticazione che deve essere usato dalla classe client di registrazione, ad esempio OAuth o Active Directory.

9. Aggiungere quindi il seguente codice nella sezione di implementazione di ViewController.m:

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

	Si noti come impostando il token del dispositivo viene abilitato il pulsante di accesso. Questo dipende dal fatto che, nell'ambito dell'azione di accesso, il controller di visualizzazione esegue la registrazione per le notifiche push con il back-end dell'app. L'azione di accesso deve quindi essere accessibile solo dopo che il token del dispositivo è stato correttamente configurato. È possibile separare l'accesso dalla registrazione push, purché la prima azione avvenga prima della seconda.

10. In ViewController.m aggiungere una costante per l'endpoint back-end e usare i seguenti frammenti per implementare i metodi di azione per gli oggetti UIButton. Sostituire l'endpoint back-end segnaposto con l'URL di destinazione usato per il back-end.

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

11. Nella funzione **ViewDidLoad** aggiungere quanto segue per creare l'istanza di RegisterClient e impostare il delegato per i campi di testo.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. In **AppDelegate.m** rimuovere tutto il contenuto del metodo **application:didRegisterForPushNotificationWithDeviceToken:** e sostituirlo con il seguente per assicurarsi che il controller di visualizzazione contenga il token del dispositivo più recente recuperato dagli APN:

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Infine in **AppDelegate.m** verificare che sia presente il seguente metodo:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Esecuzione dell'applicazione

1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).

2. Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore. Quindi fare clic su **Log In**.

3. Verrà visualizzata una finestra popup che informa che la registrazione è stata completata. Fare clic su **OK**.

4. Fare clic su **Send push** e premere il pulsante home. Poco dopo verrà visualizzata una notifica push.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=45--> 
