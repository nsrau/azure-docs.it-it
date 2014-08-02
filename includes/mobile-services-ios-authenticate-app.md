
1.  Aprire il file di progetto QSTodoListViewController.m e il metodo **viewDidLoad** e rimuovere il codice seguente che ricarica i dati nella tabella:

         [self refresh];

2.  Subito dopo il metodo **viewDidLoad** aggiungere il codice seguente:

        - (void)viewDidAppear:(BOOL)animated
                {
                    MSClient *client = self.todoService.client;
                
            if (client.currentUser != nil) {
                return;
                    }
                        
                    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
                    }];
                }

    **Nota**

    Se si utilizza un provider di identità diverso da Facebook, sostituire il valore passato al metodo **loginWithProvider** riportato in precedenza con uno dei seguenti: *microsoftaccount*, *facebook*, *twitter* o *google*.

3.  Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.


