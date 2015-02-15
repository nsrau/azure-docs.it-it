In questa sezione si aggiungeranno alla directory due nuovi utenti, oltre al nuovo gruppo Sales. A uno degli utenti verrà concessa l'appartenenza al gruppo Sales, che invece non verrà concessa all'altro utente. 

### Creare gli utenti


1. Nel [portale di gestione di Azure] passare alla directory configurata in precedenza per l'autenticazione quando è stata eseguita l'esercitazione [Introduzione all'autenticazione]].
2. Fare clic su **Utenti** nella parte superiore della pagina. Fare quindi clic sul pulsante **Aggiungi utente** nella parte inferiore. 
3. Completare le finestre di dialogo per la creazione di un nuovo utente creando l'utente **Bob**. Prendere nota della password temporanea per l'utente. 
4. Creare ora l'utente **Dave**. Prendere nota della password temporanea per l'utente.
5. I nuovi utenti dovrebbero essere simili a quanto illustrato nella figura seguente.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)    


### Creare il gruppo Sales


1. Nella pagina della directory, nella parte superiore, fare clic su **Gruppi**. Fare quindi clic sul pulsante **Aggiungi gruppo** nella parte inferiore. 
2. Immettere **Sales** come nome del gruppo e fare clic sul pulsante Completa della finestra di dialogo per creare il gruppo. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Aggiungere l'appartenenza utenti al gruppo Sales


1. Fare clic su **Gruppi** nella parte superiore della pagina della directory. Fare quindi clic sul gruppo **Sales** per passare alla relativa pagina. 
2. Nella pagina del gruppo Sales fare clic su **Aggiungi membri**. Aggiungere l'utente **Bob** al gruppo Sales. L'utente **Dave** non deve essere un membro del gruppo.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. Nella pagina del gruppo Sales fare clic su **Configura**, quindi notare l'**ID oggetto** del gruppo. In questa esercitazione l'ID oggetto del gruppo viene effettivamente cercato tramite le API Graph. L'ID non è quindi necessario, tuttavia in alcuni casi è preferibile non usare il nome del gruppo perché può cambiare mentre l'ID rimane sempre lo stesso. Se si vuole archiviare l'ID gruppo come impostazione dell'app nel servizio mobile oppure impostarlo come hardcoded nel codice, è possibile trovarlo qui.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)



<!--HONumber=42-->
