
1. Nel [portale di Azure](https://azure.portal.com/), fare clic su **Sfoglia** > **Servizi app**, quindi individuare e fare clic sul back-end dell'App per dispositivi mobili > **Tutte le impostazioni**, quindi in **Dispositivi mobili** fare clic su **Push**.

2. In Servizio di notifica Push fare clic su **Windows (WNS)** immettere la **Chiave di sicurezza** (chiave privata client) e **SID di pacchetto** ottenuti dal sito di servizi Live, quindi fare clic su **Salva**.

    ![Impostare la chiave API di GCM nel portale](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Il back-end dell'app per dispositivi mobili è ora configurato per usare WNS per inviare notifiche push a un'app di Windows tramite l'hub di notifica.

<!---HONumber=AcomDC_1203_2015-->