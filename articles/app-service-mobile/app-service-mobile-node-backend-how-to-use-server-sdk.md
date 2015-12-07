<properties
	pageTitle="Come usare l'SDK del server back-end di Node.js per App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare l'SDK del server back-end di Node.js per App per dispositivi mobili del servizio app di Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="adrianhall"/>

# Come usare Node.js SDK per App per dispositivi mobili di Azure

Questo articolo fornisce informazioni dettagliate ed esempi sull'uso di un back-end Node.js nelle app per dispositivi mobili del servizio app di Azure.

> [AZURE.NOTE]Questo SDK è disponibile in ANTEPRIMA. Non è quindi consigliabile usare questo SDK in fase di produzione. Gli esempi di questo documento usano la versione 2.0.0-alpha6 di [azure-mobile-apps].

## <a name="Introduction"></a>Introduzione

App per dispositivi mobili del servizio app di Azure consente di aggiungere un'API Web di accesso ai dati ottimizzata per dispositivi mobili a un'applicazione Web. L'SDK per App per dispositivi mobili del servizio app di Azure viene fornito per le applicazioni Web Node.js e ASP.NET. L'SDK consente le operazioni seguenti:

- Operazioni su tabella (read, insert, update, delete) per l'accesso ai dati
- Operazioni sulle API personalizzate

Entrambe le operazioni permettono l'autenticazione in tutti i provider di identità consentiti dal servizio app di Azure, inclusi i provider di identità basati su social network, ad esempio Facebook, Twitter, Google e Microsoft nonché Azure Active Directory per l'identità aziendale.

Esempi per ogni caso d'uso sono disponibili nella [directory degli esempi in GitHub].

### <a name="howto-cmdline-basicapp"></a>Creare un back-end Node.js di base usando la riga di comando

Ogni back-end Node.js per App per dispositivi mobili del servizio app di Azure viene avviato come applicazione ExpressJS. ExpressJS è il framework dei servizi Web più diffuso disponibile per Node.js. È possibile creare un'applicazione [Express] di base seguendo questa procedura:

1. Creare una nuova directory per il progetto in una finestra di comando o di PowerShell.

        mkdir basicapp

