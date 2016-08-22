Ecco come usare il trigger **Bus di servizio - Quando un messaggio viene ricevuto in una coda** per attivare un flusso di lavoro di app per la logica quando un nuovo elemento viene inviato a una coda del bus di servizio.

>[AZURE.NOTE]Verrà richiesto di accedere con la stringa di connessione del bus di servizio, se non è già stata creata una connessione al bus di servizio.

1. Nella casella di ricerca della finestra di progettazione per le app per la logica immettere **bus di servizio**. Selezionare quindi il trigger **Bus di servizio - Quando un messaggio viene ricevuto in una coda**. ![Immagine del trigger di bus di servizio 1](./media/connectors-create-api-servicebus/trigger-1.png)
- Viene visualizzata la finestra di dialogo **Quando un messaggio viene ricevuto in una coda**. ![Immagine del trigger di bus di servizio 2](./media/connectors-create-api-servicebus/trigger-2.png)
- Immettere il nome della coda del bus di servizio da monitorare attraverso il trigger. ![Immagine del trigger di bus di servizio 3](./media/connectors-create-api-servicebus/trigger-3.png)

A questo punto l'app per la logica è stata configurata con un trigger. Quando viene ricevuto un nuovo elemento nella coda selezionata, il trigger avvierà un'esecuzione degli altri trigger e delle altre azioni nel flusso di lavoro.

<!---HONumber=AcomDC_0810_2016-->