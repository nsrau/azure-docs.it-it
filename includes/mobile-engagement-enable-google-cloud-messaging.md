
###Creare un progetto Google Cloud Messaging con chiave API

>[AZURE.NOTE] Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Passare a [Google Cloud Console](https://console.developers.google.com/project), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create Project**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Immettere un nome di progetto, accettare le condizioni per l'utilizzo del servizio, quindi fare clic su **Create**. Se necessario, eseguire la verifica SMS, quindi fare nuovamente clic su **Create**.

3. Prendere nota del numero di progetto nella sezione **Projects**. Verrà usato più avanti nell'esercitazione per popolare il file manifesto Android.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. Nella colonna di sinistra espandere **APIs & auth**, fare clic su **APIs**, quindi scorrere verso il basso e fare clic su **Cloud Messaging for Android**. Nella pagina successiva fare clic su **Enable API** e accettare le condizioni per l'uso del servizio.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Fare clic su **Credentials**, quindi su **Add Credential**->**API Key**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. In **Create a new key** fare clic su **Server key**. Nella finestra successiva fare clic su **Create**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Prendere nota del valore di **API KEY**. Questo valore della chiave dell'API verrà usato più avanti per la configurazione nella sezione relativa al push nativo.

<!---HONumber=AcomDC_0330_2016-->