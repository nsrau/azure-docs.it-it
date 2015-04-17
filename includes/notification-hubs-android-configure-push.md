

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![Impostare proprietà dell'hub di notifica](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hubdi notifica*-ns**), quindi fare clic su **Configura** nella parte superiore.

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

6. Fare clic sulla scheda **Configura** nella parte superiore, immettere il valore **Chiave API** ottenuto nella sezione precedente, quindi fare clic su **Salva**.

   	![Impostare la chiave API di GCM nella scheda Configura](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Selezionare la scheda **Dashboard** nella parte superiore, quindi fare clic su **Visualizza stringa di connessione**. Prendere nota delle due stringhe di connessione.

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app in modo da ricevere e inviare le notifiche push.

<!--HONumber=49-->