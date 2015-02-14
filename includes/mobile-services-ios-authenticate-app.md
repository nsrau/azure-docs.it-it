

1. Aprire il file di progetto QSTodoListViewController.m e nel metodo **viewDidLoad** rimuovere il seguente codice che ricarica i dati nella tabella:

        [self refresh];

2.	Subito dopo il metodo **viewDidLoad** aggiungere il seguente codice:  

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

    > [AZURE.NOTE] Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo **loginWithProvider** riportato in precedenza con uno dei seguenti: _microsoftaccount_, _facebook_, _twitter_, _google_ o _windowsazureactivedirectory_.
		
3. Scegliere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

   Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.<!--HONumber=42-->
