1. Passare a [Google Cloud Console](https://console.developers.google.com/project), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Selezionare un progetto** e successivamente su **Crea progetto**.
2. Immettere un nome di progetto, accettare le condizioni per l'utilizzo del servizio, quindi fare clic su **Create**. Se necessario, eseguire la verifica SMS, quindi fare nuovamente clic su **Create**.
3. Prendere nota del numero di progetto nella sezione **Projects**.
   
    Più avanti nell'esercitazione si imposterà questo valore come variabile PROJECT\_ID nel client.
4. Fare clic su **Abilitare e gestire le API** in **Utente API Google** e fare clic su **Cloud Messaging per Android**. Quindi, nella pagina successiva fare clic su **Abilita API**.
5. Fare clic su **Credentials**, quindi su **Add Credential**->**API Key**.
6. In **Create a new key** fare clic su **Server key**. Nella finestra successiva fare clic su **Create**.
7. Prendere nota del valore di **API KEY**.
   
    Questo valore della chiave dell'API verrà usato successivamente per abilitare Azure per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!---HONumber=AcomDC_1203_2015-->