1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Nuovo**, quindi su **Database** e in **Azure Cosmos DB** fare clic su **Crea**. 
   
   ![Screenshot del portale di Azure in cui sono evidenziati Altri servizi e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Nella pagina **Nuovo account** immettere le impostazioni per il nuovo account Azure Cosmos DB. 
 
    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    ID|*Immettere un nome univoco*|Immettere un nome univoco per identificare l'account Azure Cosmos DB. Poiché alI'ID fornito viene aggiunto *documents.azure.com* per creare l'URI, usare un ID univoco ma facilmente identificabile.<br><br>L'ID può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri.
    API|tabella di Azure|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API per soddisfare le esigenze dell'applicazione, ovvero SQL (database di documenti) Gremlin (grafo), MongoDB, SQL (database di documenti), Tabella di Azure e Cassandra, per ognuna delle quali è attualmente necessario un account separato.<br><br>Selezionare **Tabella di Azure** perché in questa guida rapida si crea una tabella che funziona con l'API di tabella.<br><br>[Altre informazioni sull'API di tabella](../articles/cosmos-db/table-introduction.md) |
    Sottoscrizione|*Immettere lo stesso nome univoco specificato sopra in ID*|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|*Stesso valore di ID*|Immettere il nome di un nuovo gruppo di risorse per l'account. Per semplicità si può usare lo stesso nome usato come ID. 
    Percorso|*Selezionare l'area più vicina agli utenti*|Selezionare la posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.
    Abilita ridondanza geografica| Lasciare vuoto | Consente di creare una versione replicata del database in una seconda area abbinata. Lasciare vuoto.  
    Aggiungi al dashboard | Selezionare | Selezionare questa casella per aggiungere il nuovo account di database al dashboard del portale e accedervi più facilmente.

    Fare quindi clic su **Crea**.  

    ![Screenshot del pannello Nuovo Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-2.png)

4. La creazione dell'account richiede alcuni minuti, durante i quali nel portale viene visualizzato il riquadro **Distribuzione di Azure Cosmos DB**.

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount-table/deploying-cosmos-db.png)

    Al termine della creazione dell'account verrà visualizzata la pagina **L'account Azure Cosmos DB è stato creato**.
