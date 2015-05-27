Dopo aver registrato l'app con APNS e aver configurato il progetto, è necessario configurare l'app mobile in modo che si integri con APNS.

1. In accesso portachiavi, fare clic sul nuovo certificato dell'applicazione delle Guide rapide in **Chiavi** o **Certificati personali**, fare clic su **Esporta**, assegnare al file il nome QuickstartPusher, selezionare il formato **. p12** e quindi fare clic su **Salva**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Annotare il nome del file e il percorso del certificato esportato.

>[AZURE.NOTE]In questa esercitazione viene creato un file QuickstartPusher.p12. Il nome e il percorso del file potrebbero essere diversi.

2. Accedere al [Portale di anteprima di Azure], selezionare **Sfoglia**, **App per dispositivi mobili** e quindi fare clic sull'app. Fare clic su Servizi delle notifiche Push.

3. In Servizio di notifica Push di Apple, caricare il certificato con il file **.p12**, la password associata e selezionare la modalità desiderata.

L'app modile di Servizio app è ora configurata per funzionare con APNS.

<!-- URLs. -->
[Portale di anteprima di Azure]: https://portal.azure.com/

<!--HONumber=54-->