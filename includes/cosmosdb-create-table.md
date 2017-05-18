È ora possibile usare Esplora dati per creare un contenitore di grafi e aggiungere dati al database. 

1. Nel menu di navigazione del portale di Azure, in **Tabelle**, fare clic su **Esplora dati**. 
2. Nel pannello Esplora dati fare clic su **Nuova raccolta**, quindi compilare i campi della pagina con le informazioni seguenti.

    ![Esplora dati nel portale di Azure](./media/cosmosdb-create-graph/azure-cosmosdb-data-explorer.png)

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    ID database|sample-database|ID del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    ID raccolta|sample-table|ID della nuova raccolta. I caratteri dei nomi delle raccolte hanno gli stessi requisiti di quelli degli ID dei database.
    Capacità di archiviazione| 10 GB|Lasciare il valore predefinito. Indica la capacità di archiviazione del database.
    Velocità effettiva|400 UR/s|Lasciare il valore predefinito. È possibile aumentare la velocità effettiva in un secondo momento se si desidera ridurre la latenza.
    Chiave di partizione|/databases|Chiave di partizione che distribuisce i dati in modo uniforme a ogni partizione. Quando si crea una raccolta ad alte prestazioni è importante selezionare la chiave di partizione corretta. Per altre informazioni, vedere [Progettazione per il partizionamento](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Dopo aver compilato il modulo, fare clic su **OK**.