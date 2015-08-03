I passaggi seguenti mostrano il processo per la creazione di un'app Dropbox usando il sito Dropbox.com. Poiché il sito Dropbox.com potrebbe subire modifiche senza preavviso, è possibile che l'interfaccia utente sia diversa da quanto mostrato.

1. Passare al [portale per sviluppatori Dropbox](https://www.dropbox.com/developers/apps), fare clic su **App Console** e infine su **Create App**.

	![Creare un'app Dropbox](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. Scegliere **Dropbox API app** e configurare le altre impostazioni.
 
	Le opzioni di accesso ai file mostrate nella schermata seguente permetteranno di testare l'accesso all'account Dropbox con una semplice richiesta HTTP Get, se l'account include file.

	Specificare come nome dell'app per le API Dropbox un valore qualsiasi accettato dal sito Dropbox.

3. Fare clic su **Create app**.

	![Creare un'app Dropbox](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	La pagina successiva mostra le impostazioni relative alla chiave e al segreto dell'app (ID client e Segreto client in Azure) che verranno usate per configurare il connettore Dropbox per Azure.

	Questa pagina include anche un campo in cui è possibile immettere un URI di reindirizzamento, il cui valore verrà fornito nella sezione successiva.

	![Creare un'app Dropbox](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=July15_HO4-->