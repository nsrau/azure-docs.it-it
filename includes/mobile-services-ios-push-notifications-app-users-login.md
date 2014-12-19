
È quindi necessario modificare la modalità di registrazione delle notifiche push, per assicurarsi che l'utente sia autenticato prima del tentativo di registrazione. 

1. In **QSAppDelegate.m** rimuovere completamente l'implementazione di **didFinishLaunchingWithOptions**:

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Aprire il file di progetto **QSTodoListViewController.m**, quindi nel metodo **viewDidLoad** aggiungere il codice rimosso in precedenza:

	
		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		        [self refresh];
		    }];
		
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		}
