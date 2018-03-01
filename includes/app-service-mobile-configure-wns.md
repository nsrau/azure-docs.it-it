
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Esplora tutto** > **Servizi app**. Selezionare quindi il back-end di App per dispositivi mobili. In **Impostazioni** selezionare **App Service Push** (Push servizio app). Selezionare quindi il nome dell'hub di notifica.
2. Passare a **Windows (WNS)**. Immettere quindi la **chiave di sicurezza** (segreto client) e il **SID pacchetto** ottenuti dal sito dei servizi Live. Selezionare quindi **Salva**.

    ![Impostare la chiave WNS nel portale](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Il back-end ora è configurato per poter inviare le notifiche push tramite WSN.
