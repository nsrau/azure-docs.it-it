

1. Aprire il file di progetto QSTodoListViewController.m e il metodo **viewDidLoad** e rimuovere il codice seguente che ricarica i dati nella tabella:

        [self refresh];

2.	Subito dopo il metodo **viewDidLoad** aggiungere il codice seguente:  

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

    <div class="dev-callout"><b>Nota</b>
	<p>Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo <strong>loginWithProvider</strong> riportato in precedenza con uno dei seguenti: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> o <em>windowsazureactivedirectory</em>.</p>
    </div>
		
3. Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.
