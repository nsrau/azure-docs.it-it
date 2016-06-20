
###Creare un progetto Google Cloud Messaging con chiave API

>[AZURE.NOTE] Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Passare a [Google Cloud Console](https://console.developers.google.com/project) ed eseguire l'accesso con le credenziali dell'account Google.

2. Fare clic su **Go to project** (Vai al progetto), quindi fare clic su **Create Project** (Crea progetto).
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Immettere un nome di progetto.

4. Prendere nota del numero di progetto visualizzato sotto la casella di testo **Project name** (Nome progetto). Verrà usato più avanti nell'esercitazione per popolare il file manifesto Android. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. Fare clic su **Create**.

6. Nella colonna di sinistra, assicurarsi che sia selezionata la voce **Overview** (Panoramica) e quindi fare clic su **Google Cloud Messaging** sotto Mobile APIs (API per dispositivi mobili). Quindi, nella pagina successiva fare clic su **Enable** (Abilita).

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. Nella pagina successiva fare clic su **Go to Credentials** (Vai alle credenziali). Nella pagina seguente selezionare **Google Cloud Messaging** nella prima casella a discesa e **Web server** (Server Web) nella seconda e quindi fare clic su **What credentials do I need?** (Credenziali necessarie).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. Nella pagina **Add credentials to your project** (Aggiungi le credenziali al progetto) fare clic su **Create API key** (Crea chiave API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Prendere nota del valore di **API KEY**. Questo valore della chiave dell'API verrà usato più avanti per la configurazione nella sezione relativa al push nativo. Fare clic su **Done** (Fine).

<!---HONumber=AcomDC_0608_2016-->