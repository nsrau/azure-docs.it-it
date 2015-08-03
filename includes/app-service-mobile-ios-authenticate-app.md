

1. Aprire **QSTodoListViewController.m** e aggiungere il metodo seguente:


        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }


    > [AZURE.NOTE]Se si usa un provider di identità diverso da Facebook, modificare il valore passato a **loginWithProvider**. I valori supportati sono: _microsoftaccount_, _facebook_, _twitter_, _google_ oppure _windowsazureactivedirectory_.


2. Modificare `viewDidLoad` sostituendo `[self refresh]` alla fine con i valori seguenti:

        [self loginAndGetData];

3. Premere **Esegui** per avviare l'app, quindi accedere con il provider di identità scelto. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

<!---HONumber=July15_HO4-->