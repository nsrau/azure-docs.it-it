
1. In Mac avviare **Keychain Access**. Aprire **Category** > **My Certificates**. Trovare il certificato SSL, scaricato in precedenza, per l'esportazione e la divulgazione del relativo contenuto. Selezionare solo il certificato, ma non la chiave privata, ed [esportarlo](https://support.apple.com/kb/PH20122?locale=en_US).
2. Nel portale di Azure fare clic su **Esplora tutto** > **App per dispositivi mobili** > back-end > **Impostazioni** > **App per dispositivi mobili** > **Push** > **Configura le impostazioni obbligatorie** > **+ Hub di notifica** e specificare un nome e uno spazio dei nomi per l'hub di notifica, quindi fare clic su **OK**.
   
   ![][1]
3. Nel pannello per **creare l'hub di notifica** fare clic su **Crea**.
   
    Prima di procedere al passaggio successivo fare clic su **Notifiche**, per assicurarsi che l'installazione dell'hub di notifica sia stata completata. 
4. Nel portale di Azure fare clic su **Esplora tutto** > **App per dispositivi mobili** > back-end > **Impostazioni** > **App per dispositivi mobili** > **Push** > **Servizi di notifica push Apple** > **Carica certificato**. Caricare il file con estensione p12, selezionando la **Modalità** corretta, in base alla generazione di un certificato SSL di tipo Sviluppo o Distribuzione. Il servizio è ora configurato per l'uso con le notifiche push in iOS.

[1]: ./media/app-service-mobile-apns-configure-push-preview/mobile-push-notification-hub.png

<!---HONumber=Oct15_HO3-->