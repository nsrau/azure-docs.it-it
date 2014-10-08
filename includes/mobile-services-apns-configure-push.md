Dopo aver registrato l'app con APNS e aver configurato il progetto, è necessario configurare il servizio mobile in modo che si integri con APNS.

1.  In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, nominare il file QuickstartPusher, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

Annotare il nome del file e il percorso del certificato esportato.

> [WACOM.NOTE] In questa esercitazione viene creato un file QuickstartPusher.p12. Il nome e il percorso del file potrebbero essere diversi.

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][1]

2.  Fare clic sulla scheda **Push** e quindi su **Upload**.

    ![][2]

    Verrà visualizzata la finestra di dialogo Upload Certificate.

3.  Fare clic su **File**, selezionare il file QuickstartPusher.p12 del certificato esportato, immettere la password in **Password**, verificare che in **Mode** sia selezionata la modalità corretta, fare clic sull'icona del segno di spunta e quindi su **Save**.

    ![][3]

    > [WACOM.NOTE] In questa esercitazione vengono utilizzati certificati di sviluppo.

Il servizio mobile è ora configurato per funzionare con APNS.

<!-- URLs. -->

   
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
