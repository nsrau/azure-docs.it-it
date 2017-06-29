1. In una nuova finestra accedere al [portale di Azure](https://portal.azure.com/).
2. Nel riquadro a sinistra fare clic su **Nuovo**, quindi su **Database** e infine su **Azure Cosmos DB**.
   
   ![Riquadro Database nel portale di Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Nel pannello **Nuovo account** specificare la configurazione desiderata per l'account Azure Cosmos DB. 

    Con Azure Cosmos DB è possibile scegliere uno dei quattro modelli di programmazione: Gremlin (graph), MongoDB, SQL (DocumentDB) e Table (key-value).  
       
    In questa guida introduttiva viene eseguita la programmazione in base all'API Graph, quindi scegliere **Gremlin (grafo)** quando si compila il modulo. Se si hanno tuttavia dati di documenti di un'app di catalogo, dati chiave/valore (tabella) o dati di cui è stata eseguita la migrazione da un'app MongoDB, tenere presente che Azure Cosmos DB può fornire una piattaforma di servizi di database con distribuzione a livello globale e a disponibilità elevata per tutte le applicazioni cruciali.

    Nel pannello **Nuovo account** completare i campi usando le informazioni riportate nello screenshot seguente come guida. Poiché i valori usati non corrisponderanno a quelli dello screenshot, assicurarsi di scegliere valori univoci per la configurazione dell'account. 
 
    ![Pannello di Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    ID|*Valore univoco*|Nome univoco scelto per identificare l'account Azure Cosmos DB. Poiché alI'ID fornito viene aggiunto *documents.azure.com* per creare l'URI, usare un ID univoco ma facilmente identificabile. L'ID deve contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri.
    API|Gremlin (graph)|La programmazione in base all'[API Graph](../articles/cosmos-db/graph-introduction.md) verrà eseguita più avanti in questo articolo.|
    Sottoscrizione|*Sottoscrizione in uso*|Sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|*Stesso valore di ID*|Nome del nuovo gruppo di risorse per l'account. Per semplicità si può usare lo stesso nome usato come ID. 
    Località|*Area più vicina ai propri utenti*|Posizione geografica in cui ospitare l'account Azure Cosmos DB. Scegliere la posizione più vicina ai propri utenti per fornire loro l'accesso più rapido possibile ai dati.

4. Fare clic su **Crea** per creare l'account.
5. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

    ![Notifica di distribuzione avviata](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Al termine della distribuzione, aprire il nuovo account dal riquadro **Tutte le risorse**. 

    ![Account DocumentDB nel riquadro Tutte le risorse](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)