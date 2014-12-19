<properties urlDisplayName="Localized Breaking News" pageTitle="Esercitazione sull'invio di ultime notizie localizzate per iOS mediante Hub di notifica" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications (iOS)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="yuaxu" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />
# Usare Hub di notifica per inviare le ultime notizie localizzate ai dispositivi iOS

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>


Questo argomento illustra come usare la funzionalità relativa ai **modelli** di Hub di notifica di Azure per trasmettere notifiche relative alle ultime notizie localizzate in base alla lingua e al dispositivo. In questa esercitazione verrà usata l'app di Windows Store creata in [Usare Hub di notifica per inviare le ultime notizie]. Al termine, sarà possibile effettuare la registrazione per le categorie di proprio interesse, specificare la lingua in cui ricevere le notifiche e ricevere solo notifiche push per le categorie selezionate nella lingua scelta.

In questa esercitazione vengono descritte le operazioni di base per abilitare questo scenario:

1. [Concetti relativi ai modelli]
2. [Interfaccia utente dell'app]
3. [Compilazione dell'app per iOS]
4. [Inviare notifiche dal back-end]


Lo scenario è composto da due parti:

- l'app per iOS consente ai dispositivi client di specificare una lingua e di sottoscrivere categorie diverse di ultime notizie;

- il back-end trasmette le notifiche usando le funzionalità relative ai **tag** e ai **modelli** di Hub di notifica di Azure.



##Prerequisiti ##

È necessario aver completato l'esercitazione [Usare Hub di notifica per inviare le e ultime notizie] e disporre del relativo codice, in quanto questa esercitazione è basata direttamente su tale codice.

È inoltre necessario disporre di Visual Studio 2012.



<h2><a name="concepts"></a>Concetti relativi ai modelli</h2>

In [Usare Hub di notifica per inviare le ultime notizie] è stata creata un'app che usa i **tag** per sottoscrivere le notifiche per diverse categorie di notizie.
Molte app, tuttavia, sono destinate a più mercati ed è necessario localizzarle. Questo significa che il contenuto delle notifiche deve essere localizzato e inviato al set di dispositivi corretto.
In questo argomento verrà illustrato come usare la funzionalità relativa ai **modelli** di Hub di notifica per inviare facilmente notifiche relative alle ultime notizie localizzate.

Nota: un possibile modo per inviare notifiche localizzate consiste nel creare più versioni di ogni tag. Per supportare l'inglese, il francese e il mandarino, ad esempio, sono necessari tre tag diversi per le ultime notizie internazionali: "world_en", "world_fr" e "world_ch". È quindi necessario inviare una versione localizzata delle ultime notizie internazionali a ogni tag. In questo argomento verranno usati i modelli per evitare la proliferazione di tag e la necessità di inviare più messaggi.

A livello generale, i modelli consentono di specificare in che modo un particolare dispositivo deve ricevere una notifica. Il modello definisce lo specifico formato di payload da usare, facendo riferimento alle proprietà del messaggio inviato dal back-end dell'app. In questo caso verrà inviato un messaggio indipendente dalle impostazioni locali, che contiene tutte le lingue supportate:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Si procederà quindi a verificare che i dispositivi effettuino la registrazione con un modello che faccia riferimento alla proprietà corretta. Per un'app per iOS che deve essere registrata per le notizie in francese, ad esempio, si effettuerà la registrazione nel modo seguente:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

I modelli rappresentano uno strumento particolarmente efficace. Per altre informazioni, vedere l'articolo relativo alle [linee guida su Hub di notifica]. Un riferimento al linguaggio di espressione dei modelli è disponibile in [Procedura: Hub notifiche del bus di servizio (app per iOS)].

<h2><a name="ui"></a>Interfaccia utente dell'app</h2>

L'app Breaking News creata nell'argomento [Usare Hub di notifica per inviare le ultime notizie] verrà ora modificata in modo da inviare notizie localizzate usando modelli.


In MainStoryboard_iPhone.storyboard aggiungere un controllo Segmented con le tre lingue supportate: inglese, francese e mandarino.

![][13]

Assicurarsi quindi di aggiungere un elemento IBOutlet in ViewController.h come illustrato di seguito:

![][14]

<h2><a name="building-client"></a><span class="building app">Interfaccia utente app</span>Compilazione dell'app per iOS</h2>

Per adattare le app client alla ricezione di messaggi localizzati è necessario sostituire le registrazioni *native*, ovvero prive di modello, con registrazioni modello.

1. In Notification.h aggiungere il metodo *retrieveLocale*, quindi modificare i metodi store e subscribe come illustrato di seguito:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	In Notification.m modificare il metodo *storeCategoriesAndSubscribe* aggiungendo il parametro relativo alle impostazioni locali e archiviandolo nelle impostazioni predefinite dell'utente:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Modificare quindi il metodo *subscribe* in modo da includere le impostazioni locali:

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Si noti che si sta ora usando il metodo *registerTemplateWithDeviceToken* anziché *registerNativeWithDeviceToken*. Quando si effettua la registrazione per un modello, è necessario fornire il modello json e un nome per il modello. Questo perché l'app potrebbe richiedere la registrazione di modelli diversi. Assicurarsi di registrare le categorie come tag, poiché è necessario ricevere notifiche per le notizie.

	Aggiungere infine un metodo per recuperare le impostazioni locali dalle impostazioni predefinite dell'utente:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. Ora che è stata modificata la classe Notifications, è necessario assicurarsi che ViewController utilizzi il nuovo UISegmentControl. Aggiungere la riga seguente nel metodo *viewDidLoad* per assicurarsi che vengano mostrate le impostazioni locali attualmente selezionate:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Nel metodo *subscribe* modificare quindi la chiamata a *storeCategoriesAndSubscribe* come segue:

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. Aggiornare infine il metodo *didRegisterForRemoteNotificationsWithDeviceToken* in AppDelegate.m, affinché sia possibile aggiornare correttamente la registrazione all'avvio dell'applicazione. Modificare la chiamata al metodo *subscribe* delle notifiche come segue:

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

<h2><a name="send"></a>Inviare notifiche localizzate dal back-end</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]


## Passaggi successivi

Per altre informazioni sull'uso dei modelli, vedere:

- [Usare Hub di notifica per inviare notifiche agli utenti: ASP.NET]
- [Usare Hub di notifica per inviare notifiche agli utenti: Servizi mobili]
- [Linee guida su Hub di notifica]

In [Procedure di Hub di notifica per iOS]è disponibile un riferimento al linguaggio di espressione dei modelli.




<!-- Anchors. -->
[Concetti relativi ai modelli]: #concepts
[Interfaccia utente dell'app]: #ui
[Compilazione dell'app per iOS]: #building-client
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]: #next-steps

<!-- Images. -->









[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[Procedura: Hub notifiche del bus di servizio (app per iOS)]: http://msdn.microsoft.com/it-it/library/jj927168.aspx
[Usare Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-ios
[Servizio mobile]: /it-it/develop/mobile/tutorials/get-started
[Usare Hub di notifica per inviare notifiche agli utenti: ASP.NET]: /it-it/manage/services/notification-hubs/notify-users-aspnet
[Usare Hub di notifica per inviare notifiche agli utenti: Servizi mobili]: /it-it/manage/services/notification-hubs/notify-users
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-push-js.md

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Procedura di registrazione di Windows Developer Preview per servizi mobili]: ../HowTo/mobile-services-windows-developer-preview-registration.md
[oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
[Procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/it-it/library/jj927168.aspx
