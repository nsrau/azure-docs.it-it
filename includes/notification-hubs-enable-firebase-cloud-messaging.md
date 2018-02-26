

1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android). Seguire quindi le istruzioni visualizzate.

    ![Aggiungere Firebase all'app Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

    ![Selezionare Project Settings (Impostazioni progetto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Selezionare la scheda **General** (Generale) nelle impostazioni del progetto. Scaricare quindi il file **google-services.json** che contiene la chiave API del server e l'ID client.
5. Selezionare la scheda **Cloud Messaging** nelle impostazioni del progetto e quindi copiare il valore di **Legacy server key** (Chiave server legacy). Questo valore verrà usato per configurare i criteri di accesso all'hub di notifica.
