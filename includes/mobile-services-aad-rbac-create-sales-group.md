In questa sezione si aggiungeranno alla directory due nuovi utenti, oltre al nuovo gruppo Sales. A uno degli utenti verrà concessa l'appartenenza al gruppo Sales, che invece non verrà concessa all'altro utente. 

### Creare gli utenti


1. Nel [Portale di gestione di Azure] passare alla directory configurata in precedenza per l'autenticazione quando è stata eseguita l'esercitazione per l'aggiunta dell'autenticazione all'app.
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

3. Nella pagina del gruppo Sales, fare clic su **Proprietà**, quindi copiare l'**ID oggetto** per il gruppo Sales nella parte inferiore della pagina. 

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Tornare alla pagina di configurazione del servizio mobile e aggiungere l'id oggetto come impostazione di app denominata **AAD\_SALES\_GROUP\_ID**. Questa esercitazione utilizza l'id oggetto del gruppo come impostazione dell'app anziché cercare l'id in base al nome del gruppo. Questo perché il nome del gruppo può cambiare mentre l'id resta invariato.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!--HONumber=49-->