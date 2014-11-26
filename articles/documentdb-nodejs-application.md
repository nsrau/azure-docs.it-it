<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw" />

# <a name="_Toc395783175">Creare un'applicazione Node.js con DocumentDB</a>

<a name="_Toc395783175">

In questa esercitazione viene illustrato come usare il servizio Azure DocumentDB per archiviare e accedere ai dati
da un'applicazione Node.js Express ospitata in Siti Web di Azure.

In questa esercitazione si presuppone che l'utente abbia già usato l'applicazione Node.js.

Si apprenderà come:

· Usare gli strumenti Node.js per Visual Studio

· Lavorare con il servizio Azure DocumentDB usando il modulo npm di documentdb

· Distribuire l'applicazione Web in Siti Web di Azure

In questa esercitazione verrà creata una semplice
applicazione di gestione delle attività basata su Web che consente di creare, recuperare e
completare le attività. Le attività verranno archiviate come documenti JSON in Azure
DocumentDB.

![Alt text][Alt text]

## <a name="_Toc395783176">Prerequisiti</a>

Prima di seguire le istruzioni di questo articolo, verificare
che siano installati i seguenti elementi:

[Node.js][Node.js] v0.10.29 o versione successiva

[Git][Git]

[Visual Studio 2013][Visual Studio 2013] update 3

[Strumenti Node.js per Visual Studio][Strumenti Node.js per Visual Studio]

**Nota:** Sebbene in questa esercitazione si usi Visual Studio per la compilazione, il debug e la distribuzione del progetto
Node.js, è possibile usare un qualsiasi editor di propria scelta
ed eseguire Node.js direttamente su qualsiasi piattaforma nel modo in cui si eseguirebbe normalmente
un progetto Node.js. Sarà quindi possibile usare gli strumenti dell'[interfaccia della riga di comando di Azure][interfaccia della riga di comando di Azure] per
distribuire l'applicazione in Siti Web di Azure

# <a name="_Toc395637761">Creare un account di database di DocumentDB</a>

Per eseguire il provisioning di un account di database di DocumentDB in Azure, aprire il [portale di gestione di Azure][portale di gestione di Azure]
 e fare clic nel riquadro Raccolta di Azure nella home page oppure scegliere "+" nell'angolo inferiore sinistro dello schermo.

![Alt text][1]

Verrà aperta la Raccolta di Azure, da cui sarà possibile selezionare uno tra i molti
servizi di Azure disponibili. Nella raccolta scegliere "Dati, archiviazione, cache e
backup" dall'elenco delle categorie.

![Alt text][2]

Scegliere l'opzione relativa ad Azure DocumentDB

![Alt text][3]

Scegliere "Crea" nella parte inferiore dello schermo

![Alt text][4]

Verrà aperto il pannello "Nuovo DocumentDB" in cui sarà possibile specificare
nome, area, scala, gruppo di risorse e altre impostazioni per il nuovo
account.

![Alt text][5]

Dopo aver fornito i valori per l' account, fare clic su "Crea"
e il processo di provisioning avvierà la creazione dell'account.
Al termine del processo verrà visualizzata una notifica
nell'area di notifica del portale e il riquadro nella schermata
iniziale (se si è scelto di crearne una) verrà modificato per riflettere
il completamento dell'azione.

![Alt text][6]

Dopo il completamento del provisioning, facendo clic sul pannello DocumentDB della schermata
iniziale si avvierà il pannello principale per l'account
DocumentDB appena creato.

![Alt text][7]
![Alt text][8]

Usando il pulsante "Chiavi" accedere all'URL e alla chiave primaria del proprio endpoint,
copiare tali valori negli Appunti e mantenerli a portata di mano poiché verranno usati
nell'applicazione Web che verrà creata in seguito.

## <a name="_Toc395783178">Creare una nuova applicazione Node.js</a>

In Visual Studio, selezionare File, Nuovo progetto, quindi selezionare l'opzione per creare un'applicazione Express di base di Microsoft Azure.

![Alt text][9]

Verrà creata un'applicazione Express di base. Se viene chiesto se installare delle dipendenze
selezionare "Sì". Verranno installati tutti
i pacchetti npm necessari per una nuova applicazione Express.

Al termine dell'operazione, Esplora soluzioni dovrebbe avere un aspetto simile al
seguente.

![Alt text][10]

Qui viene mostrato che sono installati Express, Jade e Stylus.

Se si preme F5 in Visual Studio il progetto verrà compilato, quindi verrà avviato
Node.js e verrà visualizzato un browser con l'equivalente Express di "Hello
World"

![Alt text][11]

## <a name="_Toc395783179">Installare moduli aggiuntivi</a>

