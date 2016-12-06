
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Ottenere la stringa di connessione dal portale di Azure
Usare il [portale di Azure](https://portal.azure.com/) per ottenere la stringa di connessione necessaria al programma client per interagire con il database SQL di Azure: 

1. Fare clic su **ESPLORA** > **Database SQL**.
2. Immettere il nome del database nella casella di testo filtro vicino alla parte superiore sinistra del pannello **Database SQL** .
3. Fare clic sulla riga per il database.
4. Quando viene visualizzato il pannello del database, è possibile scegliere i controlli di riduzione a icona standard per comprimere i pannelli usati per la ricerca e il filtro del database per una visualizzazione più chiara. 
   
    ![Filtro per isolare il database][10-FilterDatabase]
5. Nel pannello del database, fare clic su **Mostra stringhe di connessione di database**.
6. Se si prevede di utilizzare la libreria di connessione ADO.NET, copiare la stringa di etichetta **ADO**. 
   
    ![Copiare la stringa di connessione ADO per il database][20-CopyAdoConnectionString]
7. In un formato o un altro, incollare le informazioni sulla stringa di connessione nel codice del programma client.

Per altre informazioni, vedere il blog sul <br/>[stringhe di connessione e file di configurazione](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Nov16_HO3-->


