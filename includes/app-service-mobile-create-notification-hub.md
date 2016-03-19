Eseguire questa procedura per creare un nuovo hub di notifica da usare per le notifiche push. Se si dispone già di un hub di notifica, è anche possibile collegarlo al back-end di App per dispositivi mobili.

1. Nel [portale di Azure], fare clic su **Sfoglia** > **Servizi app**, individuare e fare clic sul back-end dell'App per dispositivi mobili > **Tutte le impostazioni**, quindi in **Dispositivi mobili** fare clic su **Push** > **Hub di notifica**.

2. Fare clic su **+ Hub di notifica**, digitare un nuovo nome di **Hub di notifica**, che può essere lo stesso del back-end dell'App per dispositivi mobili, digitare un nuovo nome dello spazio dei nomi o usarne uno esistente e fare clic su **OK** e infine su **Crea**.

	![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

	In questo modo si crea un nuovo hub di notifica e lo si connette all’app per dispositivi mobili. Se si dispone di un hub di notifica esistente, è possibile connetterlo al back-end dell'App per dispositivi mobili anziché crearne uno nuovo.

A questo punto un hub di notifica è stato connesso al back-end dell'app per dispositivi mobili. In seguito si configurerà l'hub di notifica per la connessione a un servizio di notifica della piattaforma (PNS) che invia le notifiche push al dispositivo nativo.

[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->