Il file **package.json** è uno di quelli creati nella radice del
progetto. Questo file contiene un elenco di moduli aggiuntivi
necessari per un'applicazione Express. In seguito, quando si distribuirà questa
applicazione in un sito Web di Azure, il file verrà usato per determinare
quali moduli devono essere installati in Azure per supportare l'applicazione.

![Alt text][12]

Per l'esercitazione, è necessario installare altri due pacchetti.

Fare clic con il pulsante destro del mouse su "npm" in Esplora soluzioni, quindi scegliere "Installa nuovi pacchetti
npm".

![Alt text][13]

Nella finestra di dialogo "Installa nuovi pacchetti npm" digitare **nconf** per cercare
il modulo, che verrà usato dall'applicazione per leggere i valori di configurazione del
database da un file di configurazione.

![Alt text][14]

Infine, installare il modulo Azure DocumentDB nello stesso modo, cercando
**documentdb**. Questo è il modulo in cui risiedono tutte le funzionalità avanzate di
DocumentDB.

Dopo avere installato questi due moduli aggiuntivi con le relative dipendenze,
questi verranno visualizzati in Esplora soluzioni sotto i moduli **npm**
.

![Alt text][15]

Da un rapido controllo del file **package.json** dell'applicazione dovrebbero risultare presenti
i moduli aggiuntivi. Questo è lo stesso file che indicherà più tardi ad Azure quali
pacchetti dovranno essere scaricati e installati quando si esegue
l'applicazione.

Se necessario, modificare il file package.json in modo da renderlo simile a quello mostrato nell'esempio seguente.

![Alt text][16]

Ciò indicherà a Node (e più avanti ad Azure) che l'applicazione dipende da questi
moduli aggiuntivi.

# <a name="_Toc395783180">Uso del servizio DocumentDB in un'applicazione Node</a>

Una volta completate le operazioni iniziali di installazione e configurazione, si può passare allo scopo effettivo di questa esercitazione, che consiste nello scrivere qualche riga di codice usando Azure DocumentDB. Per iniziare, modificare il file **app.js** che si trova nella radice dell'applicazione Express appena creata. Trovare le righe seguenti nel file:

    app.get('/', routes.index);
    app.get('/users', user.list);

e sostituirle con le due righe seguenti:

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Questo indica all'applicazione come operare con i metodi GET e POST predefiniti in un modulo che verrà creato a breve.

Individuare ora il file **index.js** nella cartella **routes**. Aprire questo file nell'editor ed eliminare tutto il codice presente al suo interno.

Aggiungere quanto segue all'inizio del file:

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Questo definisce i moduli che verranno usati, che sono **documentdb** e **nconf**.

**nconf** è un modulo che consente di caricare valori di configurazione, come le stringhe di connessione del database, da file esterni, anziché inserire tali valori nel codice. Per impostazione predefinita, nconf cercherà un file **config.json** per ottenere la configurazione.

Si procederà quindi alla creazione di un file di testo vuoto denominato **config.json** nella radice del progetto (lo stesso percorso in cui si trova app.js).

Aprire il nuovo file **config.json** e immettere i valori seguenti come appropriato per il proprio endpoint DocumentDB Assicurarsi che i valori HOST e MASTER\_KEY siano impostati correttamente.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Tornare ora a **index.js** e aggiungere le righe seguenti dopo le ultime righe per eseguire la lettura effettiva del file di configurazione e archiviare i valori in variabili a livello di pagina.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

Dopo questa operazione, continuare ad aggiungere codice a **index.js.**

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Queste sono le due funzioni per l'uso da parte dell'applicazione indicate nel file **app.js** in precedenza, quando sono state definite le route. Quando un GET raggiunge la visualizzazione dell'indice, viene eseguita la funzione **exports.index**. Analogamente, quando viene ricevuto un POST nella visualizzazione dell'indice, verrà eseguita la funzione **exports.createOfUpdateItem**.

Queste due funzioni eseguono tutto il lavoro dell'applicazione che si sta sviluppando. Tuttavia, queste eseguono chiamate ad altre funzioni, unicamente per rendere il codice più leggibile e facile da seguire. Continuare aggiungendo il codice seguente al file **index.js**. Esso contiene tutti i metodi usati dalle due funzioni sopra, nonché tutte le chiamate a DocumentDB. Ognuna delle funzioni verrà illustrata in maggiore dettaglio più avanti.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** - Aggiorna un documento nel database in base all'ID elemento passato dal modulo. Questo ID viene usato per eseguire un metodo ***readDocument*** su DocumentDB per leggere il documento specifico archiviato. L'attributo di completamento del documento viene quindi impostato su true, per indicarne appunto il completamento, e si passa a sostituire il documento nel database.

**getItem -** Usa ***queryDocuments*** per ottenere un singolo elemento dal database usando la proprietà ID dell'elemento stesso.

