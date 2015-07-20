In questa sezione si utilizzerà Visual Studio per ospitare il servizio mobile in locale nella workstation di sviluppo in IIS Express. Si eseguirà quindi il test dell'app e del servizio back-end.


1. In Visual Studio premere F7 o scegliere **Compila soluzione** dal menu **Compila** per compilare sia l'app di Windows Store che il servizio mobile. Verificare che entrambi i progetti vengano compilati senza errori nella finestra di output di Visual Studio

2. In Visual Studio premere F5 o scegliere **Avvia debug** dal menu **Debug** per eseguire l'app e ospitare il servizio localmente in IIS Express.

 
3. Immettere il testo di un nuovo oggetto todoitem. Fare quindi clic su **Salva**. Un nuovo elemento TodoItem verrà inserito nel database creato dal servizio mobile ospitato localmente in IIS Express.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. Fare clic sulla casella di controllo relativa a uno degli elementi per contrassegnarlo come completato.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. In Visual Studio è possibile visualizzare le modifiche nel database creato per il servizio back-end aprendo Esplora server ed espandendo Connessioni dati. Fare clic con il pulsante destro del mouse sulla tabella TodoItems in **MS_TableConnectionString** e scegliere **Mostra dati tabella**

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

<!---HONumber=July15_HO2-->