Poiché le app per dispositivi mobili del servizio app usano [Hub di notifica] per inviare push, si dovrà configurare un hub di notifica per l'app per dispositivi mobili.

1. Nel [portale di Azure] andare a **Esplora** > **Servizi app**, quindi fare clic sul back-end dell'app. In **Impostazioni** fare clic su **App Service Push** (Push servizio app).
2. Fare clic su **Configura** per configurare un hub di notifica. È possibile creare un hub o connettersi a uno esistente.
   
    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

A questo punto un hub di notifica è stato connesso al back-end dell'app per dispositivi mobili. In seguito si configurerà l'hub di notifica per la connessione a un Platform Notification System (PNS) per effettuare il push ai dispositivi.

[portale di Azure]: https://portal.azure.com/
[Hub di notifica]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/

<!--HONumber=Nov16_HO3-->


