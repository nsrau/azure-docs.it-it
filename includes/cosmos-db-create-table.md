È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una tabella. 

1. Fare clic su **Esplora dati** > **New Table** (Nuova tabella). 
    
    Viene visualizzata l'area **Add Table** (Aggiungi tabella) all'estrema destra. Per vederla potrebbe essere necessario scorrere la schermata.

    ![Esplora dati nel portale di Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Nella pagina **Add Table** (Aggiungi tabella) immettere le impostazioni per la nuova tabella.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID tabella|sample-table|ID della nuova tabella. I nomi delle tabelle presentano gli stessi requisiti relativi ai caratteri degli ID di database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    Capacità di archiviazione| Fissa (10 GB)|Modificare il valore in **Fissa (10 GB)**. Questo valore indica la capacità di archiviazione del database.
    Velocità effettiva|400 UR/s|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.

    Fare clic su **OK**.

    In Esplora dati verranno visualizzati il nuovo database e la nuova tabella.

    ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)