

Dopo l'esecuzione del test dell'app di Windows Store rispetto al servizio mobile locale, la fase finale dell'esercitazione consiste nel pubblicare il servizio mobile in Azure e nell'eseguire l'app rispetto al servizio attivo.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto relativo al servizio mobile, quindi scegliere **Pubblica**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)

    La finestra di dialogo Pubblica sito Web viene visualizzata.

2.  Fare clic su **Importa**, quindi su **Sfoglia**, passare al percorso in cui è stato salvato in precedenza il file del profilo di pubblicazione, selezionare tale file e infine fare clic su **OK**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-import-profile.png)

    Verranno caricate le informazioni necessarie a Visual Studio per la pubblicazione del servizio mobile in Azure.

    **Nota sulla sicurezza**

    Dopo l'importazione del profilo di pubblicazione, è consigliabile eliminare il file pubblicato, in quanto contiene informazioni che possono essere utilizzate da altri utenti per accedere ai servizi.

3.  Fare clic su **Convalida connessione** per verificare che la pubblicazione sia stata configurata correttamente, quindi fare clic su **Pubblica**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

    Se la pubblicazione ha esito positivo, verrà visualizzata di nuovo la pagina di conferma, che indica che il servizio mobile è attivo e in esecuzione, questa volta in Azure.


