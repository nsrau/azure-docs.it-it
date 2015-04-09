1. Aprire **QSTodoListViewController.m** e nel metodo **viewDidLoad**, rimuovere la riga seguente:

        [aggiornamento automatico];

2.	Subito dopo il metodo **viewDidLoad** aggiungere il seguente codice:  

			- (void)viewDidAppear:(BOOL)animated
			{
				MSClient *client = self.todoService.client;
			
			if (client.currentUser != nil) {
				return;
			}

			[client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [aggiornamento automatico];
            }];
			}

    > [AZURE.NOTE] Se si utilizza un provider di identità diverso da Facebook, modificare il valore passato a **loginWithProvider**. I valori supportati sono: _microsoftaccount_, _facebook_, _twitter_, _google_ o _windowsazureactivedirectory_.

3. Premere **Esegui** per avviare l'app, quindi accedere con il provider di identità scelto. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

<!--HONumber=49-->