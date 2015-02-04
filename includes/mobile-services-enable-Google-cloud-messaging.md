


1.  Passare al sito Web [Google Cloud Console][Google Cloud Console], eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **CREATE PROJECT**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

    >[WACOM.NOTE]Se si dispone già di un progetto esistente, dopo l'accesso verrà visualizzata la pagina **Projects**. Per creare un nuovo progetto dalla pagina Dashboard, espandere **API Project**, fare clic su **Create...** in **Other projects**, quindi immettere un nome di progetto e fare clic su **Create project**.

2.  Immettere un nome di progetto, accettare le condizioni per l'utilizzo del servizio, quindi fare clic su **Create**. Eseguire la verifica SMS richiesta, quindi fare nuovamente clic su **Create**.

3.  Prendere nota del numero di progetto nella sezione **Projects**.

    Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT\_ID nel client.

4.  Nella colonna di sinistra fare clic su **APIs & auth**, scorrere verso il basso e fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android** e accettare le condizioni per l'utilizzo del servizio.

    ![][1]

5.  Fare clic su **Credentials**, quindi su **CREATE NEW KEY**

    ![][2]

6.  In **Create a new key** fare clic su **Server key**. Nella finestra successiva fare clic su **Create**.

    ![][3]

7.  Prendere nota del valore **API key**.

    ![][4]

    Questo valore della chiave dell'API verrà usato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

  [Google Cloud Console]: http://cloud.google.com/console
   
  [1]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
  [2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
  [3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
  [4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
