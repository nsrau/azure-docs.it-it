
Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio mobile ogni volta che l'app viene avviata. Non solo questo metodo è inefficiente, ma si potrebbero riscontrare problemi relativi all'uso qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dai Servizi mobili e provare a usare questo prima di usare un accesso basato su provider.


>[WACOM.NOTE] È possibile memorizzare nella cache il token rilasciato dai Servizi mobili indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.

1. Il modo consigliato per crittografare e archiviare i token di autenticazione in un client iOS consiste nell'usare il Portachiavi. A questo scopo, creare una classe KeychainWrapper copiando [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) e [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) dall'[esempio LensRocket](https://github.com/WindowsAzure-Samples/iOS-LensRocket). Viene usata questa classe KeychainWrapper perché quella definita nella documentazione di Apple non prevede il conteggio dei riferimenti automatico (ARC).


2. Aprire il file di progetto **QSTodoListViewController.m** e aggiungere il codice seguente:

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. Alla fine del metodo **viewDidAppear** in **QSTodoListViewController.m** aggiungere una chiamata a saveAuthInfo. Tramite questa chiamata si archiviano semplicemente le proprietà userId e token.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Dopo avere appreso come memorizzare nella cache il token e l'ID utente, si vedrà come si possono caricare all'avvio dell'app. Nel metodo **viewDidLoad** in **QSTodoListViewController.m**, aggiungere una chiamata a loadAuthInfo dopo l'inizializzazione di **self.todoService**. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Se l'app invia al Servizio mobile una richiesta che dovrebbe avere esito positivo perché l'utente è autenticato e viene invece visualizzata una risposta 401 (errore non autorizzato), significa che il token utente passato è scaduto. È possibile cercare una risposta 401 nel gestore completamento per ogni metodo disponibile che interagisce con il Servizio mobile oppure gestire tutto in un unico punto: il metodo handleRequest di MSFilter.  Per informazioni su come gestire questo scenario, vedere [questo post di blog](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)

