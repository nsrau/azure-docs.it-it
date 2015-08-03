In questa sezione, vengono aggiunti alla directory due nuovi utenti, oltre al nuovo gruppo Sales. A uno degli utenti viene concessa l'appartenenza al gruppo Sales, mentre all'altro utente non viene concessa.

### Creare gli utenti


1. Nel [portale di gestione di Azure] passare alla directory configurata in precedenza per l'autenticazione quando è stata eseguita l'esercitazione per l'aggiunta dell'autenticazione all'app.
2. Nella parte superiore della pagina fare clic su **Utenti**. Fare quindi clic sul pulsante **Aggiungi utente** nella parte inferiore. 
3. Completare le finestre di dialogo per la creazione di un nuovo utente creando l'utente **Bob**. Prendere nota della password temporanea per l'utente. 
4. Creare ora l'utente **Dave**. Prendere nota della password temporanea per l'utente.
5. I nuovi utenti dovrebbero essere simili a quanto illustrato nella figura seguente.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)


### Creare il gruppo Sales


1. Nella pagina della directory fare clic su **Gruppi** nella parte superiore. Fare quindi clic sul pulsante **Aggiungi gruppo** nella parte inferiore. 
2. Immettere **Sales** come nome del gruppo e fare clic sul pulsante completa della finestra di dialogo per creare il gruppo. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Aggiungere l'appartenenza utenti al gruppo Sales


1. Fare clic su **Gruppi** nella parte superiore della pagina della directory e quindi fare clic sul gruppo **Sales** per passare alla pagina del gruppo. 
2. Nella pagina del gruppo Sales fare clic su **Aggiungi membri**. Aggiungere l'utente **Bob** al gruppo Sales. L'utente **Dave** non deve essere un membro del gruppo.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. Nella pagina del gruppo Sales, fare clic su **Proprietà**, quindi copiare **l'ID oggetto** per il gruppo Sales nella parte inferiore della pagina.

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Tornare alla pagina di configurazione del servizio mobile e aggiungere l'ID oggetto come impostazione di app denominata **AAD_SALES_GROUP_ID**. Questa esercitazione usa l'ID oggetto del gruppo come impostazione dell'app anziché cercare l'ID in base al nome del gruppo. Questo perché il nome del gruppo può cambiare mentre l'ID resta invariato.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!---HONumber=July15_HO4-->