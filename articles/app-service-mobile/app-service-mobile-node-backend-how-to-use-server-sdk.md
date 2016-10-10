<properties
	pageTitle="Come usare l'SDK del server back-end di Node.js per App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare l'SDK del server back-end di Node.js per App per dispositivi mobili del servizio app di Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="erikre"
	editor=""/>  

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="adrianha"/>  

# Come usare Node.js SDK per App per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Questo articolo fornisce informazioni dettagliate ed esempi sull'uso di un back-end Node.js nelle app per dispositivi mobili del servizio app di Azure.

## <a name="Introduction"></a>Introduzione

App per dispositivi mobili del servizio app di Azure consente di aggiungere un'API Web di accesso ai dati ottimizzata per dispositivi mobili a un'applicazione Web. Azure App Service Mobile Apps SDK viene fornito per le applicazioni Web Node.js e ASP.NET. L'SDK consente le operazioni seguenti:

- Operazioni su tabella (read, insert, update, delete) per l'accesso ai dati
- Operazioni sulle API personalizzate

Entrambe le operazioni permettono l'autenticazione in tutti i provider di identità consentiti dal servizio app di Azure, inclusi i provider di identità basati su social network, ad esempio Facebook, Twitter, Google e Microsoft nonché Azure Active Directory per l'identità aziendale.

Esempi per ogni caso d'uso sono disponibili nella [directory degli esempi in GitHub].

## Piattaforme supportate

Node SDK per App per dispositivi mobili di Azure supporta la versione LTS corrente di Node e le versioni successive. Al momento della stesura di questo testo, la versione LTS più recente di è Node v4.5.0. Altre versioni di Node potrebbero funzionare, ma non sono supportate.

Node SDK per App per dispositivi mobili di Azure supporta due driver di database, il driver node-mssql supporta SQL Azure e le istanze locali di SQL Server. Il driver sqlite3 supporta i database SQLite solo su un'unica istanza.

### <a name="howto-cmdline-basicapp"></a>Creare un back-end Node.js di base usando la riga di comando

Ogni back-end Node.js per le app per dispositivi mobili del servizio app di Azure viene avviato come applicazione ExpressJS. ExpressJS è il framework dei servizi Web più diffuso disponibile per Node.js. È possibile creare un'applicazione [Express] di base seguendo questa procedura:

1. Creare una directory per il progetto in una finestra di comando o di PowerShell.

        mkdir basicapp

2. Eseguire npm init per inizializzare la struttura del pacchetto.

        cd basicapp
        npm init

    Il comando npm init pone una serie di domande per inizializzare il progetto. Vedere l'output di esempio:

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

Questa applicazione crea un'API Web ottimizzata per dispositivi mobili con un endpoint singolo, `/tables/TodoItem`, che consente l'accesso non autenticato a un archivio dati SQL sottostante usando uno schema dinamico. È adatta per l'avvio rapido delle librerie client seguenti:

- [Avvio rapido di client Android]
- [Avvio rapido di client Apache Cordova]
- [Avvio rapido di client iOS]
- [Avvio rapido di client Windows Store]
- [Avvio rapido di client Xamarin.iOS]
- [Avvio rapido di client Xamarin.Android]
- [Avvio rapido di client Xamarin.Forms]

Il codice per questa applicazione di base è disponibile nell'[esempio basicapp in GitHub].

### <a name="howto-vs2015-basicapp"></a>Procedura: Creare un back-end Node.js con Visual Studio 2015

Visual Studio 2015 richiede un'estensione per lo sviluppo di applicazioni Node.js all'interno dell'IDE. Per iniziare, installare gli [Strumenti Node.js 1.1 per Visual Studio]. Dopo aver installato gli Strumenti Node.js per Visual Studio, creare un'applicazione Express 4.x:

1. Aprire la finestra di dialogo **New Project** (da **File** > **New** > **Project**).

2. Espandere **Modelli** > **JavaScript** > **Node.js**.

3. Selezionare **Basic Azure Node.js Express 4 Application**.

4. Immettere il nome del progetto. Fare clic su *OK*.

	![Nuovo progetto di Visual Studio 2015][1]  

5. Fare clic sul nodo **npm** e selezionare **Install New npm packages**.

6. Al momento della creazione della prima applicazione Node.js potrebbe essere necessario aggiornare il catalogo npm. Fare clic su **Refresh** (Aggiorna), se necessario.

7. Immettere _azure-mobile-apps_ nella casella di ricerca. Fare clic sul pacchetto **azure-mobile-apps 2.0.0**, quindi fare clic su **Install Package**.

	![Installare nuovi pacchetti npm][2]  

