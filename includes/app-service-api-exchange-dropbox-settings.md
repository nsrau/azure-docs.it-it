4. In un'altra finestra o scheda del browser passare al [portale di anteprima di Azure](https://portal.azure.com).

3. Passare al pannello **App per le API** per il connettore Dropbox. Se è ancora visualizzato il pannello **Gruppo di risorse**, è sufficiente fare clic sul connettore Dropbox nel diagramma.

4. Fare clic su **Impostazioni** e nel pannello **Impostazioni** fare clic su **Autenticazione**.

	![Fare clic su Impostazioni](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![Fare clic su Autenticazione](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. Nel pannello Autenticazione immettere l'ID client e il segreto client dal sito Dropbox e quindi fare clic su **Salva**.

	![Immettere le impostazioni e fare clic su Salva](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Copiare l'**URI di reindirizzamento** (casella grigia sopra l'ID client e il segreto client) e aggiungere il valore alla pagina lasciata aperta nel passaggio precedente.

	L'URI di reindirizzamento usa il modello seguente:

		[gatewayurl]/api/consent/redirect/[connectorname]

	Ad esempio:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Ottenere l'URI di reindirizzamento](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Creare un'app Dropbox](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=July15_HO3-->