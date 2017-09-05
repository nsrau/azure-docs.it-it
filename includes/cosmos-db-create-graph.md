È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database a grafo. 

1. Nel menu di navigazione a sinistra del portale di Azure fare clic su **Esplora dati (anteprima)**. 
2. Nel pannello **Esplora dati (anteprima)** fare clic su **New Graph** (Nuovo grafo) e quindi compilare i campi della pagina con le informazioni seguenti.

    ![Esplora dati nel portale di Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    ID database|sample-database|ID del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    Graph id (ID grafo)|sample-graph|ID del nuovo grafo. I nomi dei grafi presentano gli stessi requisiti relativi ai caratteri degli ID di database.
    Capacità di archiviazione| 10 GB|Lasciare il valore predefinito. Indica la capacità di archiviazione del database.
    Velocità effettiva|400 UR/s|Lasciare il valore predefinito. È possibile aumentare la velocità effettiva in un secondo momento se si desidera ridurre la latenza.
    Chiave di partizione|/userId|Chiave di partizione che distribuisce i dati in modo uniforme a ogni partizione. La selezione della chiave di partizione corretta è importante per creare un grafo efficiente. Per altre informazioni, vedere [Progettazione per il partizionamento](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Dopo aver compilato il modulo, fare clic su **OK**.