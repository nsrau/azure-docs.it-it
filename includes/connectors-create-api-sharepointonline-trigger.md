In questo esempio, viene illustrato come utilizzare il trigger **SharePoint Online - Quando viene creato un nuovo elemento** per avviare un flusso di lavoro dell'app per la logica quando viene creato un nuovo elemento in un elenco SharePoint Online.

> [!NOTE]
> All'utente viene chiesto di accedere al proprio account di SharePoint se non è ancora stata creata una *connessione* a SharePoint Online.  
> 
> 

1. Immettere *sharepoint* nella casella di ricerca della finestra di progettazione delle app per la logica, quindi selezionare il trigger **SharePoint Online - Quando viene creato un nuovo elemento**.  
   ![Immagine del trigger di SharePoint Online ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. Viene visualizzato il controllo **Quando viene creato un nuovo elemento** .  
   ![Immagine del trigger di SharePoint Online 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Selezionare un **URL sito**. Questo è il sito di SharePoint Online in cui monitorare la presenza di nuovi elementi per attivare il flusso di lavoro.  
   ![Immagine del trigger di SharePoint Online 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Selezionare un **Nome elenco**. Questo è l'elenco nel sito di SharePoint Online in cui monitorare la presenza di nuovi elementi che attiveranno il flusso di lavoro.  
   ![Immagine del trigger di SharePoint Online 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

A questo punto, l'app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro. Ciò avrà luogo ogni volta che viene creato un nuovo elemento nell'elenco di SharePoint Online selezionato.  

