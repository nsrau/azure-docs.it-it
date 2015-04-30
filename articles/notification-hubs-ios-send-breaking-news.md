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
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>

# Uso di Hub di notifica per inviare le ultime notizie
<div class="dev-center-tutorial-selector sublanding">
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

##Informazioni generali

Questo argomento illustra come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per iOS. Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app che prevedono l'invio di notifiche a gruppi di utenti che hanno in precedenza manifestato il proprio interesse verso tali app, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più _tags_ durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per altre informazioni sui tag, vedere le [informazioni aggiuntive su Hub di notifica].


##Prerequisiti

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][get-started].

##Aggiunta della selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente allo storyboard esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione del dispositivo nell'hub di notifica con le categorie selezionate come tag.

2. Nel file MainStoryboard_iPhone.storyboard aggiungere i seguenti componenti dalla libreria di oggetti:
	+ Un'etichetta con il testo "Breaking News".
	+ Etichette con il testo relativo alle categorie "World", "Politics", "Business", "Technology", "Science", "Sports".
	+ Sei opzioni, una per categoria.
	+ Un pulsante con l'etichetta "Subscribe".

	L'aspetto dello storyboard dovrebbe essere simile al seguente:

	![][3]

3. Nell'assistente dell'editor creare outlet per tutte le opzioni e denominarli "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".

	![][4]

4. Creare un'azione per il pulsante denominata "subscribe". BreakingNewsViewController.h deve contenere quanto segue:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Creare una nuova classe denominata  `Notifications`. Copiare il seguente codice nella sezione dell'interfaccia del file Notifications.h:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Aggiungere la seguente direttiva import a Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Copiare il seguente codice nella sezione di implementazione del file Notifications.h:

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	Questa classe usa l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltri i metodi per effettuare la registrazione per queste categorie.

4. Nel codice sopra menzionato sostituire i segnaposto  `<hub name>`  e  `<connection string with listen access>`  con il nome dell'hub di notifica e la stringa di connessione per  *DefaultListenSharedAccessSignature* ottenuta in precedenza.

	> [AZURE.NOTE] Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completo viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

8. Nel file BreakingNewsAppDelegate.h aggiungere la seguente proprietà:

		@property (nonatomic) Notifications* notifications;

	Verrà creata un'istanza singleton della classe Notification nel delegato dell'app.

9. Nel metodo **didFinishLaunchingWithOptions** di BreakingNewsAppDelegate.m, aggiungere il seguente codice prima di **registerForRemoteNotificationTypes**:

		self.notifications = [[Notifications alloc] init];

	Il singleton Notification verrà inizializzato.

10. Nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** di BreakingNewsAppDelegate.m, rimuovere la chiamata a **registerNativeWithDeviceToken** e aggiungere il seguente codice:

		self.notifications.deviceToken = deviceToken;

	Si noti che a questo punto nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** non dovrebbe essere presente altro codice.

11.	Aggiungere il seguente metodo in BreakingNewsAppDelegate.m:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Questo metodo gestisce le notifiche ricevute quando l'app è in esecuzione visualizzando un semplice **UIAlert**.

9. In BreakingNewsViewController.m copiare il seguente codice nel metodo **subscribe** generato da XCode:

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Questo metodo crea un **NSMutableArray** di categorie e usa la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Quando le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

##Registrarsi per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nella risorsa di archiviazione locale.

> [AZURE.NOTE] Poiché il token di dispositivo assegnato da Apple Push Notification Service può cambiare in qualsiasi momento, è necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite con una frequenza maggiore di una volta al giorno, è possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.

1. Aggiungere il seguente metodo nella sezione dell'interfaccia del file Notifications.h:

		- (NSSet*)retrieveCategories;

	Questo codice consente di recuperare le categorie nella classe Notifications.

2. Aggiungere l'implementazione corrispondente nel file Notifications.m:

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Aggiungere il codice seguente nel metodo **didRegisterForRemoteNotificationsWithDeviceToken**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie.

3. In BreakingNewsViewController.h aggiungere il seguente codice nel metodo **viewDidLoad**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	All'avvio, l'interfaccia utente verrà aggiornata in base allo stato delle categorie salvate in precedenza.

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Verrà ora definito un back-end per l'invio delle notifiche delle categorie all'app.

##Inviare notifiche dal back-end

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##Eseguire l'app e generare notifiche

1. Premere il pulsante Esegui per compilare il progetto e avviare l'app.

	![][2]

	Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione.

2. Abilitare uno o più interruttori di categorie e quindi fare clic su **Sottoscrivi**.

	Quando si fa clic su **Sottoscrivi**, l'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica.

4. Inviare una nuova notifica dal back-end in uno dei modi seguenti:

	+ **App console:** avviare l'app console.

	+ **Java/PHP:** eseguire l'app o lo script.

5. Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

## Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità per categoria. Per informazioni su altri scenari avanzati di Hub di notifica, provare a completare le seguenti esercitazioni:

+ **[Usare Hub di notifica per la trasmissione di notizie localizzate]**

	Informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate.

+ **[Usare Hub di notifica per inviare notifiche agli utenti]**

	Informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a determinati utenti.



<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Procedura: Hub di notifica di Service Bus (app per iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usare Hub di notifica per la trasmissione di notizie localizzate]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Servizio mobile]: /develop/mobile/tutorials/get-started
[Usare Hub di notifica per inviare notifiche agli utenti]: /manage/services/notification-hubs/notify-users/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=49-->