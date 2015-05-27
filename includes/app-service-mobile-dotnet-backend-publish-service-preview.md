Dopo avere testato l'app client rispetto all'app mobile locale, la fase finale dell'esercitazione consiste nel pubblicare il backend dell'app mobile in Azure e nell'eseguire l'app rispetto al servizio attivo.

> [AZURE.NOTE]Questa procedura mostra come pubblicare il backend dell'app mobile mediante gli strumenti di Visual Studio. È anche possibile pubblicare il backend .NET usando il controllo del codice sorgente.

1. In Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto di codice di app per dispositivi mobili (è il nome dell'app con il suffisso "Service") e selezionare **Pubblica**. 

	![Selezionare pubblica sul progetto di codice app](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. Nella finestra di dialogo **Pubblica sito Web**, selezionare **App per dispositivi mobili di Azure** come destinazione di pubblicazione. Nella finestra di dialogo visualizzata, selezionare l'app mobile esistente, che sarà il nome dell'app mobile con il suffisso "code".

    ![Selezionare un'app Web esistente in cui effettuare la pubblicazione](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. Fare clic su **Convalida connessione** per verificare che la pubblicazione sia stata configurata correttamente, quindi fare clic su **Pubblica**.

	![Pagina finale della procedura guidata per le impostazioni di pubblicazione](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   Se la pubblicazione ha esito positivo, verrà visualizzata la pagina di conferma, che indica che il backend dell'app mobile è attivo e in esecuzione in Azure. La finestra dei risultati di Visual Studio indicherà l'esito positivo.

<!--HONumber=54-->