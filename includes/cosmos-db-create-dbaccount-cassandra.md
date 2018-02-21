1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Nuovo** > **Database** > **Azure Cosmos DB**.
   
   ![Riquadro Database nel portale di Azure](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. Nella pagina **Nuovo account** immettere le impostazioni per il nuovo account Azure Cosmos DB. 
 
    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID|*Immettere un nome univoco*|Immettere un nome univoco per identificare l'account Azure Cosmos DB. Poiché alI'ID fornito viene aggiunto *documents.azure.com* per creare il punto di contatto, usare un ID univoco ma facilmente identificabile.<br><br>L'ID può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri.
    API|Cassandra|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API per soddisfare le esigenze dell'applicazione, ovvero SQL (database di documenti) Gremlin (grafo), MongoDB, SQL (database di documenti), Tabella di Azure e Cassandra, per ognuna delle quali è attualmente necessario un account separato. <br><br>Selezionare **Cassandra** perché in questa guida introduttiva si crea un database a colonne ampie su cui è possibile eseguire query usando la sintassi SQL.<br><br>Se Cassandra (a colonna ampia) non compare nell'elenco, è necessario [fare richiesta di partecipare](../articles/cosmos-db/cassandra-introduction.md#sign-up-now) al programma di anteprima dell'API Cassandra.<br><br> [Altre informazioni sull'API Cassandra](../articles/cosmos-db/cassandra-introduction.md)|
    Sottoscrizione|*Sottoscrizione in uso*|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|*Immettere lo stesso nome univoco specificato sopra in ID*|Immettere il nome di un nuovo gruppo di risorse per l'account. Per semplicità si può usare lo stesso nome usato come ID. 
    Località|*Selezionare l'area più vicina agli utenti*|Selezionare la posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.
    Aggiungi al dashboard | Select | Selezionare questa casella per aggiungere il nuovo account di database al dashboard del portale e accedervi più facilmente.

    Fare quindi clic su **Crea**.

    ![Pannello Nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-2.png)

4. La creazione dell'account richiede alcuni minuti, durante i quali nel portale viene visualizzato il riquadro del dashboard **Distribuzione di Azure Cosmos DB**.

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount-cassandra/deploying-cosmos-db.png)

    Al termine della creazione dell'account verrà visualizzata la pagina **L'account Azure Cosmos DB è stato creato**. 

