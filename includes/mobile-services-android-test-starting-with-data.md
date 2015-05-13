Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Nel menu **Run** scegliere **Run** per avviare il progetto.

	L'app, compilata tramite Android SDK che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   Un nuovo elemento verrà inviato come inserimento al servizio mobile.

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos mediante el Portal de administración de Azure:  en los dos pasos siguientes se hará esto para ver los cambios en la base de datos.


4. Nel portale di gestione di Azure fare clic su Manage per il database associato al servizio mobile.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da usare sarà simile alla seguente, ma anziché  `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

L'esercitazione **Introduzione ai dati** per Android è terminata. 

<!--HONumber=52-->
