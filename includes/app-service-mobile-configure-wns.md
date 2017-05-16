
1. Nel [Portale di Azure](https://portal.azure.com/) fare clic su **Browse All** (Esplora tutto)  > **Servizi app** e quindi scegliere il back-end dell'app per dispositivi mobili. In **Impostazioni** fare clic su **App Service Push** (Push servizio app), quindi fare clic sul nome dell'hub di notifica.
2. Andare in **Windows (WNS)**, immettere la **chiave di sicurezza** (chiave privata client) e il **SID di pacchetto** ottenuti dal sito di servizi Live, quindi fare clic su **Salva**.

    ![Impostare la chiave WNS nel portale](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Il back-end ora è configurato per poter inviare le notifiche push tramite WSN.