8. Fare clic su **Close**.

9. Aprire il file _app.js_ per aggiungere il supporto per l'SDK per App per dispositivi mobili di Azure. Nella riga 6 nella parte inferiore delle istruzioni require della libreria aggiungere il codice seguente:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Circa alla riga 27 dopo le altre istruzioni app.use aggiungere il codice seguente:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Salvare il file.

10. Eseguire l'applicazione in locale, ovvero l'API viene eseguita in http://localhost:3000, o pubblicarla in Azure.

### <a name="create-node-backend-portal"></a>Procedura: Creare un back-end Node.js usando il portale di Azure

È possibile creare un back-end dell'app per dispositivi mobili direttamente nel [portale di Azure]. È possibile seguire i passaggi seguenti o creare un client e un nuovo server seguendo l'esercitazione [Creare un'app per dispositivi mobili](app-service-mobile-ios-get-started.md). L'esercitazione contiene una versione semplificata di queste istruzioni e dimostra i progetti del concetto.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Nel pannello _Attività iniziali_, in **Creare un'API di tabella** scegliere **Node.js** come **Linguaggio back-end**. Selezionare la casella per "**Sono consapevole che questa operazione comporterà la sovrascrittura di tutti i contenuti del sito.**" e fare clic su **Crea tabella TodoItem**.

### <a name="download-quickstart"></a>Procedura: Scaricare il progetto di codice di avvio rapido del back-end Node.js tramite Git

Quando si crea un back-end dell'app per dispositivi mobili Node.js usando il pannello **Avvio rapido** del portale, viene creato e distribuito nel sito un progetto Node.js. È possibile aggiungere tabelle e API e modificare i file di codice per il back-end Node.js nel portale. Si possono anche usare vari strumenti di distribuzione per scaricare il progetto back-end, per poter aggiungere o modificare tabelle e API, quindi ripubblicare il progetto. Per altre informazioni, vedere la [Guida alla distribuzione del servizio app di Azure]. La procedura seguente usa un repository Git per scaricare il codice del progetto di avvio rapido.

1. Se non è già stato fatto, installare Git. I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

2. Seguire i passaggi in [Abilitare il repository dell'app del servizio app](../app-service-web/web-sites-deploy-local-git.md#Step3) per abilitare il repository Git per il sito di back-end, prendendo nota del nome utente e della password della distribuzione.

3. Nel pannello di back-end dell'app per dispositivi mobili annotare l'impostazione **URL clone Git**.

4. Eseguire il comando `git clone` usando l'URL clone Git e immettendo la password quando richiesto, come nell'esempio seguente:

		$ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Passare alla directory locale, che nell'esempio precedente è /todolist e osservare che sono stati scaricati i file di progetto. Individuare il file `todoitem.json` nella directory `/tables`. Questo file definisce le autorizzazioni sulla tabella. Individuare il file `todoitem.js` nella stessa directory, che definisce gli script di operazioni CRUD per la tabella.

6. Dopo che sono state apportate modifiche ai file di progetto, eseguire i comandi seguenti per aggiungere, eseguire il commit delle modifiche e quindi caricare nel sito:

		$ git commit -m "updated the table script"
		$ git push origin master

	Quando si aggiungono nuovi file al progetto, è necessario prima di tutto eseguire il comando `git add .`.

Il sito viene ripubblicato ogni volta che viene effettuato il push di un nuovo set di commit al sito.

### <a name="howto-publish-to-azure"></a>Procedura: Pubblicare il back-end Node.js in Azure

Microsoft Azure offre numerosi meccanismi per la pubblicazione del back-end Node.js per le app per dispositivi mobili del servizio app di Azure, tra cui l'uso di strumenti di distribuzione integrati in Visual Studio, strumenti da riga di comando e opzioni di distribuzione continua basate sul controllo del codice sorgente. Per altre informazioni su questo argomento, vedere la [Guida alla distribuzione del servizio app di Azure].

Il servizio app di Azure include suggerimenti specifici per l'applicazione Node.js che è consigliabile esaminare prima della distribuzione:

- Come [specificare la versione di Node.js]
- Come [usare i moduli di Node]

### <a name="howto-enable-homepage"></a>Procedura: Abilitare una home page dell'applicazione

Molte applicazioni sono una combinazione di app Web e per dispositivi mobili e il framework ExpressJS consente di combinare i due aspetti. In alcuni casi, tuttavia, è consigliabile implementare solo un'interfaccia per dispositivi mobili. È utile fornire una pagina di destinazione per garantire il servizio app sia in esecuzione. È possibile fornire la propria home page o abilitarne una temporanea. Per abilitare una home page temporanea, usare quanto segue per creare un'istanza di App per dispositivi mobili di Azure:

    var mobile = azureMobileApps({ homePage: true });

È possibile aggiungere questa impostazione al file `azureMobile.js` se si vuole che questa opzione sia disponibile solo quando si sviluppa in locale.

## <a name="TableOperations"></a>Operazioni su tabella 

Node.js Server SDK (azure-mobile-apps) offre diversi meccanismi per esporre le tabelle dati archiviate nel database SQL di Azure come API Web. Sono disponibili cinque operazioni.

| Operazione | Descrizione |
| --------- | ----------- |
| GET /tables/_tablename_ | Ottiene tutti i record nella tabella |
| GET /tables/_tablename_/:id | Ottiene un record specifico nella tabella |
| POST /tables/_tablename_ | Creare un record nella tabella |
| PATCH /tables/_tablename_/:id | Aggiornare un record nella tabella |
| DELETE /tables/_tablename_/:id | Elimina un record dalla tabella |

Questa API Web supporta [OData] ed estende lo schema della tabella per supportare la [sincronizzazione dati offline].

### <a name="howto-dynamicschema"></a>Procedura: Definire le tabelle con uno schema dinamico

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

Per impostazione predefinita, le tabelle usano lo schema dinamico. Per disattivare lo schema dinamico a livello globale, impostare su false l'impostazione dell'app **MS\_DynamicSchema** nel portale di Azure.

Per un esempio completo, vedere l'[esempio todo in GitHub].

### <a name="howto-staticschema"></a>Procedura: Definire le tabelle con uno schema statico

È possibile definire in modo esplicito le colonne da esporre con l'API Web. Node.js SDK (azure-mobile-apps) aggiunge automaticamente tutte le altre colonne necessarie per la sincronizzazione dati offline all'elenco specificato. Ad esempio, le applicazioni client di avvio rapido richiedono una tabella con due colonne: text (una stringa) e complete (un valore booleano). Questa tabella può essere specificata nel file JavaScript di definizione della tabella, presente nella directory delle tabelle, nel modo seguente:

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

Se si definiscono le tabelle in modo statico, è necessario chiamare il metodo tables.initialize() per creare lo schema del database all'avvio. Il metodo tables.initialize() restituisce un oggetto [Promise] in modo che il servizio Web non gestisca le richieste prima dell'inizializzazione del database.

### <a name="howto-sqlexpress-setup"></a>Procedura: Usare SQL Express come archivio dati di sviluppo nel computer locale

Node SDK per le app per dispositivi mobili di Azure offre tre opzioni predefinite per la gestione dei dati:

- Usare il driver **memory** per fornire un archivio di esempio non persistente
- Usare il driver **mssql** per fornire un archivio dati di SQL Express per lo sviluppo
- Usare il driver **mssql** per fornire un archivio dati del database SQL di Azure per la produzione

Node.js SDK per le app per dispositivi mobili di Azure usa il [pacchetto mssql per Node.js] per stabilire e usare una connessione a SQL Express e al database SQL di Azure. Per questo pacchetto è necessario abilitare le connessioni TCP nell'istanza di SQL Express.

> [AZURE.TIP] Il driver memory non fornisce un set completo di funzionalità per i test. Per testare il back-end in locale, è consigliabile usare un archivio dati di SQL Express con il driver mssql.

1. Scaricare e installare [Microsoft SQL Server 2014 Express]. Assicurarsi di installare l'edizione SQL Server 2014 Express with Tools. A meno che non sia richiesto in modo esplicito il supporto a 64 bit, l'esecuzione della versione a 32 bit richiede una quantità di memoria inferiore.

2. Eseguire Gestione configurazione SQL Server 2014.

  1. Espandere il nodo **Configurazione di rete SQL Server** nel menu ad albero a sinistra.
  2. Fare clic su **Protocolli per SQLEXPRESS**.
  3. Fare clic con il pulsante destro del mouse su **TCP/IP** e scegliere **Abilita**. Fare clic su **OK** nella finestra di dialogo popup.
  4. Fare clic con il pulsante destro del mouse su **TCP/IP** e scegliere **Proprietà**.
  5. Fare clic sulla scheda **Indirizzi IP**.
  6. Trovare il nodo **IPAll**. Nel campo **Porta TCP** immettere **1433**.

	 	 ![Configure SQL Express for TCP/IP][3]

  7. Fare clic su **OK**. Fare clic su **OK** nella finestra di dialogo popup.
  8. Fare clic su **Servizi di SQL Server** nel menu ad albero a sinistra.
  9. Fare clic con il pulsante destro del mouse su **SQL Server (SQLEXPRESS)** e scegliere **Riavvia**
  10. Chiudere Gestione configurazione SQL Server 2014.

3. Eseguire SQL Server 2014 Management Studio e connettersi all'istanza locale di SQL Express

  1. Fare clic con il pulsante destro del mouse sull'istanza in Esplora oggetti e scegliere **Proprietà**
  2. Selezionare la pagina **Sicurezza**.
  3. Assicurarsi che l'opzione **Modalità di autenticazione di SQL Server e di Windows** sia selezionata.
  4. Fare clic su **OK**.

  		![Configure SQL Express Authentication][4]

  5. In Esplora oggetti espandere **Sicurezza** > **Account di accesso**.
  6. Fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account di accesso**
  7. Immettere un nome account di accesso. Selezionare **Autenticazione di SQL Server**. Immettere una password e quindi immetterla di nuovo in **Conferma password**. La password deve soddisfare i requisiti di complessità di Windows.
  8. Fare clic su **OK**.

  		![Add a new user to SQL Express][5]

  9. Fare clic con il pulsante destro del mouse sul nuovo account di accesso e scegliere **Proprietà**.
  10. Selezionare la pagina **Ruoli server**.
  11. Selezionare la casella accanto al ruolo server **dbcreator**.
  12. Fare clic su **OK**.
  13. Chiudere SQL Server 2015 Management Studio.

Prendere nota del nome utente e della password selezionati. Potrebbe essere necessario assegnare autorizzazioni o ruoli del server aggiuntivi a seconda dei requisiti di database specifici.

L'applicazione Node.js legge la variabile di ambiente **SQLCONNSTR\_MS\_TableConnectionString** per la stringa di connessione per il database. Questa variabile può essere impostata all'interno dell'ambiente. Ad esempio, è possibile usare PowerShell per impostare questa variabile di ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Accedere al database con una connessione TCP/IP e fornire un nome utente e una password per la connessione.

### <a name="howto-config-localdev"></a>Procedura: Configurare il progetto per lo sviluppo locale

App per dispositivi mobili di Azure legge un file JavaScript denominato _azureMobile.js_ dal file system locale. Non usare questo file per configurare Azure Mobile Apps SDK nell'ambiente di produzione. Usare invece Impostazioni app nel [portale di Azure]. Il file _azureMobile.js_ deve esportare un oggetto di configurazione. Le impostazioni più comuni sono:

- Impostazioni database
- Impostazioni di registrazione diagnostica
- Impostazioni CORS alternative

Di seguito è riportato un file _azureMobile.js_ di esempio che implementa le impostazioni del database seguenti:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

È consigliabile aggiungere _azureMobile.js_ al file con estensione _gitignore_, o altro file IGNORE di controllo del codice sorgente, per evitare che le password vengano archiviate nel cloud. Configurare sempre le impostazioni di produzione in Impostazioni app nel [portale di Azure].

### <a name="howto-appsettings"></a>Procedura: Configurare le impostazioni dell'app per dispositivi mobili

Quasi tutte le impostazioni nel file _azureMobile.js_ hanno un'impostazione app equivalente nel [portale di Azure]. Usare l'elenco seguente per configurare l'app in Impostazioni app:

| Impostazione app | Impostazione di _azureMobile.js_ | Descrizione | Valori validi |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS\_MobileAppName** | name | Nome dell'app | string |
| **MS\_MobileLoggingLevel** | logging.level | Livello log minimo di messaggi da registrare | error, warning, info, verbose, debug, silly |
| **MS\_DebugMode** | debug | Abilitare o disabilitare la modalità di debug | true, false |
| **MS\_TableSchema** | data.schema | Nome dello schema predefinito per le tabelle SQL | string (valore predefinito: dbo) |
| **MS\_DynamicSchema** | data.dynamicSchema | Abilitare o disabilitare la modalità di debug | true, false |
| **MS\_DisableVersionHeader** | version (impostata su undefined)| Disabilita l'intestazione X-ZUMO-Server-Version | true, false |
| **MS\_SkipVersionCheck** | skipversioncheck | Disabilita il controllo della versione dell'API client | true, false |

Per definire un'impostazione app:

1. Accedere al [Portale di Azure].
2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome dell'app per dispositivi mobili.
3. Per impostazione predefinita si apre il pannello Impostazioni. In caso contrario fare clic su **Impostazioni**.
4. Fare clic su **Impostazioni dell'applicazione** nel menu GENERALE.
5. Scorrere fino alla sezione Impostazioni app.
6. Se l'impostazione app esiste già, fare clic sul valore dell'impostazione dell'app per modificarlo.
7. Se l'impostazione app non esiste, immettere l'impostazione app nella casella Chiave e il valore nella casella Valore.
8. Al termine, fare clic su **Salva**.

Per modificare la maggior parte delle impostazioni dell'app, è necessario riavviare il servizio.

### <a name="howto-use-sqlazure"></a>Procedura: Usare il database SQL come archivio dati di produzione

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

L'uso del database SQL di Azure come archivio dati è identico in tutti i tipi di applicazione del Servizio app di Azure. Se non è già stato fatto, seguire questa procedura per creare un back-end dell'app per dispositivi mobili.

1. Accedere al [Portale di Azure].

2. Nella parte superiore sinistra della finestra fare clic sul pulsante **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end dell'app per dispositivi mobili.

3. Nella casella **Gruppo di risorse** immettere lo stesso nome dell'app.

4. Viene selezionato il piano di servizio app predefinito. Per modificare il piano di servizio app, fare clic su Piano di servizio app > **+ Crea nuovo**. Specificare un nome del nuovo piano di servizio app e selezionare un percorso appropriato. Fare clic su Piano tariffario e selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. Dopo avere scelto il piano tariffario, fare clic sul pulsante **Seleziona**. Nel pannello **Piano di servizio app** fare clic su **OK**.

5. Fare clic su **Crea**. L'operazione di provisioning di un back-end dell'app per dispositivi mobili può richiedere alcuni minuti. Dopo avere eseguito il provisioning del back-end dell'app per dispositivi mobili, nel portale viene aperto il pannello **Impostazioni** relativo al back-end dell'app per dispositivi mobili.

Una volta creato il back-end dell'app per dispositivi mobili, è possibile scegliere se connettere un database SQL esistente o creare un nuovo database SQL. In questa sezione viene creato un database SQL.

> [AZURE.NOTE] Se nella stessa posizione del back-end dell'app per dispositivi mobili è già presente un database, è possibile scegliere **Usare un database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in una posizione diversa, a causa di latenze più elevate.

6. Nel nuovo back-end dell'app per dispositivi mobili fare clic su **Impostazioni** > **App per dispositivi mobili** > **Dati** > **+Aggiungi**.

7. Nel pannello **Aggiungi connessione dati** fare clic su **Database SQL - Configurare le impostazioni necessarie** > **Crea un nuovo database**. Immettere il nome del nuovo database nel campo **Nome**.

8. Fare clic su **Server**. Nel pannello **Nuovo server** immettere un nome di server univoco nel campo **Nome server** e specificare un **Account di accesso amministratore server** e una **Password** idonei. Verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata. Fare clic su **OK**.

	![Creare un database SQL di Azure][6]  

9. Nel pannello **Nuovo database** fare clic su **OK**.

10. Nel pannello per **Aggiungi connessione dati** selezionare **Stringa di connessione** e immettere l'account di accesso e la password forniti al momento della creazione del database. Se si usa un database esistente, fornire le credenziali di accesso per il database. Dopo averli immessi fare clic su **OK**.

11. Nel pannello **Aggiungi connessione dati** fare nuovamente clic su **OK** per creare il database.

<!--- END OF ALTERNATE INCLUDE -->  

La creazione del database può richiedere alcuni minuti. Usare l'area **Notifiche** per monitorare l'avanzamento della distribuzione. L'avanzamento non viene eseguito se il database non è stato distribuito correttamente. Al termine della distribuzione viene creata una stringa di connessione per l'istanza di database SQL nelle impostazioni dell'app del back-end mobile. È possibile visualizzare l'impostazione dell'app in **Impostazioni** > **Impostazioni applicazione** > **Stringhe di connessione**.

### <a name="howto-tables-auth"></a>Procedura: Richiedere l'autenticazione per l'accesso alle tabelle

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

  - *anonymous* indica che all'applicazione client è consentito leggere i dati senza autenticazione
  - *authenticated* indica che l'applicazione client deve inviare un token di autenticazione valido con la richiesta
  - *disabled* indica che la tabella è attualmente disabilitata.

Se la proprietà di accesso non è definita, è consentito l'accesso non autenticato.

### <a name="howto-tables-getidentity"></a>Procedura: Usare le attestazioni di autenticazione con le tabelle

È possibile impostare diverse attestazioni richieste quando viene impostata l'autenticazione. Queste attestazioni non sono in genere disponibili tramite l'oggetto `context.user`. È possibile tuttavia recuperarle usando il metodo `context.user.getIdentity()`. Il metodo `getIdentity()` restituisce una promessa che viene risolta in un oggetto. L'oggetto viene associato a una chiave tramite il metodo di autenticazione (facebook, google, twitter, microsoftaccount o aad).

Ad esempio, se si configura l'autenticazione dell'account Microsoft e si richiede l'attestazione degli indirizzi di posta elettronica, è possibile aggiungere l'indirizzo di posta elettronica nel record con il controller tabelle seguente:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Per visualizzare le attestazioni disponibili, usare un browser Web per visualizzare l'endpoint `/.auth/me` del sito.

### <a name="howto-tables-disabled"></a>Procedura: Disabilitare l'accesso a specifiche operazioni su tabella

Oltre che sulla tabella, la proprietà di accesso può essere usata per controllare singole operazioni. Sono disponibili quattro operazioni:

  - *read* è l'operazione RESTful GET nella tabella.
  - *insert* è l'operazione POST RESTful sulla tabella
  - *update* è l'operazione PATCH RESTful sulla tabella
  - *delete* è l'operazione RESTful DELETE nella tabella.

Si supponga, ad esempio, di voler fornire una tabella non autenticata di sola lettura:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Procedura: Modificare la query usata con le operazioni su tabella

Un requisito comune per le operazioni su tabella è consentire una visualizzazione con restrizioni dei dati. Ad esempio, è possibile fornire una tabella contrassegnata con l'ID dell'utente autenticato in modo sia possibile solo leggere o aggiornare i propri record. La definizione di tabella riportata di seguito fornisce questa funzionalità:

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
    });

    module.exports = table;

