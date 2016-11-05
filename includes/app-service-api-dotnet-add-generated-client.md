1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione) e quindi scegliere **Aggiungi > Client app per le API di Azure**. 
   
    ![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
2. Nella finestra di dialogo **Aggiungi client app per le API di Azure** fare clic su **Scarica da app per le API di Azure**.
3. Nell'elenco a discesa selezionare l'app per le API da chiamare.
4. Fare clic su **OK**.
   
    ![Schermata di generazione](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)
   
    La procedura guidata scaricherà il file di metadati dell'API e genererà un'interfaccia tipizzata per richiamare l'app per le API.
   
    ![Generazione in corso](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)
   
    Al termine della generazione di codice, in **Esplora soluzioni** verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa le classi client e i modelli di dati.
   
    ![Generazione completata](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=Oct15_HO3-->