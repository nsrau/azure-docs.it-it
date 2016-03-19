

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

   	![Impostare le proprietà dell'hub di notifica](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. In **Bus di servizio** fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**).

5. Nello spazio dei nomi fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

6. Fare clic sulla scheda **Configure** nella parte superiore, immettere il valore **API Key** ottenuto nella sezione precedente, quindi fare clic su **Save**.

   	![Impostare la chiave API GCM nella scheda Configura](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **View Connection String**. Prendere nota delle due stringhe di connessione.

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app in modo da ricevere e inviare le notifiche push.

<!---HONumber=Oct15_HO3-->