Le operazioni che in genere eseguono una query avranno una proprietà query modificabile con una clausola where. La proprietà query è un oggetto [QueryJS] usato per convertire una query OData in qualcosa che il back-end dei dati possa elaborare. Per i casi di semplice uguaglianza, come quello riportato in precedenza, è possibile usare una mappa. È anche possibile aggiungere clausole SQL specifiche:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Procedura: Configurare l'eliminazione temporanea in una tabella

L'eliminazione temporanea non elimina effettivamente i record. Al contrario, questi vengono contrassegnati come eliminati nel database impostando la colonna relativa all'eliminazione su true. L'SDK di App per dispositivi mobili di Azure rimuove automaticamente dai risultati i record con eliminazione temporanea, a meno che l'SDK del client mobile non usi il metodo IncludeDeleted(). Per configurare una tabella per l'eliminazione temporanea, impostare la proprietà `softDelete` nel file di definizione della tabella:

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

È necessario stabilire un meccanismo per l'eliminazione dei record, che sia da un'applicazione client, con un processo Web, tramite Funzioni di Azure o un'API personalizzata.

### <a name="howto-tables-seeding"></a>Procedura: Eseguire il seeding dei dati nel database

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

Il seeding dei dati viene eseguito solo quando la tabella viene creata dall'SDK di App per dispositivi mobili di Azure. Se la tabella esiste già all'interno del database, non verranno inseriti dati nella tabella. Se lo schema dinamico è abilitato, lo schema viene dedotto dai dati di seeding.

