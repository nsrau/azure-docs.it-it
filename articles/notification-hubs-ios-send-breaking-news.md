<properties linkid="notification-hubs-ios-send-breaking-news" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Utilizzo di Hub di notifica per inviare le ultime notizie

<div class="dev-center-tutorial-selector sublanding">       
    <a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
    <a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

Questo argomento descrive come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per iOS. Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app nelle quali le notifiche devono essere inviate a gruppi di utenti che hanno dichiarato un interesse, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più *tag* durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per altre informazioni sui tag, vedere l'articolo relativo alle [linee guida per gli hub di notifica][linee guida per gli hub di notifica].

In questa esercitazione vengono descritte le operazioni di base per abilitare questo scenario:

1.  [Aggiunta della selezione delle categorie all'app][Aggiunta della selezione delle categorie all'app]
2.  [Registrazione per le notifiche][Registrazione per le notifiche]
3.  [Invio di notifiche dal back-end][Invio di notifiche dal back-end]
4.  [Esecuzione dell'app e generazione di notifiche][Esecuzione dell'app e generazione di notifiche]

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica].

## <a name="adding-categories"></a>Aggiunta della selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente allo storyboard esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione nell'hub di notifica con le categorie selezionate come tag.

1.  Nel file MainStoryboard\_iPhone.storyboard aggiungere i componenti seguenti dalla libreria di oggetti:

    -   Un'etichetta con il testo "Breaking News".
    -   Etichette con il testo relativo alle categorie "World", "Politics", "Business", "Technology", "Science", "Sports".
    -   Sei opzioni, una per categoria.
    -   Un pulsante con l'etichetta "Subscribe".

    L'aspetto dello storyboard dovrebbe essere simile al seguente:

    ![][0]

2.  Nell'assistente dell'editor creare outlet per tutte le opzioni e denominarli "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".

    ![][1]

3.  Creare un'azione per il pulsante denominata "subscribe". BreakingNewsViewController.h deve contenere quanto segue:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4.  Creare una nuova classe denominata `Notifications`. Copiare il codice seguente nella sezione dell'interfaccia del file Notifications.h:

        @property NSData* deviceToken;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
        - (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

5.  Aggiungere la direttiva import seguente a Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6.  Copiare il codice seguente nella sezione di implementazione del file Notifications.h:

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }

        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

            [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
        }

    Questa classe utilizza l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltri i metodi per effettuare la registrazione per queste categorie.

7.  Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuta in precedenza.

    <div class="dev-callout"><strong>Nota</strong> 
    <p>Poich&eacute; le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completa viene utilizzata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.</p>
</div>

8.  Nel file BreakingNewsAppDelegate.h aggiungere la proprietà seguente:

        @property (nonatomic) Notifications* notifications;

    Verrà creata un'istanza singleton della classe Notification nel delegato dell'app.

9.  Nel metodo **didFinishLaunchingWithOptions** di BreakingNewsAppDelegate.m aggiungere il codice seguente prima di **registerForRemoteNotificationTypes**:

        self.notifications = [[Notifications alloc] init];

    Il singleton Notification verrà inizializzato.

10. Nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** di BreakingNewsAppDelegate.m rimuovere la chiamata a **registerNativeWithDeviceToken** e aggiungere il codice seguente:

        self.notifications.deviceToken = deviceToken;

    Si noti che a questo punto nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** non dovrebbe essere presente altro codice.

11. Aggiungere il metodo seguente in BreakingNewsAppDelegate.m:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    This method handles notifications received when the app is running by displaying a simple **UIAlert**.

12. In BreakingNewsViewController.m copiare il codice seguente nel metodo **subscribe** generato da XCode:

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

    Questo metodo crea un elenco **NSMutableArray** di categorie e utilizza la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

## <a name="register"></a>Registrazione per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nell'archiviazione locale.

<div class="dev-callout"><strong>Nota</strong> 
    <p>Poich&eacute; il token di dispositivo assegnato dal servizio di notifica Push di Apple pu&ograve; cambiare in qualsiasi momento, &egrave; necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite di frequente, oltre una volta al giorno, &egrave; possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.</p>
</div>

1.  Aggiungere il metodo seguente nella sezione dell'interfaccia del file Notifications.h:

        - (NSSet*)retrieveCategories;

    Questo codice consente di recuperare le categorie nella classe Notifications.

2.  Aggiungere l'implementazione corrispondente nel file Notifications.m:

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }

3.  Aggiungere il codice seguente nel metodo **didRegisterForRemoteNotificationsWithDeviceToken**:

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];
        [notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie.

4.  In BreakingNewsViewController.h aggiungere il codice seguente nel metodo **viewDidLoad**:

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

## <a name="send"></a><span class="short-header">Invio di notifiche</span>Invio di notifiche dal back-end

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Esecuzione dell'app e generazione di notifiche

1.  Premere il pulsante Esegui per compilare il progetto e avviare l'app.

    ![][2]

    Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione.

2.  Abilitare uno o più interruttori di categorie e quindi fare clic su **Subscribe**.

    Quando si fa clic su **Subscribe**, l'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica.

3.  Inviare una nuova notifica dal back-end in uno dei modi seguenti:

    -   **App console:** avviare l'app console.

    -   **Java/PHP:** eseguire l'app o lo script.

4.  Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità in base alle categorie. Per informazioni su altri scenari di Hub di notifica avanzati, provare a completare le esercitazioni seguenti:

-   **[Utilizzo di Hub di notifica per la trasmissione di notizie localizzate][Utilizzo di Hub di notifica per la trasmissione di notizie localizzate]**

    Altre informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate.

-   **[Utilizzo di Hub di notifica per inviare notifiche agli utenti][Utilizzo di Hub di notifica per inviare notifiche agli utenti]**

    Altre informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a utenti specifici.



  [linee guida per gli hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
  [Aggiunta della selezione delle categorie all'app]: #adding-categories
  [Registrazione per le notifiche]: #register
  [Invio di notifiche dal back-end]: #send
  [Esecuzione dell'app e generazione di notifiche]: #test-app
  [Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/get-started-notification-hubs-ios/
  [0]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
  [1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
  [Utilizzo di Hub di notifica per la trasmissione di notizie localizzate]: /it-it/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Utilizzo di Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users/
