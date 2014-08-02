

**Nota**

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Passare al sito [Google Cloud Console](http://cloud.google.com/console), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **CREATE PROJECT**.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    **Nota**

    Se si dispone già di un progetto esistente, dopo l'accesso verrà visualizzata la pagina **Projects**. Per creare un nuovo progetto dalla pagina Dashboard, espandere **API Project**, fare clic su **Create...** in **Other projects**, quindi immettere un nome di progetto e fare clic su **Create project**.

2.  Immettere un nome di progetto, accettare le condizioni per l'utilizzo del servizio, quindi fare clic su **Create**. Eseguire la verifica SMS richiesta, quindi fare nuovamente clic su **Create**.

3.  Prendere nota del numero di progetto nella sezione **Projects**.

    Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT\_ID nel client.

4.  Nella colonna di sinistra fare clic su **APIs & auth**, scorrere verso il basso e fare clic sull'interruttore per abilitare **Google Cloud Messaging for Android** e accettare le condizioni per l'utilizzo del servizio.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Fare clic su **Credentials**, quindi su **CREATE NEW KEY**

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  In **Create a new key** fare clic su **Server key**. Nella finestra successiva fare clic su **Create**.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  Prendere nota del valore **API key**.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


