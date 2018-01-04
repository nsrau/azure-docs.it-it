
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Ottenere la stringa di connessione dal portale di Azure
Utilizzare il [portale di Azure](https://portal.azure.com/) per ottenere la stringa di connessione necessarie per il programma client per interagire con Database SQL di Azure. 

1. Selezionare **Esplora tutto** > **database SQL**.

2. Immettere il nome del database nella casella di testo filtro quasi nell'angolo superiore sinistro di **database SQL** blade.

3. Selezionare la riga per il database.

4. Quando viene visualizzato il pannello per il database, per selezionare agevolarne il **Riduci a icona** pulsanti per comprimere i pannelli di cui è utilizzato per la ricerca e filtro di database. 
   
    ![Filtro per isolare il database][10-FilterDatabase]
5. Nel pannello per il database, selezionare **Mostra le stringhe di connessione di database**.

6. Se si prevede di utilizzare la libreria di connessione ADO.NET, copiare la stringa di etichetta **ADO**. 
   
    ![Copiare la stringa di connessione ADO per il database][20-CopyAdoConnectionString]
7. In un formato o un altro, incollare le informazioni sulla stringa di connessione nel codice del programma client.

Per ulteriori informazioni, vedere [stringhe di connessione e i file di configurazione](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
