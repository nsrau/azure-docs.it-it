1. Accedere al [portale di Azure].

2. Nella parte superiore sinistra della finestra fare clic su **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end dell'app per dispositivi mobili.

3. Nella casella **Gruppo di risorse** selezionare un gruppo di risorse esistente. Se non sono disponibili gruppi di risorse, immettere lo stesso nome dell'app.
 
	A questo punto viene selezionato il piano predefinito del servizio app, ovvero il livello gratuito. Le impostazioni del piano del servizio app determinano la posizione, le funzionalità, i costi e le risorse di calcolo associati all'app. È possibile selezionare un altro piano del servizio app o crearne uno nuovo. Per altre informazioni sui piani dei servizi app e su come creare un nuovo piano, vedere [Panoramica approfondita dei piani del servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Usare il piano predefinito del servizio app, selezionare un piano diverso o [creare un nuovo piano](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan), quindi fare clic su **Crea**.
	
	Verrà creato il back-end dell'app per dispositivi mobili. In seguito il progetto server verrà distribuito nel back-end. Il provisioning del back-end dell'app per dispositivi mobili può richiedere alcuni minuti. Il pannello **Impostazioni** per il back-end dell'app per dispositivi mobili viene visualizzato al completamento del provisioning. Prima di potere usare il back-end dell'app per dispositivi mobili, è necessario definire anche una connessione a un archivio dati.

    > [AZURE.NOTE]Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa posizione del nuovo back-end dell'app per dispositivi mobili è già presente un database, è possibile scegliere **Usare un database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in una posizione diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze più elevate. Sono disponibili altre opzioni di archiviazione dei dati.

6. Nel pannello **Impostazioni** per il nuovo back-end dell'app per dispositivi mobili scorrere verso il basso fino alla voce relativa ai dispositivi mobili, quindi fare clic su **Attività iniziali** > piattaforma app client > **Connetti al database**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Nel pannello **Aggiungi connessione dati** fare clic su **Database SQL** > **Crea un nuovo database**, immettere il **Nome** del database, scegliere un piano tariffario, quindi fare clic su **Server**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Nel pannello **Nuovo server** immettere un nome server univoco nel campo **Nome server**, specificare valori sicuri per **Nome di accesso amministratore server** e **Password**, verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata, quindi fare due volte clic su **OK**. Verranno creati il nuovo database e il server.

10. Nel pannello **Aggiungi connessione dati** fare clic su **Stringa di connessione**, immettere i valori per l'accesso e la password del database, quindi fare due volte clic su **OK**.

	La creazione del database può richiedere alcuni minuti. Usare l'area **Notifiche** per monitorare l'avanzamento della distribuzione. Per continuare occorre attendere il completamento corretto della distribuzione del database.


<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1125_2015-->