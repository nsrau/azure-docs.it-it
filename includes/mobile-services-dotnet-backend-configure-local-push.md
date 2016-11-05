
In alternativa, è possibile testare le notifiche push con il servizio mobile in esecuzione nel computer locale o nella macchina virtuale prima di pubblicarlo in Azure. Per eseguire questa operazione, è necessario impostare le informazioni relative all'hub di notifica usato dall'app nel file web.config. Queste informazioni vengono usate solo quando il servizio viene eseguito in locale per connettersi all'hub di notifica, ma vengono ignorate quando il servizio è pubblicato in Azure.

1. Nella scheda **Push** del servizio mobile fare clic sul collegamento **Hub di notifica**.
   
    ![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)
   
    Verrà visualizzato l'hub di notifica usato dal servizio mobile.
2. Nella pagina Hub di notifica, prendere nota del nome dell'hub e quindi fare clic su **Visualizza stringa di connessione**.
   
    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)
3. In **Accedi a informazioni di connessione** copiare la stringa di connessione **DefaultFullSharedAccessSignature**.
   
    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)
4. Nel progetto di servizio mobile in Visual Studio aprire il file Web.config per il servizio e in **connectionStrings** sostituire la stringa di connessione per **MS\_NotificationHubConnectionString** con la stringa di connessione copiata nel passaggio precedente.
5. In **appSettings** sostituire il valore dell'impostazione dell'app **MS\_NotificationHubName** con il nome dell'hub di notifica.

A questo punto, il progetto di servizio mobile è configurato per connettersi all'hub di notifica in Azure quando viene eseguito in locale. Si noti che è importante che il nome e la stringa di connessione dell'hub di notifica corrispondano a quelli del portale, perché le impostazioni del progetto Web.config vengono sovrascritte dalle impostazioni del portale quando il servizio è eseguito in Azure.

<!---HONumber=Oct15_HO3-->