**createItem** - Usa il metodo ***createDocument*** per creare un nuovo documento nel database in base al nome e alla categoria dell'elemento immessi nel modulo. Imposta anche il flag di completamento su false per indicare che l'elemento non è ancora completo.

**listItems** - Usa ***queryDocuments*** per cercare tutti i documenti nella raccolta ancora non completati o per i quali completed=false. Viene usata la grammatica di query di DocumentDB, basata su ANSI-SQL, per dimostrare questa familiare ma potente funzionalità di query.

**readOrCreateDatabase** - Usa ***queryDatabases*** per controllare se esiste un database con lo stesso nome. Se non ne viene trovato uno, verrà usato ***createDatabase*** per crearne uno nuovo con l'identificatore fornito (dal file di configurazione) sull'endpoint specificato (anch'esso dal file di configurazione).

**readOrCreateCollection** - Come per readOrCreateDatabase, questo metodo cerca prima di trovare una raccolta con l'identificatore fornito: se ne esiste una, questa verrà restituita, mentre se non esiste, ne verrà creata una.

Questo codice è sufficiente per interagire con DocumentDB per questa applicazione di esempio.

È ora possibile passare alla realizzazione di un'interfaccia utente che consenta a un utente di interagire con l'applicazione. L'applicazione Express creata usa **Jade** come motore di visualizzazione. Per altre informazioni su Jade, vedere <http://jade-lang.com/>

Dalla cartella **views** aprire **layout.jade** e sostituire il contenuto con quello seguente:

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Ciò in effetti comunica al motore **Jade** di eseguire il rendering di una parte di HTML per l'applicazione e crea un "**blocco** denominato "**content**" tramite cui è possibile fornire il layout per le pagine di contenuto.

Aprire ora il file **index.jade** , la visualizzazione associata a index.js, quindi sostituirne il contenuto con quello seguente:

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Ciò estende il layout e fornisce contenuto per il segnaposto "block content"
già visto in precedenza nel file layout.jade.

In questo layout sono stati creati due moduli HTML. Il primo modulo contiene una tabella
per i dati e un pulsane che consente di aggiornare gli elementi. Il secondo contiene
alcuni campi di input e un pulsante che consente di creare un nuovo elemento.

Quando viene fatto clic su uno di questi pulsanti, viene creato un POST per il modulo tramite la chiamata della funzione **createOrUpdateItem**
 creata in precedenza nel file **index.js**
. Se questa pagina viene solo caricata direttamente (ossia viene eseguito un
GET) verrà eseguita la funzione **index**.

Ciò è tutto quanto è necessario per il funzionamento dell'applicazione.


# <a name="_Toc395783181">Esecuzione dell'applicazione in locale</a>

Per eseguire il test dell'applicazione nel computer locale, premere F5 in
Visual Studio. L'app verrà compilata, verrà avviato Node.js, quindi verrà avviato un browser con una
pagina simile a quella riportata di seguito:

![Alt text][17]

1. Usare i campi forniti per Item, Item Name e Category per l'immissione delle
informazioni, quindi fare clic su **Add item**.

2. La pagina verrà aggiornata e verrà visualizzato il nuovo elemento creato in
ToDo.List.

	![Alt text][18]

3. Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete,
quindi fare clic su **Update tasks.**


# <a name="_Toc395783182">Distribuire l'applicazione in Siti Web di Azure</a>

Con gli strumenti Node.js per Visual Studio installati, la distribuzione in Siti Web di
Azure potrà essere eseguita in pochi brevi passaggi.

Fare clic con il pulsante destro del mouse sul progetto e scegliere "Pubblica".

![Alt text][19]

Seguire la Pubblicazione guidata per fornire la configurazione richiesta per il proprio sito Web di
Azure. La procedura guidata consentirà di scegliere un sito Web esistente
da aggiornare o di crearne uno nuovo.

Una volta fornita la configurazione necessaria, fare clic su "Pubblica".

![Alt text][20]

Seguire la Pubblicazione guidata per fornire la configurazione richiesta per il proprio sito Web di
Azure. La procedura guidata consentirà di scegliere un sito Web esistente
da aggiornare o di crearne uno nuovo.

Una volta fornita la configurazione necessaria, fare clic su "Pubblica".

Visual Studio effettuerà la connessione alla sottoscrizione di Azure e pubblicherà l'applicazione
Node.js.

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione
Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro
in esecuzione in Azure.

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Conclusioni</a>

Congratulazioni. Si è creata la prima applicazione Web Express Node.js
usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure.

Il codice sorgente per l'applicazione di riferimento completa è disponibile per il download [qui][qui].

</h1>

  [Alt text]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/it-it/vstudio/cc136611.aspx
  [Strumenti Node.js per Visual Studio]: https://nodejstools.codeplex.com/
  [interfaccia della riga di comando di Azure]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/
  [portale di gestione di Azure]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [qui]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
