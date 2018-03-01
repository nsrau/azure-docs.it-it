Poiché le funzioni delle app per dispositivi mobili del Servizio app di Azure usano [Hub di notifica di Azure] per inviare push, si dovrà configurare un hub di notifica per l'app per dispositivi mobili.

1. Nel [portale di Azure] passare a **Servizi app** e quindi selezionare il back-end dell'app. In **Impostazioni** selezionare **Push**.
2. Per aggiungere una risorsa hub di notifica all'app selezionare **Connetti**. È possibile creare un hub o connettersi a uno esistente.

    ![Configurare un hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

A questo punto un hub di notifica è stato connesso al progetto di back-end dell'app per dispositivi mobili. In seguito si configurerà questo hub di notifica per la connessione a un sistema PNS (Platform Notification System) per eseguire il push ai dispositivi.

[portale di Azure]: https://portal.azure.com/
[Hub di notifica di Azure]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
