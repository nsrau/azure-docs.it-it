In questo esempio viene illustrato come utilizzare il trigger **Bus di servizio - Quando un messaggio viene ricevuto in una coda** per avviare il flusso di lavoro di un'app per la logica quando viene aggiunto un nuovo elemento alla coda del bus di servizio.

>[AZURE.NOTE]All'utente viene chiesto di accedere con la propria stringa di connessione del bus di servizio se non è ancora stata creata una *connessione* al bus di servizio.

1. Immettere *bus di servizio* nella casella di ricerca della finestra di progettazione di App per la logica, quindi selezionare il trigger **Bus di servizio - Quando un messaggio viene ricevuto in una coda**. ![Immagine del trigger di bus di servizio 1](./media/connectors-create-api-servicebus/trigger-1.png)
- Viene visualizzato il controllo **Quando un messaggio viene ricevuto in una coda**. ![Immagine del trigger di bus di servizio 2](./media/connectors-create-api-servicebus/trigger-2.png)
- Immettere il nome della coda del bus di servizio da monitorare attraverso il trigger. ![Immagine del trigger di bus di servizio 3](./media/connectors-create-api-servicebus/trigger-3.png)

A questo punto, l'app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro quando un file viene modificato o creato nell'apposita cartella FTP.

<!---HONumber=AcomDC_0727_2016-->