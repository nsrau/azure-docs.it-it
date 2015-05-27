Dopo aver registrato l'app con APNS e aver configurato il progetto, è necessario configurare il servizio mobile in modo che si integri con APNS.

1. In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato dell'app di guida introduttiva in **Chiavi** o **Certificati**, scegliere **Esporta**, nominare il file QuickstartPusher, selezionare il formato **.p12** e quindi fare clic su **Salva**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  Annotare il nome del file e il percorso del certificato esportato.

>[AZURE.NOTE]In questa esercitazione viene creato un file QuickstartPusher.p12. Il nome e il percorso del file potrebbero essere diversi.

2. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. Fare clic sulla scheda **Push** e quindi su **Carica**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	Verrà visualizzata la finestra di dialogo Carica certificato.

4. Fare clic su **File**, selezionare il file QuickstartPusher.p12 del certificato esportato, immettere la password in **Password**, verificare che in **Modalità** sia selezionata la modalità corretta, fare clic sull'icona del segno di spunta e quindi su **Salva**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [AZURE.NOTE]Questa esercitazione usa certificati di sviluppo.

Il servizio mobile è ora configurato per l'uso con APNS.

<!-- URLs. -->
[portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=54-->