
1. Passare a [Google Cloud Console](https://console.developers.google.com/project) ed eseguire l'accesso con le credenziali dell'account Google. 
2. Fare clic su **Crea progetto**, digitare un nome di progetto e quindi fare clic su **Crea**. Se necessario, eseguire la verifica SMS, quindi fare nuovamente clic su **Crea**.
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)
   
     Digitare il nome desiderato in **Nome progetto** e fare clic su **Crea progetto**.
3. Fare clic sul pulsante **Utilities and More** e quindi su **Project Information**. Prendere nota del valore di **Project Number**. Sarà necessario impostare questo valore come variabile `SenderId` nell'app client.
   
       ![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. Nel dashboard del progetto fare clic su **Google Cloud Messaging** (Messaggistica cloud di Google) in **Mobile APIs** (API per dispositivi mobili), quindi nella pagina successiva fare clic su **Enable API** (Abilita API) e accettare le condizioni del servizio.
   
    ![Abilitare GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Abilitare GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png)
5. Nel dashboard del progetto fare clic su **Credentials** (Credenziali) > **Create Credential** (Crea credenziale) > **API Key** (Chiave API).
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. In **Crea una nuova chiave** fare clic su **Chiave server**, digitare un nome per la chiave e quindi fare clic su **Crea**.
7. Prendere nota del valore di **API KEY**.
   
    Questo valore della chiave dell'API verrà usato successivamente per abilitare Azure per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!---HONumber=AcomDC_0608_2016-->