
###Creare un progetto Google Cloud Messaging con chiave API

>[AZURE.NOTE] Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Passare a [Google Cloud Console](https://console.developers.google.com/project) ed eseguire l'accesso con le credenziali dell'account Google.

2. Passare a **All Projects** (Tutti i progetti) e quindi fare clic su **Create Project** (Crea progetto).

3. Immettere un nome in **Project name** (Nome progetto) e fare clic su **Create** (Crea)

4. Dopo aver creato il progetto, prendere nota del valore in **Project number** (Numero progetto), che sarà un valore numerico lungo. Questo dato è disponibile nella sezione **IAM & Admin** (IAM e amministrazione) nell'area **Settings** (Impostazioni) del progetto e sarà necessario in un secondo momento.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. A questo punto verrà creata una chiave per la piattaforma Google Cloud Messaging che verrà usata dalla piattaforma dell'utente per inviare notifiche ai dispositivi Android. Passare alla sezione **API Manager** (Gestione API) e fare clic su **Google Cloud Messaging** in **Mobile APIs** (API per dispositivi mobili).

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. Nella pagina successiva fare clic su **Enable** (Abilita). Il dashboard chiederà di creare le credenziali. Fare quindi clic sul pulsante **Go to Credentials** (Vai alle credenziali).

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Nella pagina seguente selezionare **Google Cloud Messaging** nella prima casella a discesa e **Web server** (Server Web) nella seconda e quindi fare clic su **What credentials do I need?** (Credenziali necessarie).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. Nella pagina **Add credentials to your project** (Aggiungi le credenziali al progetto) fare clic su **Create API key** (Crea chiave API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Prendere nota del valore di **API KEY**. Questo valore della chiave dell'API verrà usato più avanti per la configurazione nella sezione relativa al push nativo. Fare clic su **Done** (Fine).

<!---HONumber=AcomDC_0622_2016-->