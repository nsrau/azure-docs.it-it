Questa procedura dettagliata descrive come utilizzare il trigger **Salesforce - Quando viene creato un oggetto** per avviare un flusso di lavoro dell'app per la logica quando viene creato un nuovo lead in Salesforce.

> [!NOTE]
> All'utente viene chiesto di accedere al proprio account di Salesforce se non è ancora stata creata una *connessione* a Salesforce.  
> 
> 

1. Immettere *salesforce* nella casella di ricerca della finestra di progettazione delle app per la logica e quindi selezionare il trigger **Salesforce - Quando viene creato un oggetto**.  
   ![Immagine del trigger Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. Viene visualizzato il controllo **Quando viene creato un oggetto** .  
   ![Immagine del trigger Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Selezionare **Tipo di oggetto** e *Lead* dall'elenco di oggetti. In questo passaggio si indica che si sta creando un trigger che invierà una notifica all'app per la logica ogni volta che viene creato un nuovo lead in Salesforce.   
   ![Immagine del trigger Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. È tutto. Il trigger è stato creato. Tuttavia, è necessario creare almeno un'azione per rendere valida questa app per la logica.    
   ![Immagine del trigger Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

A questo punto, l'app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro quando un nuovo elemento viene creato in Salesforce.  



<!--HONumber=Nov16_HO3-->


