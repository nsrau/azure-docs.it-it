<properties
	pageTitle="Esercitazione sull'invio di ultime notizie mediante Hub di notifica - iOS"
	description="Informazioni su come usare Hub di notifica del bus di servizio di Azure per inviare notifiche relative alle ultime notizie a dispositivi iOS."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# Uso di Hub di notifica per inviare le ultime notizie

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Panoramica

Questo argomento illustra come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per iOS. Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app nelle quali le notifiche devono essere inviate a gruppi di utenti che hanno dichiarato un interesse, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più _tag_ durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per ulteriori informazioni sui tag, vedere l'articolo relativo alle [linee guida per gli hub di notifica].


##Prerequisiti

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][get-started].

##Aggiungere la selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente allo storyboard esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione nell'hub di notifica con le categorie selezionate come tag.

1. Nel file MainStoryboard\_iPhone.storyboard aggiungere i componenti seguenti dalla libreria di oggetti:
	+ Un'etichetta con il testo "Breaking News".
	+ Etichette con il testo relativo alle categorie "World", "Politics", "Business", "Technology", "Science", "Sports".
	+ Sei opzioni, una per ogni categoria, impostare lo **stato** di ogni opzione su **Off** per impostazione predefinita.
	+ Un pulsante con l'etichetta "Subscribe".

	L'aspetto dello storyboard dovrebbe essere simile al seguente:

	![][3]

2. Nell'assistente dell'editor creare outlet per tutte le opzioni e denominarli "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".


3. Creare un'azione per il pulsante denominata "subscribe". BreakingNewsViewController.h deve contenere quanto segue:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

4. Creare una nuova **classe Cocoa Touch** chiamata `Notifications`. Copiare il seguente codice nella sezione dell'interfaccia del file Notifications.h:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
					completion:(void (^)(NSError* error))completion;

		- (NSSet*)retrieveCategories;

		- (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Aggiungere la seguente direttiva import a Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Nel codice seguente della sezione dell’implementazione del file Notifications.m sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuta in precedenza.

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}


		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}


		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
		{
		    SBNotificationHub* hub = [[SBNotificationHub alloc]
										initWithConnectionString:@"<connection string with listen access>"
										notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}



	Questa classe usa l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltre un metodo per effettuare la registrazione per queste categorie.

	> [AZURE.NOTE]Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completo viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

7. Nel file Appdelegate.h, aggiungere un'istruzione import per Notifications.h e aggiungere una proprietà per un'istanza della classe delle notifiche:

		#import "Notifications.h"

		@property (nonatomic) Notifications* notifications;

	Verrà creata un'istanza singleton della classe Notification nel delegato dell'app.

8. Nel metodo **didFinishLaunchingWithOptions** in appdelegate.m aggiungere il codice per inizializzare l'istanza di notifiche all'inizio del metodo:

		self.notifications = [[Notifications alloc] init];

	Il singleton Notification verrà inizializzato.


9. Nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** in AppDelegate.m sostituire il codice nel metodo con il codice seguente per passare il token del dispositivo alla classe delle notifiche. La classe delle notifiche eseguirà la registrazione per le notifiche con le categorie. Se l'utente modifica le selezioni delle categorie, chiamare il metodo `subscribeWithCategories` in risposta al pulsante **sottoscrizione** per aggiornarle.

	> [AZURE.NOTE]Poiché il token di dispositivo assegnato da Apple Push Notification Service può cambiare in qualsiasi momento, è necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite con una frequenza maggiore di una volta al giorno, è possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.

		self.notifications.deviceToken = deviceToken;

		// Retrieves the categories from local storage and requests a registration for these categories
		// each time the app starts and performs a registration.

	    NSSet* categories = [self.notifications retrieveCategories];
	    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];


	Si noti che a questo punto nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** non dovrebbe essere presente altro codice.

10.	I metodi seguenti devono essere già presenti in AppDelegate.m dal completamento dell’esercitazione [Introduzione ad hub di notifica][get-started]. In caso contrario, aggiungerli.

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
	    }

	This method handles notifications received when the app is running by displaying a simple **UIAlert**.

11. In ViewController.m, aggiungere un'istruzione import per AppDelegate.h e copiare il codice seguente nel metodo **subscribe** generato da XCode. Questo codice aggiornerà la registrazione della notifica per utilizzare i nuovi tag di categoria selezionati dall'utente nell'interfaccia utente.

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            [self MessageBox:@"Notification" message:@"Subscribed!"];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Questo metodo crea un elenco **NSMutableArray** di categorie e utilizza la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

