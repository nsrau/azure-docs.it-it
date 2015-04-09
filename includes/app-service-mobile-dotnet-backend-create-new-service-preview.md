

Per creare una nuova app mobile, attenersi alla procedura seguente.

1. Accedere al [Portale di Azure]. In basso a sinistra nella finestra fare clic su **+NUOVO**. Scorrere fino all'elemento **App mobile**.

   ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/new-mobile-app.png)

   Verrà visualizzata la finestra di dialogo **Nuova app mobile**.

2. Digitare un nome per l'app mobile. Tale nome deve essere lungo almeno 8 caratteri e contenere lettere minuscole dalla a alla z.  

7. Scegliere un'area, In questa esesercitazione verrà utilizzata l'area **South Central US**.

   > [AZURE.NOTE] Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riusare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa area del nuovo backend di app mobile si dispone già di un database, è possibile scegliere **Utilizza database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in un'area diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze più elevate.

3. Selezionare la propria sottoscrizione.

4. Creare un nuovo gruppo di risorse con lo stesso nome dell'app mobile.

5. In **Impostazioni pacchetto**, selezionare **USERDATABASE**, è possibile scegliere un database esistente o crearne uno nuovo. Per creare un nuovo database, digitare il nome del nuovo **database**, creare un nuovo **server**, digitare il nome di tale server, quindi scegliere un **nome di accesso**, che è il nome di accesso dell'amministratore per il nuovo server di database SQL, digitare e confermare la password e fare clic sul pulsante Ok per completare la procedura. Se so seleziona un database esistente, è necessario fornire una **password di amministratore server**.

   ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create-db.png)

6. Creare un nuovo piano di webhosting con lo stesso nome dell'app mobile.

   > [AZURE.NOTE] Digitare, non copiare e incollare, il nome del piano di webhosting. Questo è il campo di convalida del nome, se il nome non viene digitato non viene convalidato. Non è necessario che corrisponda esattamente al nome del sito Web (ma deve seguire le stesse regole).

8. Selezionare un piano tariffario. In questa esercitazione sarà utilizzato il piano **Standard 1**.

   La pagina delle impostazioni della nuova app mobile apparirà simile alla seguente:

   ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create.png)

9. Fare clic sul pulsante **Crea** in fondo al pannello. Sarà avviata la distribuzione nella finestra di notifica.


A questo punto è stata creata un nuovo backend di app mobile che può essere usato dalle app per dispositivi mobili.

> [AZURE.NOTE] Una volta creata l'app mobile, spostarsi nel portale sul server sql creato (accertarsi di selezionare il sever e non il db sql azure). Da qui, fare clic sulla parte delle impostazioni, espandere la parte del firewall e cambiare "Consenti accesso ai servizi Azure". In caso contrario, l'applicazione non funziona.

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com/

<!--HONumber=49-->