
1. Fare clic con il pulsante destro del mouse sul progetto di Windows Store, selezionare **Imposta come progetto di avvio** e premere il tasto F5 per eseguire l'app di Windows Store.
   
    All'avvio dell'app, il dispositivo viene registrato per le notifiche push.
2. Arrestare l'app di Windows Store e ripetere i passaggi precedenti per eseguire l'app di Windows Phone Store.
   
    A questo punto, entrambi i dispositivi sono stati registrati per la ricezione delle notifiche push.
3. Eseguire nuovamente l'app di Windows Store, digitare del testo in **Inserisci TodoItem** e fare clic su **Salva**.
   
       ![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png)
   
       Si noti che, al termine dell'inserimento, sia l'app di Windows Store che quella di Windows Phone ricevono una notifica push da WNS.
   
       ![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png)
   
    La notifica viene visualizzata su Windows Phone anche se l'app non è in esecuzione.
   
       ![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

<!---HONumber=August15_HO6-->