12. In ViewController.m, aggiungere il codice seguente nel metodo **viewDidLoad** per impostare l'interfaccia utente in base alle categorie salvate in precedenza.


		// This updates the UI on startup based on the status of previously saved categories.

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Ora l'app può archiviare un insieme di categorie nella risorsa di archiviazione locale del dispositivo utilizzata per la registrazione con l'hub di notifica ogni volta che l'app viene avviata. L'utente può modificare la selezione di categorie al runtime e scegliere il metodo **subscribe** per aggiornare la registrazione per il dispositivo. Successivamente, si aggiornerà l'app per inviare le notifiche relative alle ultime notizie direttamente nell'applicazione stessa.


##Inviare notifiche

In genere potrebbero essere inviate le notifiche da un servizio back-end ma per questa esercitazione verrà aggiornato il codice di notifica di invio in modo da poter inviare notifiche relative alle ultime notizie direttamente dall'applicazione. A tale scopo si aggiornerà il metodo `SendNotificationRESTAPI` definito nell’esercitazione [Introduzione agli hub di notifica][get-started].


1. In ViewController.m aggiornare metodo `SendNotificationRESTAPI` come segue in modo che acquisisca un parametro di servizio di notifica piattaforma `pns` e un parametro per il tag di categoria.

		- (void)SendNotificationRESTAPI:(NSString*)pns Category:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the APNS notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

		    // Windows Notification format of the notification message
		    if ([pns isEqualToString:@"wns"])
		    {
		        json = [NSString stringWithFormat:@"<?xml version="1.0" encoding="utf-8"?>"
		                                           "<toast>"
		                                           "<visual><binding template="ToastText01">"
		                                           "<text id="1">Breaking %@ News : %@</text>"
		                                           "</binding>"
		                                           "</visual>"
		                                           "</toast>",
		                categoryTag, self.notificationMessage.text];

		        // Signify windows notification format
		        [request setValue:@"windows" forHTTPHeaderField:@"ServiceBusNotification-Format"];

		        // XML Content-Type
		        [request setValue:@"application/xml" forHTTPHeaderField:@"Content-Type"];

		        // Set X-WNS-TYPE header
		        [request setValue:@"wns/toast" forHTTPHeaderField:@"X-WNS-Type"];
		    }

		    // Google Cloud Messaging Notification format of the notification message
		    if ([pns isEqualToString:@"gcm"])
		    {
		        json = [NSString stringWithFormat:@"{"data":{"message":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify gcm notification format
		        [request setValue:@"gcm" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }

		    // Apple Notification format of the notification message
		    if ([pns isEqualToString:@"apns"])
		    {
		        json = [NSString stringWithFormat:@"{"aps":{"alert":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify apple notification format
		        [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }

		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
		               {
		               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		                   if (error || httpResponse.statusCode != 200)
		                   {
		                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
		                   }
		                   if (data != NULL)
		                   {
		                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
		                       //[xmlParser setDelegate:self];
		                       //[xmlParser parse];
		                   }
		               }];
		    [dataTask resume];
		}



2. In ViewController.m aggiornare l’azione **Send Notification** nel modo illustrato nel codice seguente. In tal modo si inviano notifiche a più piattaforme utilizzando singolarmente ogni tag.



		- (IBAction)SendNotificationMessage:(id)sender
		{
		    self.sendResults.text = @"";

		    NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
									@"Technology", @"Science", @"Sports", nil];

		    // Lets send the message as breaking news for each category to WNS, GCM, and APNS
		    for(NSString* category in categories)
		    {
		        [self SendNotificationRESTAPI:@"wns" Category:category];
		        [self SendNotificationRESTAPI:@"gcm" Category:category];
		        [self SendNotificationRESTAPI:@"apns" Category:category];
		    }
		}



3. Ricompilare il progetto e assicurarsi che non siano presenti errori di compilazione.


##Eseguire l'app e generare notifiche

1. Premere il pulsante Esegui per compilare il progetto e avviare l'app. Selezionare alcune opzioni di notizie per cui eseguire la sottoscrizione e premere il pulsante **Subscribe**. Verrà visualizzata una finestra di dialogo che indica che è staa effettuata la sottoscrizione alle notifiche.

	![][1]

	Quando si fa clic su **Subscribe**, l'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica.

2. Immettere un messaggio da inviare come ultime notizie, quindi premere il pulsante **Send Notification**

	![][2]


3. Ogni dispositivo iscritto alle notizie riceverà le notifiche relative alle ultime notizie appena inviate.



## Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità per categoria. Per informazioni su altri scenari avanzati di Hub di notifica, provare a completare le seguenti esercitazioni:

+ **[Usare Hub di notifica per la trasmissione di notizie localizzate]**

	Informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate.

+ **[Usare Hub di notifica per inviare notifiche agli utenti]**

	Informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a determinati utenti.



<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usare Hub di notifica per la trasmissione di notizie localizzate]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Usare Hub di notifica per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-ios-notify-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[linee guida per gli hub di notifica]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!---HONumber=September15_HO1-->