
1. Passare a [Google Cloud Console](https://console.developers.google.com/project) ed eseguire l'accesso con le credenziali dell'account Google. 
 
2. Fare clic su **Crea progetto**, digitare un nome di progetto e quindi fare clic su **Crea**. Se necessario, eseguire la verifica SMS, quindi fare nuovamente clic su **Crea**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 Digitare il nome desiderato in **Nome progetto** e fare clic su **Crea progetto**.

3. Prendere nota del numero di progetto nella sezione **Projects**. Sarà necessario impostare questo valore come variabile *PROJECT\_ID* nel client.

4. Nel dashboard del progetto fare clic su **Usa API Google** > **Cloud Messaging for Android** e, nella pagina successiva, fare clic su **Abilita API**.

5. Nello strumento di gestione delle API fare clic su **Credenziali** > **Aggiungi credenziali** > **Chiave API**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. In **Crea una nuova chiave** fare clic su **Chiave server**, digitare un nome per la chiave e quindi fare clic su **Crea**.

7. Prendere nota del valore di **API KEY**.

	Questo valore della chiave dell'API verrà usato successivamente per abilitare Azure per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!---HONumber=AcomDC_1203_2015-->