Poiché le funzioni delle app per dispositivi mobili del Servizio app di Azure usano [Hub di notifica di Azure] per inviare push, si dovrà configurare un hub di notifica per l'app per dispositivi mobili.

1. Nel [Portale di Azure] andare in **Servizi app**, quindi fare clic sul back-end dell'app. In **Impostazioni** fare clic su **Push**.
2. Fare clic su **Connetti** per aggiungere una risorsa dell'hub di notifica all'app. È possibile creare un hub o connettersi a uno esistente.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

A questo punto un hub di notifica è stato connesso al progetto di back-end dell'app per dispositivi mobili. In seguito si configurerà l'hub di notifica per la connessione a un Platform Notification System (PNS) per effettuare il push ai dispositivi.

[Portale di Azure]: https://portal.azure.com/
[Hub di notifica di Azure]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/


<!--HONumber=Dec16_HO2-->


