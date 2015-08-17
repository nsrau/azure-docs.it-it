<properties
	pageTitle="Introduzione ad Hub di notifica di Azure"
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push."
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
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Panoramica

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. In questa esercitazione si creerà un'app per iOS vuota che riceve notifiche push tramite il Servizio di notifica Push di Apple. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica.

##Prerequisiti

Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

+ [Mobile Services SDK per iOS]
+ [XCode 6][Install Xcode]
+ Dispositivo con iOS 8 o versione successiva
+ Iscrizione a iOS Developer Program

   >[AZURE.NOTE]Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un simulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per iOS.

> [AZURE.NOTE]Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Hub di notifica - Abilita notifiche Push di Apple](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Configurare l'hub di notifica

In questa sezione viene illustrato come creare e configurare un nuovo hub di notifica con il certificato push che è stato creato. Se si desidera utilizzare un hub di notifica che è stato già creato, è possibile ignorare i passaggi da 2 a 5.


1. In Accesso portachiavi, fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati**. Fare clic su **Esporta**, assegnare un nome al file, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][1]

	Annotare il nome del file e il percorso del certificato esportato.

	>[AZURE.NOTE]In questa esercitazione viene creato un file QuickStart.p12. Il nome e il percorso del file potrebbero essere diversi.

2. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

3. Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

   	![][2]

4. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

   	![][3]

5. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**) per aprire il dashboard.

   	![][4]

6. Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][5]

7. Fare clic sulla scheda **Configura** nella parte superiore e quindi scegliere il pulsante **Carica** nelle impostazioni di notifica di Apple per caricare l'identificazione digitale del certificato. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera usare il servizio push **Production** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

   	![][6]

8. Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Visualizza stringa di connessione**. Prendere nota delle due stringhe di connessione.

   	![][7]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

##Connettere l'app all'hub di notifica

1. In XCode creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

   	![][8]

2. Quando si impostano le opzioni per il nuovo progetto, assicurarsi di utilizzare **nome prodotto** e **ID organizzazione** utilizzati quando è stato impostato l'ID del pacchetto sul portale Apple Development.

	![][11]

3. In **Targets**, fare clic sul nome del progetto, quindi scegliere la scheda **Bulid Settings** ed espandere **Code Signing Identit**, quindi in **Debug** impostare **code-signing identity**. Passare dal **livello** **base** a **tutti** i livelli e impostare il **profilo di provisioning** sul profilo di provisioning creato in precedenza.

	Se non viene visualizzato il nuovo profilo di provisioning creato in XCode, provare ad aggiornare i profili per l’identità di firma facendo clic su **XCode** nella barra dei menu e quindi fare clic sulla scheda **Preferences**, **Account**, pulsante **View Details**, scegliere l’identità di firma e fare clic sul pulsante di aggiornamento nell'angolo inferiore destro.

   	![][9]

4. Scaricare la **versione 1.2.4** di [Mobile Services SDK per iOS] e decomprimere il file. In XCode, fare clic con il pulsante destro del mouse sul progetto e scegliere l’opzione **Add Fileso to** per aggiungere la cartella **WindowsAzureMessaging.framework** al progetto XCode. Selezionare **Copy items if needed** e fare clic su **Add**.

   	![][10]

5. Nel file AppDelegate.h aggiungere la seguente istruzione di importazione:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Nel file AppDelegate.m aggiungere il codice seguente nel metodo `didFinishLaunchingWithOptions`, in base alla versione di iOS. Questo codice registra l'handle di dispositivo con servizio APNS:

	Per iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Per la versione di iOS precedente alla 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. Nello stesso file, aggiungere i metodi seguenti e sostituire i segnaposto di valore letterale stringa con *nome hub* e *DefaultListenSharedAccessSignature* annotati in precedenza. Questo codice fornisce il token del dispositivo all'hub di notifica in modo che l'hub di notifica possa inviare notifiche:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. Nello stesso file aggiungere il metodo seguente per visualizzare un **avviso dell'interfaccia utente** se la notifica viene ricevuta mentre l'app è attiva:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Compilare ed eseguire l'app sul dispositivo per verificare gli errori.

## Come inviare notifiche


