
Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio mobile ogni volta che l'app viene avviata. Questo metodo non è scalabile ed è inefficiente.

Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dai Servizi mobili di Azure e provare a usarlo prima tentare l'accesso basato su provider.

1. Il modo consigliato per crittografare e archiviare i token di autenticazione in un client iOS consiste nell'usare il Portachiavi iOS. Verrà usato [SSKeychain](https://github.com/soffes/sskeychain), un semplice wrapper intorno al Portachiavi iOS. Seguire le istruzioni riportate nella pagina SSKeychain e aggiungerlo al progetto. Verificare che l'impostazione per **abilitare i moduli** sia abilitata nelle **impostazioni di creazione** del progetto (sezione **Apple LLVM - Lingue - Moduli**).

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

3. Nel metodo `loginAndGetData`, modificare il blocco di completamento della chiamata `loginWithProvider:controller:animated:completion:` mediante l'aggiunta di una chiamata a `saveAuthInfo` prima della riga `[self refresh]`. Con questa chiamata, vengono archiviate le proprietà token e l'ID utente in modo semplice.

				[self saveAuthInfo];

4. Caricare anche l'ID utente e il token all'avvio dell'app. Nel metodo `viewDidLoad` in **QSTodoListViewController.m**, aggiungere una chiamata a loadAuthInfo subito dopo l'inizializzazione di `self.todoService`.

				[self loadAuthInfo];
<!--HONumber=54-->