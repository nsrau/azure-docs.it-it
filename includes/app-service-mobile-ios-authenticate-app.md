**Objective-C**:

1. In Mac aprire _QSTodoListViewController.m_ in Xcode e aggiungere il metodo seguente. Modificare _google_ in _accountmicrosoft_, _twitter_, _facebook_ o _microsoftazureactivedirectory_ se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario consentire i domini Facebook all'interno dell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Sostituire `[self refresh]` in `viewDidLoad` in _QSTodoListViewController.m_ con quanto riportato di seguito:

            [self loginAndGetData];

3. Fare clic su _segui_ per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

**Swift**:

1. In Mac aprire _ToDoTableViewController.swift_ in Xcode e aggiungere il metodo seguente. Modificare _google_ in _accountmicrosoft_, _twitter_, _facebook_ o _microsoftazureactivedirectory_ se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario consentire i domini Facebook all'interno dell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Rimuovere le righe `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` alla fine di `viewDidLoad()` in _ToDoTableViewController.swift_. Aggiungere una chiamata a `loginAndGetData()` al posto di tali righe:

            loginAndGetData()

3. Fare clic su _segui_ per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

<!---HONumber=AcomDC_0218_2016-->