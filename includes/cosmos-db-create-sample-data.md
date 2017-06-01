È ora possibile aggiungere dati alla nuova raccolta usando Esplora dati.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere il database **Items**, espandere la raccolta **ToDoList**, fare clic su **Documenti** e quindi su **Nuovo documento**. 

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/cosmos-db-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Aggiungere ora alcuni nuovi documenti alla raccolta con la struttura seguente, inserendo valori univoci per l'ID in ogni documento e modificando le altre proprietà come necessario. I nuovi documenti possono avere la struttura desiderata, perché Azure Cosmos DB non impone alcuno schema per i dati.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     È ora possibile usare query in Esplora dati per recuperare i dati. Per impostazione predefinita, Esplora dati usa SELECT * FROM c per recuperare tutti i documenti della raccolta, ma è possibile usare SELECT * FROM c ORDER BY c.name ASC per restituire tutti i documenti in ordine alfabetico crescente in base alla proprietà name. 
 
     È anche possibile usare Esplora dati per creare stored procedure, funzioni definite dall'utente e trigger per eseguire la logica di business sul lato server. Esplora dati espone tutti i tipi di accesso ai dati a livello di codice predefiniti disponibili nelle API, ma consente anche di accedere facilmente ai dati nel portale di Azure.