È consigliabile chiamare in modo esplicito il metodo `tables.initialize()` per creare la tabella all'avvio dell'esecuzione del servizio.

### <a name="Swagger"></a>Procedura: Abilitare il supporto di Swagger

Le app per dispositivi mobili del servizio app di Azure sono fornite con il supporto di [Swagger] incorporato. Per abilitare il supporto di Swagger, installare prima di tutto swagger-ui come una dipendenza:

    npm install --save swagger-ui

Una volta installato, è possibile abilitare il supporto di Swagger nel costruttore di app per dispositivi mobili di Azure:

    var mobile = azureMobileApps({ swagger: true });

È consigliabile abilitare il supporto di Swagger solo nelle edizioni di sviluppo. È possibile farlo usando l'impostazione dell'app `NODE_ENV`:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

L'endpoint di Swagger si trova in http://_yoursite_.azurewebsites.net/swagger. È possibile accedere all'interfaccia utente di Swagger tramite l'endpoint `/swagger/ui`. Se si sceglie di richiedere l'autenticazione a livello dell'intera applicazione., Swagger genera un errore. Per ottenere risultati ottimali, scegliere di consentire le richieste non autenticate tramite le impostazioni di autenticazione/autorizzazione del Servizio app di Azure e quindi controllare l'autenticazione con la proprietà `table.access`.

