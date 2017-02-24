
1. Fare clic con il pulsante destro del mouse sul progetto di Windows Store, selezionare **Imposta come progetto di avvio**e premere il tasto F5 per eseguire l'app di Windows Store.
   
    All'avvio dell'app, il dispositivo viene registrato per le notifiche push.
2. Arrestare l'app di Windows Store e ripetere i passaggi precedenti per eseguire l'app di Windows Phone Store.
   
    A questo punto, entrambi i dispositivi sono stati registrati per la ricezione delle notifiche push.
3. Eseguire nuovamente l'app di Windows Store, digitare del testo in **Inserisci TodoItem** e quindi fare clic su **Salva**.
   
       Note that after the insert completes, both the Windows Store and the Windows Phone apps receive a push notification from WNS. The notification is displayed on Windows Phone even when the app isn't running.
   
       ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)



<!--HONumber=Nov16_HO3-->


