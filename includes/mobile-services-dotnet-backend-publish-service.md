

Dopo avere testato l'app rispetto al servizio mobile locale, la fase finale dell'esercitazione consiste nel pubblicare il servizio mobile in Azure e nell'eseguire l'app rispetto al servizio attivo.

>[AZURE.NOTE]Questa procedura mostra come pubblicare il servizio mobile mediante gli strumenti di Visual Studio. È anche possibile pubblicare il servizio mobile back-end .NET usando il controllo del codice sorgente. Per altre informazioni, vedere [Archiviare il codice del progetto nel controllo del codice sorgente](/it-it/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/).

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto relativo al servizio mobile, quindi scegliere **Pubblica**. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Servizi mobili di Azure**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)
	
2. Effettuare l'accesso con le credenziali dell'account Azure, selezionare il servizio in **Servizi mobili esistenti** e quindi fare clic su **OK**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

	Visual Studio scaricherà le impostazioni di pubblicazione direttamente da Azure.

	>[AZURE.NOTE]Visual Studio mantiene in archivio le credenziali di Azure fino a quando non viene eseguita la disconnessione in modo esplicito.

3. Fare clic su **Convalida connessione** per verificare che la pubblicazione sia stata configurata correttamente, quindi fare clic su **Pubblica**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Se la pubblicazione ha esito positivo, verrà visualizzata di nuovo la pagina di conferma, che indica che il servizio mobile è attivo e in esecuzione, questa volta in Azure.

<!--HONumber=42-->
