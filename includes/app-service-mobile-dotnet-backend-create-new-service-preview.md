

Per creare una nuova app mobile, attenersi alla procedura seguente.

1. Accedere al [portale di Azure]. Nella parte inferiore sinistra della finestra fare clic su **+NUOVO**. Scorrere fino all'elemento **App per dispositivi mobili**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/new-mobile-app.png)

    Verrà visualizzato il pannello **Nuova app per dispositivi mobili**.

2. Digitare un nome per l'app per dispositivi mobili. Tale nome deve essere lungo almeno 8 caratteri e contenere lettere minuscole dalla a alla z.

7. Scegliere un'area, In questa esercitazione verrà usata l'area **Stati Uniti centro-meridionali**.

    > [AZURE.NOTE]Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa area del nuovo back-end dell'app per dispositivi mobili è già presente un database, è possibile scegliere **Utilizza database esistente** e quindi selezionare questo database. Non è consigliabile utilizzare un database in un'area diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze più elevate.

3. Selezionare la propria sottoscrizione.

4. Creare un nuovo gruppo di risorse con lo stesso nome dell'app mobile.

5. In **Impostazioni pacchetto** selezionare **USERDATABASE**, quindi scegliere un database esistente o crearne uno nuovo. Per creare un nuovo database, digitare il nome del nuovo **database**, creare un nuovo **server**, digitare il nome del server, quindi scegliere un **nome di accesso**, ovvero il nome di accesso dell'amministratore per il nuovo server di database SQL, digitare e confermare la password e infine fare clic sul pulsante OK per completare la procedura. Se si seleziona un database esistente, è necessario specificare una **password di amministratore del server**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create-db.png)

6. Creare un nuovo piano di webhosting con lo stesso nome dell'app per dispositivi mobili.

    > [AZURE.NOTE]Digitare, non copiare e incollare, il nome del piano di webhosting. Questo è il campo di convalida del nome, se il nome non viene digitato non viene convalidato. Non è necessario che corrisponda esattamente al nome del sito Web (ma deve seguire le stesse regole).

8. Selezionare un piano tariffario. In questa esercitazione si userà il piano **Standard 1**.

    La pagina delle impostazioni della nuova app per dispositivi mobili apparirà simile alla seguente:

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create.png)

9. Fare clic sul pulsante **Crea** nella parte inferiore del pannello. Nella finestra di notifica verrà visualizzato l'avvio della distribuzione.

A questo punto è stato creato un nuovo backend che può essere usato dalle app per dispositivi mobili.

> [AZURE.NOTE]Una volta creata l'app per dispositivi mobili, spostarsi nel portale sul server sql creato (accertarsi di selezionare il server e non il db sql azure). Da qui, fare clic sulla parte delle impostazioni, espandere la parte del firewall e cambiare "Consenti accesso ai servizi Azure". Se non si esegue questa operazione, l'applicazione non funziona.

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/

<!---HONumber=July15_HO4-->