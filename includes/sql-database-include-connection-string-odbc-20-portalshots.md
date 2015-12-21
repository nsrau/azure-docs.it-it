<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Ottenere la stringa di connessione dal portale di Azure


Utilizzare l’[anteprima del portale Azure](http://portal.azure.com/) per ottenere la stringa di connessione necessaria al programma client per interagire con il Database SQL di Azure:


1. Fare clic su **ESPLORA** > **database SQL**.

    ![Selezione di SQL][1-select-sql]

2. Immettere il nome del database nella casella di testo filtro vicino alla parte superiore sinistra del pannello **Database SQL**.

    ![Selezionare il database][2-select-database]]

3. Fare clic sulla riga per il database.

4. Quando viene visualizzato il pannello del database, è possibile scegliere i controlli di riduzione a icona standard per comprimere i pannelli che sono stati utilizzati per la ricerca e filtro database per motivi di praticità visiva.

5. Nel pannello del database fare clic su **Mostra stringhe di connessione del database**.

6. Se si prevede di usare la libreria di connessione ODBC, copiare la stringa di etichetta **ODBC (che include Node.js)**.

	![Copiare la stringa di connessione ODBC per il database][3-get-connection-string]

7. Incollare le informazioni sulla stringa di connessione nel codice del programma client. Sarà necessario sostituire {your\_password\_here} con la password reale.



<!--
Could not find a good link for ODBC

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-odbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_1210_2015-->