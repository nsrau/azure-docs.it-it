Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1.  Nel menu **Run** scegliere **Run** per avviare il progetto.

    L'app, compilata tramite Android SDK che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

2.  Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. È anche possibile esaminare il database nel portale di gestione di Azure. I due passaggi successivi illustrano come eseguire questa operazione per visualizzare le modifiche nel database.

3.  Nel portale di gestione di Azure fare clic su Manage per il database associato al servizio mobile.

    ![][]

4.  Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da usare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![][1]

L'esercitazione **Introduzione ai dati** per Android è terminata.

  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png
