* Aprire **QSTodoListViewController.m** e aggiungere il metodo seguente. Modificare *facebook* in *accountmicrosoft*, *twitter*, *google* o *microsoftazureactivedirectory* se non si usa Facebook come provider di identità.

```
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
```

* Sostituire `[self refresh]` in `viewDidLoad` con quanto riportato di seguito:

```
        [self loginAndGetData];
```

* Fare clic su **segui** per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

<!---HONumber=Oct15_HO3-->