2. Eseguire npm init per inizializzare la struttura del pacchetto.

        cd basicapp
        npm init

    Il comando npm init porrà una serie di domande per inizializzare il progetto. L'output di esempio è riportato di seguito

    ![L'output di init npm][0]

3. Installare le librerie express e azure-mobile-apps dal repository npm.

        npm install --save express azure-mobile-apps

4. Creare un file app.js per implementare il server per dispositivi mobili di base.

		var express = require('express'),
			azureMobileApps = require('azure-mobile-apps');

		var app = express(),
			mobile = azureMobileApps();

		// Define a TodoItem table
		mobile.tables.add('TodoItem');

		// Add the mobile API so it is accessible as a Web API
		app.use(mobile);

		// Start listening on HTTP
		app.listen(process.env.PORT || 3000);

Questa applicazione crea una semplice API Web ottimizzata per dispositivi mobili con un endpoint singolo, /tables/TodoItem, che consente l'accesso non autenticato a un archivio dati SQL sottostante usando uno schema dinamico. È adatta per l'avvio rapido delle librerie client seguenti:

- [Avvio rapido di client iOS]
- [Avvio rapido di client Xamarin.iOS]
- [Avvio rapido di client Xamarin.Android]
- [Avvio rapido di client Xamarin.Forms]
- [Avvio rapido di client Windows Store]
- [Avvio rapido di client HTML/JavaScript]

Il codice per questa applicazione di base è disponibile nell'[esempio basicapp in GitHub].

### <a name="howto-vs2015-basicapp"></a>Creare un back-end Node.js con Visual Studio 2015

Visual Studio 2015 richiede un'estensione per lo sviluppo di applicazioni Node.js all'interno dell'IDE. Per iniziare, scaricare e installare gli [Strumenti Node.js 1.1 per Visual Studio]. Dopo aver installato gli Strumenti Node.js per Visual Studio, creare un'applicazione Express 4.x:

1. Aprire la finestra di dialogo **New Project** (da **File** > **New** > **Project**).

2. Espandere **Templates** > **JavaScript** > **Node.js**.

3. Selezionare **Basic Azure Node.js Express 4 Application**.

4. Immettere il nome del progetto. Fare clic su *OK*.

	![Nuovo progetto di Visual Studio 2015][1]

5. Fare clic sul nodo **npm** e selezionare **Install New npm packages**.

6. Al momento della creazione della prima applicazione Node.js è necessario aggiornare il catalogo npm. A questo scopo, fare clic su **Refresh**.

7. Immettere _azure-mobile-apps_ nella casella di ricerca. Fare clic sul pacchetto **azure-mobile-apps 2.0.0**, quindi fare clic su **Install Package**.

	![Installare nuovi pacchetti npm][2]

8. Fare clic su **Close**.

9. Aprire il file _app.js_ per aggiungere il supporto per l'SDK per App per dispositivi mobili di Azure. Nella riga 6 nella parte inferiore delle istruzioni require della raccolta aggiungere il codice seguente:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Circa alla riga 27 dopo le altre istruzioni app.use aggiungere il codice seguente:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use('mobile');

    Salvare il file.

10. Eseguire l'applicazione in locale (l'API verrà fornita in http://localhost:3000) o pubblicarla in Azure.

### <a name="howto-publish-to-azure"></a>Pubblicare il back-end Node.js in Azure

Microsoft Azure offre numerosi meccanismi per la pubblicazione del back-end Node.js per App per dispositivi mobili del servizio app di Azure, tra cui l'uso di strumenti di distribuzione integrati in Visual Studio, strumenti da riga di comando e opzioni di distribuzione continua basate sul controllo del codice sorgente. Per altre informazioni su questo argomento, vedere la [Guida alla distribuzione del servizio app di Azure].

Il servizio app di Azure include suggerimenti specifici per l'applicazione Node.js che è consigliabile esaminare prima della distribuzione:

- Come [specificare la versione di Node.js]
- Come [usare i moduli di Node.js]

## <a name="TableOperations"></a>Operazioni su tabella

L'SDK del server di Node.js azure-mobile-apps offre diversi meccanismi per esporre le tabelle di dati archiviate in SQL Azure come API Web. Sono disponibili cinque operazioni.

| Operazione | Descrizione |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Ottiene tutti i record nella tabella |
| GET /tables/\_tablename\_/:id | Ottiene un record specifico nella tabella |
| POST /tables/\_tablename\_ | Crea un nuovo record nella tabella |
| PATCH /tables/\_tablename\_/:id | Aggiorna un record esistente nella tabella |
| DELETE /tables/\_tablename\_/:id | Elimina un record dalla tabella |

Questa API Web supporta [OData] ed estende lo schema tabella per supportare la [sincronizzazione dati offline].

### <a name="howto-dynamicschema"></a>Definire le tabelle usando uno schema dinamico

Perché sia possibile usare una tabella, è necessario prima definirla. Le tabelle possono essere definite con uno schema statico (dove lo sviluppatore definisce le colonne all'interno dello schema) o in modo dinamico (dove l'SDK controlla lo schema in base alle richieste in ingresso). Lo sviluppatore può anche controllare alcuni aspetti specifici dell'API Web aggiungendo codice JavaScript alla definizione.

Come procedura consigliata, è necessario definire ogni tabella in un file JavaScript nella directory delle tabelle, quindi usare il metodo tables.import() per importare le tabelle. Estendendo l'app di base, il file app.js viene regolato:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
	    mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definire la tabella in ./tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Per impostazione predefinita, le tabelle usano lo schema dinamico. Per disabilitare lo schema dinamico a livello globale, impostare su false l'impostazione app **MS\_DynamicSchema** nel portale di Azure.

Per un esempio esaustivo, vedere l'[esempio todo in GitHub].

### <a name="howto-staticschema"></a>Definire le tabelle usando uno schema statico

È possibile definire in modo esplicito le colonne da esporre con l'API Web. L'SDK di Node.js azure-mobile-apps aggiunge automaticamente all'elenco specificato tutte le altre colonne necessarie per la sincronizzazione dati offline. Ad esempio, le applicazioni client di avvio rapido richiedono una tabella con due colonne: text (una stringa) e complete (un valore booleano). Ciò può essere specificato nel file JavaScript di definizione della tabella, situato nella directory delle tabelle, nel modo seguente:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Se si definiscono le tabelle in modo statico, è necessario chiamare il metodo tables.initialize() per creare lo schema del database all'avvio. Il metodo tables.initialize() restituisce un oggetto [Promise], usato per verificare che il servizio Web non fornisca le richieste prima dell'inizializzazione del database.

### <a name="howto-sqlexpress-setup"></a>Usare SQL Express come archivio dati di sviluppo nel computer locale

L'SDK di Node.js per App per dispositivi mobili di Azure offre tre opzioni per la gestione dei dati predefiniti:

- Usare il driver di **memoria** per fornire un archivio di esempio non persistente
- Usare il driver **sql** per fornire un archivio dati di SQL Express per lo sviluppo
- Usare il driver **sql** per fornire un archivio dati di SQL Azure per la produzione

L'SDK di Node.js per App per dispositivi mobili di Azure usa il [pacchetto Node.js mssql] per stabilire e usare una connessione a SQL Express e a SQL Azure. Per questo pacchetto è necessario abilitare le connessioni TCP nell'istanza di SQL Express.

> [AZURE.NOTE]Il driver di memoria non fornisce un set completo di funzionalità per il test. Per testare il back-end in locale, è consigliabile usare un archivio dati di SQL Express con driver sql.

1. Scaricare e installare [Microsoft SQL Server 2014 Express]. Assicurarsi di installare l'edizione SQL Server 2014 Express with Tools. A meno che non sia richiesto in modo esplicito il supporto a 64 bit, l'esecuzione della versione a 32 bit richiede una quantità di memoria inferiore.

2. Eseguire Gestione configurazione SQL Server 2014.

  a. Espandere il nodo **Configurazione di rete SQL Server** nel menu struttura a sinistra. b. Fare clic su **Protocolli per SQLEXPRESS**. c. Fare clic con il pulsante destro del mouse su **TCP/IP** e scegliere **Abilita**. Fare clic su **OK** nella finestra di dialogo popup. d. Fare clic con il pulsante destro del mouse su **TCP/IP** e scegliere **Proprietà**. e. Fare clic sulla scheda **Indirizzi IP**. f. Trovare il nodo **IPAll**. Nel campo **Porta TCP** immettere **1433**.

  ![Configurare SQL Express per TCP/IP][3]

  g. Fare clic su **OK**. Fare clic su **OK** nella finestra di dialogo popup. h. Fare clic su **Servizi di SQL Server** nel menu struttura a sinistra. i. Fare clic con il pulsante destro del mouse su **SQL Server (SQLEXPRESS)** e scegliere **Riavvia**. j. Chiudere Gestione configurazione SQL Server 2014.

3. Creare un'esecuzione di SQL Server 2014 Management Studio e connettersi all'istanza locale di SQL Express

  a. Fare clic con il pulsante destro del mouse sull'istanza in Esplora oggetti e scegliere **Proprietà**. b. Selezionare la pagina **Sicurezza**. c. Assicurarsi che l'opzione **Autenticazione di SQL Server e di Windows** sia selezionata. d. Fare clic su **OK**.

  ![Configurare l'autenticazione di SQL Express][4]

  e. Espandere **Sicurezza** > **Account di accesso** in Esplora oggetti. f. Fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account di accesso**. g. Immettere un nome account di accesso. Selezionare **Autenticazione di SQL Server**. Immettere una password, quindi immettere la stessa password in **Conferma password**. Si noti che la password deve soddisfare i requisiti di complessità di Windows. h. Fare clic su **OK**.

  ![Aggiungere un nuovo utente a SQL Express][5]

  i. Fare clic con il pulsante destro del mouse sul nuovo account di accesso e scegliere **Proprietà**. j. Selezionare la pagina **Ruoli server**. k. Selezionare la casella accanto al ruolo server **dbcreator**. l. Fare clic su **OK**. m. Chiudere SQL Server 2015 Management Studio.

Prendere nota del nome utente e della password selezionati. Potrebbe essere necessario assegnare autorizzazioni o ruoli server aggiuntivi a seconda dei requisiti di database specifici.

L'applicazione Node.js legge la variabile di ambiente **SQLCONNSTR\_MS\_TableConnectionString** per leggere la stringa di connessione per il database. Questa impostazione può essere eseguita all'interno dell'ambiente. Ad esempio, è possibile usare PowerShell per impostare questa variabile di ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Si noti che è necessario accedere al database con una connessione TCP/IP e fornire un nome utente e una password per la connessione.

### <a name="howto-config-localdev"></a>Configurare il progetto per lo sviluppo locale

App per dispositivi mobili di Azure legge un file JavaScript denominato _azureMobile.js_ dal file system locale. Non usare questo file per configurare l'SDK per App per dispositivi mobili di Azure nell'ambiente di produzione. Usare invece Impostazioni app nel [portale di Azure]. Il file _azureMobile.js_ deve esportare un oggetto di configurazione. Le impostazioni più comuni sono:

- Impostazioni database
- Impostazioni di registrazione diagnostica
- Impostazioni CORS alternative

Di seguito è riportato un file _azureMobile.js_ di esempio che implementa le impostazioni di database specificate in precedenza:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'sql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

È consigliabile aggiungere _azureMobile.js_ al file con estensione _gitignore_ (o altro file ignore di controllo del codice sorgente) per evitare che le password vengano archiviate nel cloud. Configurare sempre le impostazioni di produzione in Impostazioni app nel [portale di Azure].

### <a name="howto-use-sqlazure"></a>Usare SQL Azure come archivio dati di produzione

<!-- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

L'uso di SQL Azure come archivio dati è identico in tutti i tipi di applicazione del Servizio app di Azure. Se non è già stato fatto, seguire questa procedura per creare un nuovo back-end dell'app per dispositivi mobili.

1. Accedere al [portale di Azure].

2. Nella parte superiore sinistra della finestra fare clic su **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end dell'app per dispositivi mobili.

3. Nella casella **Gruppo di risorse** immettere lo stesso nome dell'app.

4. Verrà selezionato il piano del servizio app predefinito. Per modificare il piano di servizio app, fare clic su Piano di servizio app > **+ Crea nuovo**. Specificare un nome del nuovo piano del servizio app e selezionare un percorso appropriato. Fare clic su Livello di prezzo e selezionare un livello di prezzo appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. Dopo avere scelto il piano tariffario, fare clic sul pulsante **Seleziona**. Nel pannello **Piano di servizio app** fare clic su **OK**.

5. Fare clic su **Crea**. In questo modo, viene creato un back-end dell'app per dispositivi mobili in cui verrà in seguito distribuito il progetto server. L'esecuzione del provisioning di un back-end dell'app per dispositivi mobili può richiedere alcuni minuti. Dopo l'esecuzione del provisioning del back-end dell'app per dispositivi mobili, nel portale viene aperto il pannello **Impostazioni** per il back-end dell'app per dispositivi mobili.

Una volta creato il back-end dell'app per dispositivi mobili, è possibile scegliere se connettere un database SQL Azure esistente o creare un nuovo database SQL Azure. In questa procedura viene creato un nuovo database SQL.

> [AZURE.NOTE]Se nella stessa posizione del nuovo back-end dell'app per dispositivi mobili è già presente un database, è possibile scegliere **Usare un database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in una posizione diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze più elevate.

6. Nel nuovo back-end dell'app per dispositivi mobili fare clic su **Impostazioni** > **App per dispositivi mobili** > **Dati** > **+Aggiungi**.

7. Nel pannello **Aggiungi connessione dati** fare clic su **Database SQL - Configura le impostazioni obbligatorie** > **Crea un nuovo database**. Immettere il nome del nuovo database nel campo **Nome**.

8. Fare clic su **Server**. Nel pannello **Nuovo server** immettere un nome di server univoco nel campo **Nome server** e specificare un **Account di accesso amministratore server** e una **Password** idonei. Verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata. Fare clic su **OK**.

	![Creare un database SQL Azure][6]

9. Nel pannello **Nuovo database** fare clic su **OK**.

10. Nel pannello **Aggiungi connessione dati** selezionare **Stringa di connessione** e immettere l'account di accesso e la password forniti al momento della creazione del database. Se si usa un database esistente, fornire le credenziali di accesso per il database. Dopo averli immessi fare clic su **OK**.

11. Nel pannello **Aggiungi connessione dati** fare nuovamente clic su **OK** per creare il database.

<!-- END OF ALTERNATE INCLUDE -->

La creazione del database può richiedere alcuni minuti. Usare l'area **Notifiche** per monitorare l'avanzamento della distribuzione. L'avanzamento non viene eseguito se il database non è stato distribuito correttamente. Al termine della distribuzione viene creata una stringa di connessione per l'istanza di database SQL Azure nelle impostazioni app del back-end mobile. È possibile visualizzare questa impostazione app in **Impostazioni** > **Impostazioni applicazione** > **Stringhe di connessione**.

### <a name="howto-tables-auth"></a>Richiedere l'autenticazione per l'accesso alle tabelle

Per usare l'autenticazione del servizio app con l'endpoint delle tabelle, è necessario prima configurare l'autenticazione del servizio app nel [portale di Azure]. Per altre informazioni sulla configurazione dell'autenticazione in un Servizio app di Azure, vedere la Guida alla configurazione per il provider di identità che si intende usare:

- [Come configurare l'autenticazione di Azure Active Directory]
- [Come configurare l'autenticazione di Facebook]
- [Come configurare l'autenticazione di Google]
- [Come configurare l'autenticazione di Microsoft]
- [Come configurare l'autenticazione di Twitter]

Ogni tabella ha una proprietà di accesso che può essere usata per controllare l'accesso alla tabella. L'esempio seguente mostra una tabella definita in modo statico in cui è richiesta l'autenticazione.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

La proprietà di accesso può assumere uno dei tre valori seguenti:

  - *anonymous* indica che all'applicazione client è consentito leggere i dati senza autenticazione.
  - *authenticated* indica che l'applicazione client deve inviare un token di autenticazione valido con la richiesta.
  - *disabled* indica che la tabella è attualmente disabilitata.

Se la proprietà di accesso non è definita, è consentito l'accesso non autenticato.

### <a name="howto-tables-disabled"></a>Disabilitare l'accesso a specifiche operazioni su tabella

Oltre che sulla tabella, la proprietà di accesso può essere usata per controllare singole operazioni. Sono disponibili quattro operazioni:

  - *read* è l'operazione RESTful GET nella tabella.
  - *insert* è l'operazione RESTful POST nella tabella.
  - *update* è l'operazione RESTful PATCH nella tabella.
  - *delete* è l'operazione RESTful DELETE nella tabella.

Si supponga, ad esempio, di voler fornire una tabella non autenticata di sola lettura. A questo scopo è possibile usare la definizione di tabella seguente:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Modificare la query usata con le operazioni su tabella

Un requisito comune per le operazioni su tabella è consentire una visualizzazione con restrizioni dei dati. Ad esempio, è possibile fornire una tabella contrassegnata con l'ID dell'utente autenticato in modo che l'utente possa solo leggere o aggiornare i propri record. La definizione di tabella riportata di seguito fornisce questa funzionalità:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
		context.query.where({ userId: context.user.id });
		return context.execute();
	});

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
	    context.item.userId = context.user.id;
	    return context.execute();
    }

    module.exports = table;

Le operazioni che in genere eseguono una query avranno una proprietà query regolabile con una clausola where. La proprietà query è un oggetto [QueryJS] usato per convertire una query OData in qualcosa che il back-end dei dati possa elaborare. Per i casi di semplice uguaglianza (come quello riportato in precedenza), è possibile usare una mappa. Anche aggiungere clausole SQL specifiche risulta relativamente semplice:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Configurare l'eliminazione temporanea in una tabella

L'eliminazione temporanea non elimina effettivamente i record. Al contrario, questi vengono contrassegnati come eliminati nel database impostando la colonna relativa all'eliminazione su true. L'SDK di App per dispositivi mobili di Azure rimuove automaticamente dai risultati i record con eliminazione temporanea, a meno che l'SDK del client mobile non usi il metodo IncludeDeleted(). Per configurare una tabella per l'eliminazione temporanea, impostare la proprietà softDelete nel file di definizione della tabella. Un esempio potrebbe essere:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
		"complete": "boolean"
	};

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Turn on Soft Delete
	table.softDelete = true;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

È necessario stabilire un meccanismo per l'eliminazione dei record, che sia da un'applicazione client oppure con un processo Web o un meccanismo personalizzato.

### <a name="howto-tables-seeding"></a>Eseguire il seeding dei dati nel database

Quando si crea una nuova applicazione, è consigliabile eseguire il seeding dei dati in una tabella. Questa operazione può essere eseguita all'interno del file JavaScript di definizione della tabella, come illustrato di seguito:

	var azureMobileApps = require('azure-mobile-apps');

	var table = azureMobileApps.table();

	// Define the columns within the table
	table.columns = {
		"text": "string",
		"complete": "boolean"
	};
	table.seed = [
		{ text: 'Example 1', complete: false },
		{ text: 'Example 2', complete: true }
	];

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

È importante notare che il seeding dei dati viene eseguito solo quando la tabella viene creata dall'SDK di App per dispositivi mobili di Azure. Se la tabella esiste già all'interno del database, non verranno inseriti dati nella tabella. Se lo schema dinamico è abilitato, lo schema viene dedotto dai dati di seeding.

È consigliabile chiamare in modo esplicito il metodo initialize() per creare la tabella all'avvio dell'esecuzione del servizio.

## <a name="CustomAPI"></a>API personalizzata

Oltre all'API di accesso ai dati attraverso l'endpoint /tables, App per dispositivi mobili di Azure può fornire la copertura per le API personalizzate. Le API personalizzate sono definite in modo analogo alle definizioni di tabella e possono accedere alle stesse funzionalità, inclusa l'autenticazione.

Per usare l'autenticazione del servizio app con un'API personalizzata, è necessario prima configurare l'autenticazione del servizio app nel [portale di Azure]. Per altre informazioni sulla configurazione dell'autenticazione in un Servizio app di Azure, vedere la Guida alla configurazione per il provider di identità che si intende usare:

- [Come configurare l'autenticazione di Azure Active Directory]
- [Come configurare l'autenticazione di Facebook]
- [Come configurare l'autenticazione di Google]
- [Come configurare l'autenticazione di Microsoft]
- [Come configurare l'autenticazione di Twitter]

### <a name="howto-customapi-basic"></a>Definire una semplice API personalizzata

Le API personalizzate vengono definite in modo molto simile alle API di tabella.

1. Creare una directory **api**.
2. Creare un file JavaScript di definizione dell'API nella directory **api**.
3. Usare il metodo di importazione per importare la directory **api**.

Di seguito è riportata la definizione dell'API prototipo basata sull'esempio di app di base usato in precedenza.

	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

Si prenda ad esempio un'API semplice che restituisce la data del server usando il metodo _Date.now()_. Il file api/date.js è il seguente:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

Ogni parametro è uno dei verbi RESTful standard: GET, POST, PATCH o DELETE. Il metodo è una funzione [ExpressJS Middleware] standard che invia l'output richiesto.

### <a name="howto-customapi-auth"></a>Richiedere l'autenticazione per l'accesso a un'API personalizzata

L'SDK di App per dispositivi mobili di Azure implementa l'autenticazione nello stesso modo sia per l'endpoint delle tabelle che per le API personalizzate. Per aggiungere l'autenticazione all'API sviluppata nella sezione precedente, aggiungere una proprietà **access**:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// All methods must be authenticated.
	api.access = 'authenticated';

	module.exports = api;

È anche possibile specificare l'autenticazione su operazioni specifiche:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

Lo stesso token usato per l'endpoint delle tabelle deve essere usato per le API personalizzate che richiedono l'autenticazione.

## <a name="Debugging"></a>Debug e risoluzione dei problemi

Il Servizio app di Azure offre diverse tecniche di debug e risoluzione dei problemi per le applicazioni Node.js. Sono disponibili tutte le tecniche seguenti:

- [Monitoraggio di un servizio app di Azure]
- [Abilitazione della registrazione diagnostica nel servizio app di Azure]
- [Risoluzione dei problemi di un Servizio app di Azure in Visual Studio]

### <a name="howto-diagnostic-logs"></a>Scrivere nei log di diagnostica di App per dispositivi mobili di Azure

Le applicazioni Node.js hanno accesso a un'ampia gamma di strumenti per i log di diagnostica. Al suo interno l'SDK di Node.js per App per dispositivi mobili di Azure usa [Winston] per la registrazione diagnostica. Questa opzione viene abilitata automaticamente abilitando la modalità di debug o impostando su true l'impostazione app **MS\_DebugMode** nel [portale di Azure]. I log generati verranno visualizzati tra i log di diagnostica del [portale di Azure].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Avvio rapido di client iOS]: app-service-mobile-ios-get-started.md
[Avvio rapido di client Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Avvio rapido di client Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Avvio rapido di client Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Avvio rapido di client Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[Avvio rapido di client HTML/JavaScript]: app-service-html-get-started.md
[sincronizzazione dati offline]: app-service-mobile-offline-data-sync.md
[Come configurare l'autenticazione di Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Come configurare l'autenticazione di Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Come configurare l'autenticazione di Google]: app-service-mobile-how-to-configure-google-authentication.md
[Come configurare l'autenticazione di Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare l'autenticazione di Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guida alla distribuzione del servizio app di Azure]: ../app-service-web/web-site-deploy.md
[Monitoraggio di un servizio app di Azure]: ../app-service-web/web-sites-monitor.md
[Abilitazione della registrazione diagnostica nel servizio app di Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Risoluzione dei problemi di un Servizio app di Azure in Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[specificare la versione di Node.js]: ../nodejs-specify-node-version-azure-apps.md
[usare i moduli di Node.js]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/

[portale di Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/it-IT/docs/Web/JavaScript/Reference/Global_Objects/Promise
[esempio basicapp in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[esempio todo in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[directory degli esempi in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Strumenti Node.js 1.1 per Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacchetto Node.js mssql]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/it-IT/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1125_2015-->