È possibile testare la ricezione delle notifiche nell'app mediante l'invio di notifiche nel portale di Azure, usando la scheda Debug nell'hub di notifica, come illustrato nella schermata seguente.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. In XCode aprire Main.storyboard e aggiungere i seguenti componenti dell'interfaccia utente dalla libreria di oggetti per consentire all'utente di inviare notifiche push nell'app.

	- Etichetta senza testo. Sarà utilizzata per segnalare errori di invio di notifiche. La proprietà **Lines** deve essere impostata su **0** in modo da limitare automaticamente la dimensione ai margini destro e sinistro e alla parte superiore della visualizzazione.
	- Un campo di testo con testo **segnaposto** impostato su **Enter Notification Message**. Vincolare il campo sotto l'etichetta, come illustrato di seguito. Impostare il controller di visualizzazione come delegato di uscita.
	- Un pulsante denominato **Send Notification** vincolato sotto il campo di testo e nell'area orizzontale.

	La visualizzazione dovrebbe apparire come segue:

	![][32]


2. Aprire il file ViewController.h e aggiungere le seguenti istruzioni `#import` e `#define`. Sostituire il valore letterale della stringa segnaposto con la stringa di connessione *DefaultFullSharedAccessSignature* e il *nome hub* effettivi.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Aggiungere uscite per il campo di testo ed etichetta connessi alla visualizzazione e aggiornare la definizione di `interface` per supportare `UITextFieldDelegate` e `NSXMLParserDelegate`. Aggiungere le tre dichiarazioni di proprietà riportate di seguito per supportare la chiamata dell'API REST e l'analisi della risposta.

	Il file ViewController.h dovrebbe apparire come segue:

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Aprire ViewController.m e aggiungere il codice seguente per analizzare la stringa di connessione *DefaultFullSharedAccessSignature*. Come indicato nel [riferimento all'API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), queste informazioni analizzate verranno utilizzate per generare un token di firma di accesso condiviso per l’intestazione della richiesta di *autorizzazione*.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. In ViewController.m, aggiornare il metodo `viewDidLoad` per analizzare la stringa di connessione quando viene caricata la visualizzazione. Aggiungere inoltre i metodi di utilità riportati di seguito.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. In ViewController.m, aggiungere il codice seguente per generare il token di autorizzazione SAS che verrà fornito nell’intestazione di *autorizzazione* come indicato nel [riferimento all'API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. **Ctrl+trascinare** dal pulsante **Send Notification** a ViewController.m per aggiungere un'azione per l’evento **Touch down** che esegue la chiamata all'API REST tramite il codice seguente.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

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
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. In ViewController.m, aggiungere il seguente metodo delegato per supportare la chiusura della tastiera per il campo di testo. **Ctrl+trascinare** dal campo di testo all'icona del controller di visualizzazione nella finestra di progettazione di interfaccia per impostare il controller di visualizzazione del delegato di uscita.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. In ViewController.m, aggiungere i seguenti metodi di delegato per supportare l'analisi di risposta utilizzando `NSXMLParser`.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Compilare il progetto e verificare che non siano presenti errori.



È possibile trovare tutti i payload di notifica disponibili nella [guida alla programmazione di notifiche push e locali] di Apple.



##Testare l'app

Per testare le notifiche push in iOS, è necessario distribuire l'applicazione in un dispositivo. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'applicazione e verificare che le registrazioni abbiano esito positivo, quindi premere **OK**.

	![][33]

2. Toccare nel campo di testo per immettere un messaggio di notifica. Premere il pulsante **Send** sulla tastiera o **Send Notification** nella visualizzazione per inviare il messaggio di notifica.

	![][34]

3. La notifica viene inviata a tutti i dispositivi registrati per ricevere la notifica.

	![][35]

In caso di problemi o suggerimenti per migliorare questa esercitazione per tutti i lettori, inserire un commento nella sezione Disqus.


##Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si vogliono segmentare gli utenti in base a gruppi di interesse, vedere [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services SDK per iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[portale di gestione di Azure]: https://manage.windowsazure.com/
[linee guida su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Usare Hub di notifica per inviare notifiche push agli utenti]: notification-hubs-aspnet-backend-ios-notify-users.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-ios-send-breaking-news.md

[guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!---HONumber=August15_HO6-->