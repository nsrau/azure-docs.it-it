
### Configurazione dell'emulatore di Android per il test
Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

> [!IMPORTANT]
> Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings** e quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.
> 
> 

1. In **Tools** fare clic su **Open Android Emulator Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.
   
       ![Android Virtual Device Manager](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)
2. Selezionare **Google APIs** in **Target** e quindi fare clic su **OK**.
   
       ![Modificare il dispositivo AVD (Android Virtual Device)](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)
3. Fare clic su **Run** sulla barra degli strumenti nella parte superiore della schermata e selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.
   
   L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

### Inserimento di un nuovo elemento per generare una notifica
1. Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.
2. Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.
   
    ![Visualizzare una notifica nel Notification Center](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=Oct15_HO3-->