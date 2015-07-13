

>[AZURE.NOTE]Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Passare al sito Web <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create Project**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]Se si dispone già di un progetto esistente, dopo l'accesso viene visualizzata la pagina <strong>Projects</strong>. Per creare un nuovo progetto dalla pagina Dashboard, espandere <strong>API Project</strong>, fare clic su <strong>Create</strong> in <strong>Other projects</strong> e quindi immettere un nome di progetto e fare clic su <strong>Create project</strong>.

2. Immettere un nome di progetto, accettare le condizioni per l'utilizzo del servizio, quindi fare clic su **Create**. Se necessario, eseguire la verifica SMS, quindi fare nuovamente clic su **Create**.

3. Prendere nota del numero di progetto nella sezione **Projects**.

	Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT_ID nel client.

4. Nella colonna a sinistra espandere **APIs & auth**, fare clic su **APIs**, quindi scorrere verso il basso e fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android** e accettare le condizioni per l'uso del servizio.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Fare clic su **Credentials** e quindi su **Create new Key**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. In **Create a new key** fare clic su **Server key**. Nella finestra successiva fare clic su **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Prendere nota del valore di **API KEY**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png)

	Questo valore della chiave dell'API verrà usato successivamente per abilitare Azure per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!---HONumber=62-->