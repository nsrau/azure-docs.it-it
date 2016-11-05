**Objective-C**:

1. In Mac aprire *QSTodoListViewController.m* in Xcode e aggiungere il metodo seguente. Modificare *google* in *accountmicrosoft*, *twitter*, *facebook* o *microsoftazureactivedirectory* se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario consentire i domini Facebook all'interno dell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
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
2. Sostituire `[self refresh]` in `viewDidLoad` in *QSTodoListViewController.m* con quanto riportato di seguito:
   
            [self loginAndGetData];
3. Fare clic su *segui* per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

**Swift**:

1. In Mac aprire *ToDoTableViewController.swift* in Xcode e aggiungere il metodo seguente. Modificare *google* in *accountmicrosoft*, *twitter*, *facebook* o *microsoftazureactivedirectory* se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario consentire i domini Facebook all'interno dell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. Rimuovere le righe `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` alla fine di `viewDidLoad()` in *ToDoTableViewController.swift*. Aggiungere una chiamata a `loginAndGetData()` al posto di tali righe:
   
            loginAndGetData()
3. Fare clic su *segui* per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

<!---HONumber=AcomDC_0218_2016-->