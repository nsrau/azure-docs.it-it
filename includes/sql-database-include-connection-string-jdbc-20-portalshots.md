<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Ottenere la stringa di connessione dal portale di Azure
Usare il [portale di Azure](https://portal.azure.com/) per ottenere la stringa di connessione necessaria al programma client per interagire con il database SQL di Azure:

1. Fare clic su **ESPLORA** > **Database SQL**.
   
    ![Selezione di SQL][1-select-sql]
2. Immettere il nome del database nella casella di testo filtro vicino alla parte superiore sinistra del pannello **Database SQL** .
   
    ![Selezionare il database][2-select-database]
3. Fare clic sulla riga per il database.
4. Quando viene visualizzato il pannello del database, è possibile scegliere i controlli di riduzione a icona standard per comprimere i pannelli usati per la ricerca e il filtro del database per una visualizzazione più chiara.
5. Nel pannello del database fare clic su **Mostra stringhe di connessione del database**.
6. Se si prevede di usare la libreria di connessione JDBC, copiare la stringa di etichetta **JDBC**.
   
    ![Copiare la stringa di connessione JDBC per il database][3-get-connection-string]
7. Incollare le informazioni sulla stringa di connessione nel codice del programma client.  Sarà necessario sostituire {your_password_here} con la password reale.

Per altre informazioni, vedere:<br/>[Stringhe di connessione e file di configurazione](https://msdn.microsoft.com/library/ms378428.aspx).

<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-jdbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
