
L'esempio precedente contatta sia il provider di identità che il servizio mobile ogni volta che l'app viene avviata. È possibile invece memorizzare nella cache il token di autorizzazione e provare a usarlo per primo.

* Il modo consigliato per crittografare e archiviare i token di autenticazione in un client iOS consiste nell'usare il Portachiavi iOS. Verrà usato [SSKeychain](https://github.com/soffes/sskeychain), un semplice wrapper intorno al Portachiavi iOS. Seguire le istruzioni riportate nella pagina SSKeychain e aggiungerlo al progetto. Verificare che l'impostazione per **abilitare i moduli** sia abilitata nelle **impostazioni di creazione** del progetto (sezione **Apple LLVM - Lingue - Moduli**).

* Aprire **QSTodoListViewController.m** e aggiungere il codice seguente:

```
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
```

* In `loginAndGetData` modificare il blocco di completamento di `loginWithProvider:controller:animated:completion:`. Aggiungere la riga seguente subito prima di `[self refresh]` per archiviare le proprietà dell'ID utente e del token:

```
				[self saveAuthInfo];
```

* Caricare l'ID utente e il token all'avvio dell'app. Nel metodo `viewDidLoad` in **QSTodoListViewController.m** aggiungere la riga seguente subito dopo l'inizializzazione di `self.todoService`.

```
				[self loadAuthInfo];
```

<!---HONumber=Oct15_HO3-->