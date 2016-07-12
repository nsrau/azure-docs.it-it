1. Accedere al [portale di Azure].

2. Fare clic su **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili**, quindi specificare un nome per il back-end dell'app per dispositivi mobili.

3. Per **Gruppo di risorse** selezionare un gruppo di risorse esistente o crearne uno nuovo usando lo stesso nome dell'app.
 
	È possibile selezionare un altro piano del servizio app o crearne uno nuovo. Per altre informazioni sui piani di Servizi app e su come creare un nuovo piano in un piano tariffario diverso e nella località preferita, vedere [Panoramica approfondita dei piani del servizio app di Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Per **Piano di Servizio app**, viene selezionato il piano predefinito nel [Piano Standard](https://azure.microsoft.com/pricing/details/app-service/). È anche possibile selezionare un piano diverso o [crearne uno nuovo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Le impostazioni del piano di servizio app determinano [località, funzionalità, risorse di calcolo e costi](https://azure.microsoft.com/pricing/details/app-service/) associati all'app.

	Dopo avere scelto il piano, fare clic su **Crea**. Verrà creato il back-end dell'app per dispositivi mobili.
	
6. Nel pannello **Impostazioni** relativo al nuovo back-end dell'app per dispositivi mobili, fare clic su **Avvio rapido** > piattaforma dell'app client > **Connetti al database**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Nel pannello **Aggiungi connessione dati** fare clic su **Database SQL** > **Crea un nuovo database**, immettere il **Nome** del database, scegliere un piano tariffario, quindi fare clic su **Server**. È possibile riutilizzare questo nuovo database. Se si ha già un database nella stessa località, è possibile scegliere **Usare un database esistente**. Non è consigliabile usare un database in una località diversa a causa dei costi di larghezza di banda e della latenza più elevata.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Nel pannello **Nuovo server** digitare un nome del server univoco nel campo **Nome server**, specificare un account di accesso e una password, selezionare **Consenti ai servizi di Azure di accedere al server** e fare clic su **OK**. Verrà creato il nuovo database.

9. Nel pannello **Aggiungi connessione dati** fare clic su **Stringa di connessione**, immettere i valori per l'account di accesso e la password del database e fare clic su **OK**. Prima di procedere, attendere alcuni minuti che a distribuzione del database venga completata.

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0706_2016-->