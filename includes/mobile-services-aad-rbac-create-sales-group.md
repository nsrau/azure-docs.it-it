In questa sezione si aggiungeranno alla directory due nuovi utenti, oltre al nuovo gruppo Sales. A uno degli utenti verrà concessa l'appartenenza al gruppo Sales, che invece non verrà concessa all'altro utente.

### Creare gli utenti

1.  Nel [portale di gestione di Azure] passare alla directory configurata in precedenza per l'autenticazione quando è stata eseguita l'esercitazione [Introduzione all'autenticazione].
2.  Nella parte superiore della pagina fare clic su **Utenti**. Fare quindi clic sul pulsante **Aggiungi utente** nella parte inferiore.
3.  Completare le finestre di dialogo per la creazione di un nuovo utente creando l'utente **Bob**. Prendere nota della password temporanea per l'utente.
4.  Creare ora l'utente **Dave**. Prendere nota della password temporanea per l'utente.
5.  I nuovi utenti dovrebbero essere simili a quanto illustrato nella figura seguente.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)

### Creare il gruppo Sales

1.  Nella pagina della directory fare clic su **Gruppi** nella parte superiore. Fare quindi clic sul pulsante **Aggiungi gruppo** nella parte inferiore.
2.  Immettere **Sales** come nome del gruppo e fare clic sul pulsante completa della finestra di dialogo per creare il gruppo.

    ![][1]

### Aggiungere l'appartenenza utenti al gruppo Sales

1.  Fare clic su **Gruppi** nella parte superiore della pagina della directory e quindi fare clic sul gruppo **Sales** per passare alla pagina del gruppo.
2.  Nella pagina del gruppo Sales fare clic su **Aggiungi membri**. Aggiungere l'utente **Bob** al gruppo Sales. L'utente **Dave** non deve essere un membro del gruppo.

    ![][2]

3.  Nella pagina del gruppo Sales fare clic su **Configura**, quindi notare l'**ID oggetto** del gruppo Sales. In questa esercitazione l'ID oggetto del gruppo viene effettivamente cercando tramite le API Graph. L'ID non è quindi necessario, tuttavia in alcuni casi è preferibile non usare il nome del gruppo perché può cambiare mentre l'ID rimane sempre lo stesso. Se si vuole archiviare l'ID gruppo come impostazione dell'app nel servizio mobile oppure impostarlo come hardcoded nel codice, è possibile trovarlo qui.

    ![][3]

   
  [1]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group.png
  [2]: ./media/mobile-services-aad-rbac-create-sales-group/group-membership.png
  [3]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png
