
1. Nel [Portale di Azure](https://portal.azure.com/) fare clic su **Browse All** (Esplora tutto)  > **Servizi app** e quindi scegliere il back-end dell'app per dispositivi mobili. In **Impostazioni** fare clic su **App Service Push** (Push servizio app), quindi fare clic sul nome dell'hub di notifica.
2. Passare a **Google (GCM)**, immettere il valore **Chiave server** ottenuto da Firebase nella procedura precedente, quindi fare clic su **Salva**.

    ![Impostare la chiave API di GCM nel portale](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Il back-end dell'app per dispositivi mobili è ora configurato per l'uso di Firebase Cloud Messaging. Questo consente di inviare notifiche push all'app in esecuzione su un dispositivo Android usando l'hub di notifica.

<!-- URLs. -->


<!-- images -->


<!--HONumber=Dec16_HO2-->


