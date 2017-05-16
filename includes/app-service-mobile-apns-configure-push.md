

1. In Mac avviare **Keychain Access**. Aprire **My Certificates** (Certificati personali) in **Categoria** nella barra di spostamento sinistra. Trovare il certificato SSL scaricato nella sezione precedente e divulgare il contenuto. Selezionare solo il certificato (non selezionare la chiave privata) ed [esportarlo](https://support.apple.com/kb/PH20122?locale=en_US).
2. Nel [Portale di Azure](https://portal.azure.com/) fare clic su **Browse All** (Esplora tutto)  > **Servizi app** e quindi scegliere il back-end dell'app per dispositivi mobili. In **Impostazioni** fare clic su **App Service Push** (Push servizio app), quindi fare clic sul nome dell'hub di notifica. Andare a **Apple Push Notification Services** > **Carica certificato**. Caricare il file con estensione p12, selezionando la **modalità** corretta, a seconda che il certificato SSL client di prima sia di produzione o sandbox. Salvare le modifiche.

Il servizio è ora configurato per l'uso con le notifiche push in iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
