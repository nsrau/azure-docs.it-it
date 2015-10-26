
* Eseguire i passaggi illustrati nella pagina relativa all'[installazione dell'identità di firma SSL di un client nel server](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) per esportare il certificato scaricato nel passaggio precedente in un file con estensione p12.

* Nel portale di Azure, fare clic su **Servizi mobili** > app personale > scheda **Push** > **impostazioni di notifica push di apple** > "**Carica**. Caricare il file con estensione p12, assicurandosi che sia selezionata la **Modalità** corretta, ovvero Sandbox o Produzione, in base alla generazione di un certificato SSL di tipo Sviluppo o Distribuzione. Il servizio è ora configurato per l'uso con le notifiche push in iOS.

<!---HONumber=Oct15_HO3-->