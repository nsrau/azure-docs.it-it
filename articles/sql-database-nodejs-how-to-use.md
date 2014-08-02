<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="Node.js" title="How to Access Azure SQL Database from Node.js" authors="" solutions="" manager="" editor="" />

Come accedere al database SQL di Azure da Node.js
=================================================

In questa guida verranno fornite le nozioni di base per utilizzare il driver Microsoft per Node.JS per SQL Server per accedere a un database SQL di Azure. Gli scenari presentati includono la **creazione di un database SQL** e la **connessione a un database SQL**. In questa guida viene illustrata la creazione di un database SQL dal [portale di gestione in anteprima](https://manage.windowsazure.com).

Sommario
--------

-   [Concetti](#Concepts)
-   [Procedura: Configurare l'ambiente](#Setup)
-   [Procedura: Creare un database SQL](#CreateServer)
-   [Procedura: Recuperare le informazioni sulla connessione al database SQL](#ConnectionInfo)
-   [Procedura: Connettersi a un'istanza di database SQL](#Connect)
-   [Considerazioni sulla distribuzione di Azure](#Deploy)
-   [Passaggi successivi](#NextSteps)

Concetti
--------

### Informazioni sul database SQL di Azure

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con il database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

Driver Microsoft per Node.JS per SQL Server
-------------------------------------------

Il Driver Microsoft per Node.JS per SQL Server consente agli sviluppatori di accedere ai dati archiviati in Microsoft SQL Server o nel database SQL di Azure da un'applicazione Node.js. Il driver è attualmente solo una versione di anteprima. Altre funzionalità verranno integrate nel progetto non appena completate. Per ulteriori informazioni sul driver, vedere la pagina [Github page](https://github.com/WindowsAzure/node-sqlserver) del progetto relativo al driver Microsoft per Node.JS per SQL Server e il [Wiki](https://github.com/WindowsAzure/node-sqlserver/wiki) associato.
**Nota**

Il driver Microsoft per Node.js per SQL Server è attualmente disponibile come versione di anteprima e si basa su componenti di runtime disponibili solo nei sistemi operativi Microsoft Windows e Azure.

Procedura: Configurare l'ambiente
---------------------------------

### Installare SQL Server Native Client

Il driver Microsoft SQL Server per Node.js si basa su SQL Server Native Client. Anche se il client nativo è automaticamente disponibile quando l'applicazione viene distribuita in Azure, è possibile che non sia presente nell'ambiente di sviluppo locale. È possibile installare SQL Server Native Client dalla pagina di download [Feature Pack di Microsoft SQL Server 2012](http://www.microsoft.com/en-us/download/details.aspx?id=29065).
**Nota**

SQL Server Native Client è disponibile solo per il sistema operativo Microsoft Windows. Anche se questo driver è disponibile in modo nativo in Azure, non sarà possibile testare l'applicazione localmente utilizzando le informazioni in questo articolo, se si sta eseguendo lo sviluppo in un sistema operativo diverso da Microsoft Windows.

### Installare Node.js

Node.js può essere installato da <http://nodejs.org/#download>. Se un pacchetto di installazione non è disponibile per il sistema operativo, è possibile compilare Node.js dall'origine.

Procedura: Creare un database SQL
---------------------------------

Per creare un database SQL di Azure, eseguire la procedura seguente:

1.  Accedere al [portale di gestione in anteprima](https://manage.windowsazure.com).
2.  Fare clic sull'icona **+ New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure](./media/sql-database-nodejs-how-to-use/new_website.jpg)

3.  Fare clic su **SQL Database**, quindi su **Custom Create**.

    ![Creazione personalizzata di un nuovo database SQL](./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg)

4.  Immettere un valore per il campo **NAME** relativo al database, selezionare l'opzione per **EDITION** (WEB o BUSINESS), selezionare il valore per **MAX SIZE** relativo al database, scegliere il valore per **COLLATION** e selezionare **NEW SQL Database Server**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo. Si noti che, se in precedenza è stato creato un database SQL, è possibile scegliere un server esistente dall'elenco a discesa **Choose a server**.

    ![Compilazione delle impostazioni del database SQL](./media/sql-database-nodejs-how-to-use/new-sql-db.png)

5.  Immettere un nome amministratore e una password (e confermare la password), scegliere l'area in cui verrà creato il nuovo database SQL e selezionare la casella `Allow Azure Services to access the server`.

    ![Creazione del nuovo server di database SQL](./media/sql-database-nodejs-how-to-use/db-server-settings.png)

Per visualizzare le informazioni sul server e sul database, fare clic su **SQL Databases** nel portale di gestione in anteprima. Sarà quindi possibile fare clic su **DATABASES** o su **SERVERS** per visualizzare le informazioni pertinenti.

![Visualizzazione delle informazioni sul server e sul database](./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png)

Procedura: Recuperare le informazioni sulla connessione al database SQL
-----------------------------------------------------------------------

Per ottenere informazioni sulla connessione al database SQL, fare clic su **SQL DATABASES** nel portale, quindi fare clic sul nome del database.

![Visualizzazione delle informazioni sul database](./media/sql-database-nodejs-how-to-use/go-to-db-info.png)

Fare quindi clic su **Show connection strings**.

![Visualizzazione delle stringhe di connessione](./media/sql-database-nodejs-how-to-use/show-connection-string.png)

Nella sezione ODBC della finestra risultante, prendere nota dei valori per la stringa di connessione. Si tratta della stringa di connessione che si utilizzerà durante la connessione al database SQL dall'applicazione node. La password sarà quella utilizzata durante la creazione del database SQL.

Procedura: Connettersi a un'istanza di database SQL
---------------------------------------------------

### Installare node-sqlserver

Il driver Microsoft per Node.js per SQL Server è disponibile come modulo nativo node-sqlserver. Una versione binaria di questo modulo è disponibile nell'[Area download](http://www.microsoft.com/en-us/download/details.aspx?id=29995). Per utilizzare la versione binaria, eseguire la procedura seguente:

1.  Estrarre l'archivio binario nella directory **node\_modules** dell'applicazione.
2.  Eseguire il file **node-sqlserver-install.cmd** estratto dall'archivio. Verrà creata una sottodirectory **node-sqlserver** sotto **node\_modules** e i file dei driver verranno spostati in questa nuova struttura di directory.
3.  Eliminare il file **node-sqlserver-install.cmd**, in quanto non più necessario.


<div class="dev-callout">
<b>Nota</b>
<p>È inoltre possibile installare il modulo node-sqlserver mediante l'utilità npm. Questa tuttavia richiamerà node-gyp per compilare una versione binaria del modulo nel sistema.</p>
</div>

### Specificare la stringa di connessione

Per utilizzare node-sqlserver, è necessario richiederlo nell'applicazione e specificare una stringa di connessione. La stringa di connessione deve essere il valore ODBC restituito nella sezione [Procedura: recuperare le informazioni sulla connessione al database SQL](#ConnectionInfo) di questo articolo. Il codice sarà simile al seguente:

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Eseguire una query del database

Le query possono essere eseguite specificando un'istruzione Transact-SQL con il metodo **query**. Il codice seguente consente di creare un server HTTP e di restituire dati dalle righe **ID**, **Column1** e **Column2** nella tabella **Test** quando si visualizza la pagina Web:

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

Anche se l'esempio precedente illustra come restituire tutte le righe contemporaneamente nella raccolta di risultati, è anche possibile restituire un oggetto istruzione che consente di sottoscrivere gli eventi. In questo modo sarà possibile ricevere le singole righe e colonne quando vengono restituite. Nell'esempio seguente viene illustrato come eseguire questa operazione:

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

Considerazioni sulla distribuzione di Azure
-------------------------------------------
<div class="dev-callout">
<b>Nota</b>
</div>

Le informazioni seguenti si basano su una versione di anteprima del driver Microsoft per Node.js per SQL Server. È possibile che le informazioni contenute in questa sezione non siano le più recenti sull'utilizzo del modulo node-sqlserver con Azure. Per le informazioni più recenti, vedere la [pagina del progetto relativo al driver Microsoft per Node.JS per SQL Server](https://github.com/WindowsAzure/node-sqlserver) in Github.


Poiché Azure non installerà in modo dinamico il modulo node-sqlserver in fase di esecuzione, è necessario verificare che la distribuzione dell'applicazione includa una versione binaria del modulo. Per verificare che la distribuzione contenga una versione binaria del modulo, assicurarsi che la struttura di directory seguente esista e contenga i file descritti di seguito:

    application directory
        node_modules
            node-sqlserver
                lib

La directory **node-sqlserver** deve contenere un file **package.json**. La directory **lib** deve contenere un file **sql.js** e un file **sqlserver.node**, che corrisponde al formato compilato del modulo node-sqlserver.

Per ulteriori informazioni sulla distribuzione di un'applicazione Node.js in Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) e [Servizio cloud Node.js](/en-us/develop/nodejs/tutorials/getting-started/).

Passaggi successivi
-------------------

-   [Presentazione del driver Microsoft per Node.JS per SQL Server](http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx)
-   [Driver Microsoft per Node.JS per SQL Server in Github.com](https://github.com/WindowsAzure/node-sqlserver)

