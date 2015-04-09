
Nell'esempio precedente viene illustrato l'accesso standard, che richiede al client di contattare il provider di identità e il servizio app ad ogni avvio dell'app. Tale metodo risulta inefficiente e sarebbe meglio memorizzare nella cache il token di autorizzazione restituito dal servizio app e provare a utilizzarlo prima di un accesso basato su provider.

1. Il metodo consigliato per crittografare e archiviare token di autorizzazione su un client iOS consiste nell'utilizzare iOS Keychain. Questa esercitazione utilizza [SSKeychain](https://github.com/soffes/sskeychain) -- un semplice wrapper intorno a iOS Keychain. Attenersi alle istruzioni della pagina SSKeychain e aggiungere l'elemento al progetto. Verificare che l'impostazione **Abilita moduli** sia abilitata nelle **Impostazioni generazione** del progetto (sezione **Apple LLVM - Lingue - Moduli**.)

2. Aprire **QSTodoListViewController.m** e aggiungere il codice seguente:


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. Alla fine del metodo **viewDidAppear** in **QSTodoListViewController.m**, aggiungere una chiamata a **saveAuthInfo** appena prima della riga `[aggiornamento automatico]`. Con questa chiamata, vengono archiviate le proprietà token e l'ID utente in modo semplice:

				[self saveAuthInfo];

4. caricare anche l'ID utente e il token all'avvio dell'app. Nel metodo **viewDidLoad** in **QSTodoListViewController.m**, aggiungere una chiamata a loadAuthInfo subito dopo l'inizializzazione di **self.todoService**.

				[self loadAuthInfo];

<!--HONumber=49-->