È anche possibile aggiungere l'opzione Swagger al file `azureMobile.js` se si vuole supportare Swagger solo quando si sviluppa in locale.

## <a name="push">Notifiche push

App per dispositivi mobili si integra con Hub di notifica di Azure per consentire l'invio di notifiche push mirate a milioni di dispositivi basati sulle piattaforme principali. Con Hub di notifica è possibile inviare notifiche push a dispositivi iOS, Android e Windows. Per altre informazioni su tutte le operazioni disponibili con Hub di notifica, vedere [Panoramica dell'Hub di notifica di Azure](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Procedura: Inviare notifiche push

Il codice seguente illustra come usare l'oggetto push per inviare una notifica push di trasmissione ai dispositivi iOS registrati:

	// Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
	    // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
	        }
        });
    }

Creando una registrazione push con modello dal client, è possibile inviare un messaggio di push con modello ai dispositivi basati su tutte le piattaforme supportate. Il codice seguente illustra come inviare una notifica con modello:

	// Define the template payload.
	var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
		// Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>Procedura: Inviare notifiche push agli utenti autenticati tramite tag

Se un utente autenticato esegue la registrazione per le notifiche push, viene automaticamente aggiunto un tag con l'ID utente. Tramite questo tag, è possibile inviare notifiche push a tutti i dispositivi registrati da un utente specifico. Il codice seguente ottiene il SID dell'utente che esegue la richiesta e invia un modello di notifica push a ogni registrazione del dispositivo per tale utente:

    // Only do the push if configured
    if (context.push) {
		// Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Durante la registrazione per le notifiche push da un client autenticato, assicurarsi che l'autenticazione sia stata completata prima di tentare la registrazione.

## <a name="CustomAPI"></a> API personalizzate

###  <a name="howto-customapi-basic"></a>Procedura: Definire un'API personalizzata


Oltre all'API di accesso ai dati attraverso l'endpoint /tables, App per dispositivi mobili di Azure può fornire la copertura per le API personalizzate. Le API personalizzate sono definite in modo analogo alle definizioni di tabella e possono accedere alle stesse funzionalità, inclusa l'autenticazione.

Per usare l'autenticazione del servizio app con un'API personalizzata, è necessario prima configurare l'autenticazione del servizio app nel [portale di Azure]. Per altre informazioni sulla configurazione dell'autenticazione in un Servizio app di Azure, vedere la Guida alla configurazione per il provider di identità che si intende usare:

- [Come configurare l'autenticazione di Azure Active Directory]
- [Come configurare l'autenticazione di Facebook]
- [Come configurare l'autenticazione di Google]
- [Come configurare l'autenticazione di Microsoft]
- [Come configurare l'autenticazione di Twitter]

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

Si prenda ad esempio un'API che restituisce la data del server usando il metodo _Date.now()_. Il file api/date.js è il seguente:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

Ogni parametro è uno dei verbi RESTful standard: GET, POST, PATCH o DELETE. Il metodo è una funzione [ExpressJS Middleware] standard che invia l'output richiesto.

### <a name="howto-customapi-auth"></a>Procedura: Richiedere l'autenticazione per l'accesso a un'API personalizzata

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
		}
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

Lo stesso token usato per l'endpoint delle tabelle deve essere usato per le API personalizzate che richiedono l'autenticazione.

### <a name="howto-customapi-auth"></a>Procedura: Gestire il caricamento di file di grandi dimensioni

L'SDK delle app per dispositivi mobili di Azure usa il [middleware body-parser](https://github.com/expressjs/body-parser) per accettare e decodificare il contenuto del corpo nell'elemento inviato. È possibile preconfigurare il body-parser per accettare caricamenti di file più grandi:

	var express = require('express'),
        bodyParser = require('body-parser'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

Il file ha una codifica in base 64 prima della trasmissione, che aumenterà le dimensioni del caricamento effettivo e di cui è quindi necessario tenere conto.

### <a name="howto-customapi-sql"></a>Procedura: Eseguire istruzioni SQL personalizzate

Con Azure Mobile App SDK è possibile accedere all'intero contesto tramite l'oggetto request, consentendo di eseguire facilmente istruzioni SQL con parametri sul provider di dati definito:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Debug, tabelle semplici e API semplici

### <a name="howto-diagnostic-logs"></a>Procedura: Eseguire il debug e diagnosticare e risolvere i problemi di App per dispositivi mobili di Azure

Il Servizio app di Azure offre diverse tecniche di debug e risoluzione dei problemi per le applicazioni Node.js. Per iniziare la risoluzione dei problemi del back-end Node.js Mobile, consultare i seguenti articoli:

- [Monitoraggio di un servizio app di Azure]
- [Abilitazione della registrazione diagnostica nel servizio app di Azure]
- [Risoluzione dei problemi di un Servizio app di Azure in Visual Studio]

Le applicazioni Node.js hanno accesso a un'ampia gamma di strumenti per i log di diagnostica. Al suo interno l'SDK di Node.js per App per dispositivi mobili di Azure usa [Winston] per la registrazione diagnostica. La registrazione viene abilitata automaticamente abilitando la modalità di debug o impostando su true l'impostazione dell'app **MS\_DebugMode** nel [portale di Azure]. I log generati vengono visualizzati tra i log di diagnostica del [portale di Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Procedura: Usare tabelle semplici nel portale di Azure

L'impostazione Easy Tables nel portale consente di creare e usare tabelle direttamente nel portale. Consente anche di modificare le operazioni di tabella usando l'editor del servizio app.

Quando si fa clic su **Tabelle semplici** nelle impostazioni del sito di back-end, è possibile aggiungere, modificare o eliminare una tabella. È anche possibile visualizzare i dati nella tabella.

![Utilizzare Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)  

I comandi seguenti sono disponibili sulla barra dei comandi di una tabella:

+ **Modifica autorizzazioni**: è possibile modificare l'autorizzazione per le operazioni di lettura, inserimento, aggiornamento ed eliminazione di operazioni sulla tabella. Le opzioni consentono di eseguire l'accesso anonimo, richiedere l'autenticazione o disabilitare qualunque tipo di accesso all'operazione.
+ **Modifica script**: il file di script per la tabella viene aperto nell'editor del servizio app.
+ **Gestisci schema**: è possibile aggiungere o eliminare le colonne o modificare l'indice di tabella.
+ **Cancella tabella**: tronca una tabella esistente eliminando tutte le righe di dati ma lasciando lo schema invariato.
+ **Elimina righe**: è possibile eliminare singole righe di dati.
+ **Visualizzare log di streaming**: consente di connettersi al servizio di log in streaming del sito.

###<a name="work-easy-apis"></a>Procedura: Usare Easy APIs nel portale di Azure

L'impostazione Easy APIs nel portale consentono di creare e usare API personalizzate direttamente nel portale. È possibile modificare gli script dell'API usando l'editor del servizio app.

Quando si fa clic su **API semplici** nelle impostazioni del sito di back-end, è possibile aggiungere, modificare o eliminare un endpoint API personalizzato.

![Utilizzare Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)  

Nel portale è possibile modificare le autorizzazioni di accesso per una determinata azione HTTP, modificare il file di script dell'API nell'editor del servizio app o visualizzare i log in streaming.

###<a name="online-editor"></a>Procedura: Modificare il codice nell'editor del servizio app

Il portale di Azure consente di modificare i file di script del back-end Node.js nell'editor del servizio app senza dover scaricare il progetto nel computer locale. Per modificare i file di script nell'editor online:

1. Nel pannello del back-end delle app per dispositivi mobili fare clic su **Tutte le impostazioni** > **Easy tables** o **Easy APIs**, fare clic su una tabella o un'API e quindi su **Modifica script**. Il file di script viene aperto nell'editor del servizio app.

	![Editor del servizio app](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)  

2. Apportare le modifiche al file di codice nell'editor online. Le modifiche vengono salvate automaticamente durante la digitazione.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Avvio rapido di client Android]: app-service-mobile-android-get-started.md
[Avvio rapido di client Apache Cordova]: app-service-mobile-cordova-get-started.md
[Avvio rapido di client iOS]: app-service-mobile-ios-get-started.md
[Avvio rapido di client Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Avvio rapido di client Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Avvio rapido di client Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Avvio rapido di client Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[sincronizzazione dati offline]: app-service-mobile-offline-data-sync.md
[Come configurare l'autenticazione di Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Come configurare l'autenticazione di Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Come configurare l'autenticazione di Google]: app-service-mobile-how-to-configure-google-authentication.md
[Come configurare l'autenticazione di Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare l'autenticazione di Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guida alla distribuzione del servizio app di Azure]: ../app-service-web/web-sites-deploy.md
[Monitoraggio di un servizio app di Azure]: ../app-service-web/web-sites-monitor.md
[Abilitazione della registrazione diagnostica nel servizio app di Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Risoluzione dei problemi di un Servizio app di Azure in Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[specificare la versione di Node.js]: ../nodejs-specify-node-version-azure-apps.md
[usare i moduli di Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[portale di Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/it-IT/docs/Web/JavaScript/Reference/Global_Objects/Promise
[esempio basicapp in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[esempio todo in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[directory degli esempi in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Strumenti Node.js 1.1 per Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacchetto mssql per Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/it-IT/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0928_2016-->