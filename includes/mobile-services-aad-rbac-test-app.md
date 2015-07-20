
Le istruzioni e le schermate seguenti si applicano ai test di un client di Windows Store, ma è possibile testare questo su una delle altre piattaforme supportate da servizi mobili di Azure.

1. In Visual Studio eseguire l'app client e tentare di eseguire l'autenticazione con l'account utente denominato Dave appena creato. 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. L'utente Dave non appartiene al gruppo Sales. Di conseguenza, il controllo di accesso basato sui ruoli negherà l'accesso alle operazioni sulle tabelle. Chiudere l'app client.

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. In Visual Studio eseguire di nuovo l'app client. Questa volta, eseguire l'autenticazione con l'account utente denominato Bob creato in precedenza.

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. L'utente Bob appartiene al gruppo Sales. Di conseguenza, il controllo di accesso basato sui ruoli consentirà l'accesso alle operazioni sulle tabelle.

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=July15_HO2-->