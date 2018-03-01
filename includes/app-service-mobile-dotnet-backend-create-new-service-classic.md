1. Accedere al [portale di Azure].
2. Selezionare **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end di App per dispositivi mobili.
3. Per **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo usando lo stesso nome dell'app. 
4. Per **Piano di servizio app** viene selezionato il piano predefinito nel [livello Standard](https://azure.microsoft.com/pricing/details/app-service/). È anche possibile selezionare un piano diverso oppure [crearne uno nuovo](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Le impostazioni del piano di servizio app determinano [località, funzionalità, costo e risorse di calcolo](https://azure.microsoft.com/pricing/details/app-service/) associati all'app. Per altre informazioni sui piani di servizio app e su come creare un nuovo piano in un diverso piano tariffario e nella località desiderata, vedere la [panoramica approfondita dei piani di servizio app di Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Selezionare **Create**. Questo passaggio crea il back-end di App per dispositivi mobili. 
6. Nel riquadro **Impostazioni** per il nuovo back-end di App per dispositivi mobili selezionare **Avvio rapido** > piattaforma app client > **Connettere un database**. 
   
   ![Selezioni per la connessione di un database](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Nel riquadro **Aggiungi connessione dati** selezionare **Database SQL** > **Crea un nuovo database**. Immettere il nome del database, scegliere un piano tariffario e quindi selezionare **Server**. È possibile riutilizzare questo nuovo database. Se si ha già un database nella stessa località, è possibile scegliere **Usare un database esistente**. Non è consigliabile usare un database in una località diversa a causa dei costi di larghezza di banda e della latenza più elevata.
   
   ![Selezione di un database](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Nel riquadro **Nuovo server** immettere un nome server univoco nella casella **Nome server**, specificare un account di accesso e una password e selezionare **Consenti ai servizi di Azure di accedere al server** e quindi **OK**. Questo passaggio crea il nuovo database.
9. Nel riquadro **Aggiungi connessione dati** selezionare **Stringa di connessione**, immettere i valori di account di accesso e password per il database e selezionare **OK**. 

   Prima di procedere, attendere alcuni minuti che la distribuzione del database venga completata.

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/
