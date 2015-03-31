<properties 
	pageTitle="Uso di un servizio mobile back-end JavaScript" 
	description="Esempi su come definire, registrare e usare script del server in Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ricksal"/>


# Uso di un servizio mobile back-end JavaScript

<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend">Back-end .NET</a> | <a href="/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>
 
Questo articolo fornisce informazioni dettagliate ed esempi sull'uso di un back-end JavaScript in Servizi mobili di Azure. 

## <a name="intro"></a>Introduzione

In un servizio mobile back-end JavaScript è possibile definire la logica di business personalizzata come codice JavaScript memorizzato ed eseguito sul server. Questo codice di script del server è assegnato a una delle seguenti funzionalità server:

+ [Operazioni di inserimento, lettura, aggiornamento o eliminazione su una determinata tabella][Operazioni su tabella].
+ [Processi pianificati][Utilità di pianificazione processi].
+ [Metodi HTTP definiti in un'API personalizzata][Ancoraggio di API personalizzate]. 

La firma della funzione principale dello script del server dipende dal contesto di cui viene usato lo script. È inoltre possibile definire codice di script comune come moduli nodes.js condivisi tra gli script. Per altre informazioni, vedere [Controllo del codice sorgente e codice condiviso][Controllo del codice sorgente, codice condiviso e funzioni di supporto].

Per le descrizioni delle singole funzioni e dei singoli oggetti di script del server, vedere [Riferimento per gli script server di Servizi mobili][Informazioni di riferimento sugli script del server di Servizi mobili]. 


## <a name="table-scripts"></a>Operazioni su tabella

Uno script di operazione su tabella è uno script del server registrato in un'operazione su una tabella, ovvero&mdash;insert, read, update o delete (*del*). Questa sezione descrive come usare le operazioni su tabella in un back-end JavaScript e include le seguenti sezioni:

+ [Informazioni generali sulle operazioni su tabella][operazioni su tabella di base]
+ [Procedura: Eseguire la registrazione per le operazioni su tabelle]
+ [Procedura: Eseguire l'override della risposta predefinita]
+ [Procedura: Eseguire l'override della riuscita di execute]
+ [Procedura: Eseguire l'override della gestione degli errori predefinita]
+ [Procedura: Generare valori ID univoci](#generate-guids)
+ [Procedura: Aggiungere parametri personalizzati]
+ [Procedura: Usare gli utenti di tabella][Procedura: Usare gli utenti]

### <a name="basic-table-ops"></a>Panoramica delle operazioni su tabella

Il nome dello script deve corrispondere il tipo di operazione per cui è registrato. È possibile registrare un solo script per una determinata operazione su tabella. Lo script viene eseguito ogni volta che l'operazione in questione viene richiamata da una richiesta REST, ad esempio quando viene ricevuta una richiesta POST di inserire un elemento nella tabella. Servizi mobili non mantiene lo stato tra le esecuzioni degli script. Dato che ogni volta che viene eseguito uno script viene creato un nuovo contesto globale, le variabili di stato definite nello script vengono reinizializzate. Se si desidera archiviare lo stato da una richiesta a un'altra, creare una tabella nel servizio mobile e quindi leggere e scrivere lo stato nella tabella. Per altre informazioni, vedere [Procedura: Accedere alle tabelle dagli script].

Gli script di operazioni su tabelle vengono scritti se è necessario applicare la logica di business personalizzata quando viene eseguita l'operazione. Il seguente script, ad esempio, rifiuta le operazioni di inserimento in cui la lunghezza della stringa del campo `text` è superiore a dieci caratteri: 

	function insert(item, user, request) {
	    if (item.text.length > 10) {
	        request.respond(statusCodes.BAD_REQUEST, 
				'Text length must be less than 10 characters');
	    } else {
	        request.execute();
	    }
	}

Una funzione di script di tabella accetta sempre tre argomenti.

- Il primo argomento varia a seconda dell'operazione di tabella. 

	- Per gli inserimenti e gli aggiornamenti, si tratta di un oggetto **item**, ovvero una rappresentazione JSON della riga interessata dall'operazione. Questo consente di accedere ai valori di colonna per nome, ad esempio *item.Owner*, dove *Owner* è uno dei nomi nella rappresentazione JSON.
	- Per un'eliminazione, si tratta dell'ID del record da eliminare. 
	- Come per una lettura, è un [oggetto query] che specifica il set di righe da restituire.

- Il secondo argomento è sempre un [oggetto user][oggetto user] che rappresenta l'utente che ha inviato la richiesta. 

- Il terzo argomento è sempre un [oggetto request][Oggetto request] con cui è possibile controllare l'esecuzione dell'operazione richiesta e la risposta inviata al client.

Qui di seguito sono indicate le firme di funzioni principali canoniche per le operazioni su tabella: 

+ [Insert][funzione insert]: `function insert (item, user, request) { ... }`
+ [Update][funzione update]: `function update (item, user, request) { ... }`
+ [Delete][funzione delete]: `function del (id, user, request) { ... }`
+ [Read][funzione read]: `function read (query, user, request) { ... }`

>[AZURE.NOTE]Una funzione registrata per l'operazione di eliminazione deve essere denominata _del_ perché delete è una parola chiave riservata in JavaScript. 

Ogni script del server include una funzione principale e può avere funzioni di supporto facoltative. Anche se è stato creato per una tabella specifica, uno script può fare riferimento anche ad altre tabelle nello stesso database. È inoltre possibile definire funzioni comuni come moduli nodes.js che possono essere condivisi tra gli script. Per altre informazioni, vedere [Controllo del codice sorgente e codice condiviso][Controllo del codice sorgente, codice condiviso e funzioni di supporto].

### <a name="register-table-scripts"></a>Procedura: Registrare script di tabella

Gli script del server registrati per un'operazione su tabella possono essere definiti in uno dei seguenti modi:

+ Nel [portale di gestione di Azure][portale di gestione]. Gli script per le operazioni su tabella sono accessibili nella scheda **Script** per una determinata tabella. Di seguito viene illustrato il codice predefinito registrato nello script insert per la tabella `TodoItem`. È possibile sostituire questo codice con la logica di business personalizzata.

	![1][1]
	
	Per informazioni su come effettuare questa operazione, vedere [Convalidare e modificare i dati in Servizi mobili mediante script del server].  

+ Mediante il controllo del codice sorgente. Dopo aver abilitato il controllo del codice sorgente, è sufficiente creare un file denominato <em>`<table>`</em>.<em>`<operation>`</em>.js nella sottocartella .\service\table del repository Git, dove <em>`<table>`</em> è il nome della tabella e <em>`<operation>`</em> è l'operazione su tabella da registrare. Per altre informazioni, vedere [Controllo del codice sorgente e codice condiviso][Controllo del codice sorgente, codice condiviso e funzioni di supporto].

+ Dal prompt dei comandi usando lo strumento da riga di comando di Azure. Per altre informazioni, vedere [Uso dello strumento da riga di comando].


Uno script di operazione su tabella deve chiamare almeno una delle seguenti funzioni dell'[oggetto request] per fare in modo che il client riceva una risposta. 
 
+ **Funzione execute**: l'operazione viene completata come richiesto e viene restituita la risposta standard.
 
+ **Funzione respond**: viene restituita una risposta personalizzata.

> [AZURE.IMPORTANT] Quando uno script include un percorso di codice in cui non viene richiamata né la funzione **execute** né la funzione **respond**, è possibile che l'operazione si blocchi.

Il seguente script chiama la funzione **execute** per completare l'operazione dati richiesta dal client: 

	function insert(item, user, request) { 
	    request.execute(); 
	}

In questo esempio l'elemento viene inserito nel database e viene restituito il codice di stato appropriato all'utente. 

Quando viene chiamata la funzione **execute**, per eseguire l'operazione viene usato il valore `item`, [query][oggetto query], o `id` passato come primo argomento nella funzione script. Per un'operazione di inserimento, aggiornamento o query, è possibile modificare l'elemento o la query prima di chiamare **execute**: 

	function insert(item, user, request) { 
	    item.scriptComment =
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 
 
	function update(item, user, request) { 
	    item.scriptComment = 
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 

	function read(query, user, request) { 
		// Only return records for the current user 	    
		query.where({ userid: user.userId}); 
	    request.execute(); 
	}
 
>[AZURE.NOTE]In uno script di eliminazione, la modifica del valore della variabile userId fornita non influisce sul record che viene eliminato.

Per altri esempi, vedere [Leggere e scrivere dati], [Modificare la richiesta] e [Convalidare i dati].


### <a name="override-response"></a>Procedura: Eseguire l'override della risposta predefinita

È anche possibile usare uno script per implementare una logica di convalida che può sostituire il comportamento di risposta predefinito. Se la convalida non riesce, è sufficiente chiamare la funzione **respond** anziché la funzione **execute** e scrivere la risposta al client: 

	function insert(item, user, request) {
	    if (item.userId !== user.userId) {
	        request.respond(statusCodes.FORBIDDEN, 
	        'You may only insert records with your userId.');
	    } else {
	        request.execute();
	    }
	}

In questo esempio la richiesta viene rifiutata quando l'elemento inserito non include una proprietà `userId` corrispondente alla proprietà `userId` dell'[oggetto user] fornito per il client autenticato. In questo caso, non si verifica un'operazione di database (*insert*) e al client vengono restituiti una risposta con codice di stato 403 HTTP e un messaggio di errore personalizzato. Per altri esempi, vedere [Modificare la risposta].

### <a name="override-success"></a>Procedura: Eseguire l'override della riuscita di execute

Per impostazione predefinita in un'operazione su tabella, la funzione **execute** scrive automaticamente le risposte. Tuttavia, è possibile passare due parametri opzionali per la funzione execute che eseguono l'override del relativo comportamento in caso di riuscita e/o errore.

Passando un gestore **success** quando si chiama execute, è possibile modificare i risultati di una query prima di scriverli nella risposta. Il seguente esempio chiama `execute({ success: function(results) { ... })` per eseguire operazioni aggiuntive dopo la lettura dei dati dal database ma prima della scrittura della risposta:

	function read(query, user, request) {
	    request.execute({
	        success: function(results) {
	            results.forEach(function(r) {
	                r.scriptComment = 
	                'this was added by a script after querying the database';
	            });
	            request.respond();
	        }
	    });
	}

Quando si fornisce un gestore **success** alla funzione **execute**, è necessario anche chiamare la funzione **respond** nell'ambito del gestore **success** per comunicare al runtime che lo script è stato completato e che è possibile scrivere una risposta. Quando si chiama **respond** senza passare argomenti, Servizi mobili genera la risposta predefinita. 

>[AZURE.NOTE]È possibile chiamare **respond** senza argomenti per richiamare la risposta predefinita solo dopo avere prima chiamato la funzione **execute**.
 
### <a name="override-error"></a>Procedura: Eseguire l'override della gestione degli errori predefinita

La funzione **execute** può avere esito negativo in caso di perdita di connettività al database, in presenza di un oggetto non valido o di una query non corretta. Per impostazione predefinita, quando si verifica un errore gli script del server registrano l'errore e scrivono un risultato di errore nella risposta. Poiché Servizi mobili fornisce la gestione predefinita degli errori, non è necessario gestire gli errori che possono verificarsi nel servizio. 

È possibile sostituire la gestione predefinita degli errori implementando la gestione degli errori esplicita se si desidera impostare una specifica azione di compensazione o usare l'oggetto console globale per scrivere informazioni più dettagliate nel log. A tale scopo è necessario fornire un gestore **error** alla funzione **execute**:

	function update(item, user, request) { 
	  request.execute({ 
	    error: function(err) { 
	      // Do some custom logging, then call respond. 
	      request.respond(); 
	    } 
	  }); 
	}
 

Quando si fornisce un gestore di errori, Servizi mobili restituisce un risultato di errore al client quando viene chiamata la funzione **respond**.

Se necessario, è anche possibile fornire sia un gestore **success** che un gestore **error**.

### <a name="generate-guids"></a>Procedura: Generare valori ID univoci

Servizi mobili supporta valori stringa univoci personalizzati per la colonna di tabella **id**. Ciò consente alle applicazioni di usare valori personalizzati come indirizzi e-mail o nomi utente per l'ID. 

Gli ID stringa offrono i seguenti vantaggi:

+ È possibile generare ID senza generare un round trip del database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Quando un valore ID di stringa non è impostato su un record inserito, Servizi mobili genera un valore univoco per l'ID. È possibile generare valori ID univoci negli script del server. Il seguente esempio di script genera un GUID personalizzato e lo assegna all'ID di un nuovo record. Tale ID sarà simile al valore ID generato da Servizi mobili se non si è passato un valore per l'ID di un record.

	// Example of generating an id. This is not required since Mobile Services
	// will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Il valore di ID specificato da un'applicazione verrà archiviato da Servizi mobili così com'è. Sono inclusi spazi vuoti iniziali o finali. Lo spazio vuoto non viene rimosso dal valore.

Il valore per `id` deve essere univoco e non deve includere caratteri appartenenti ai seguenti set:

+ Caratteri di controllo: [0x0000-0x001F] e [0x007F-0x009F]. Per altre informazioni, vedere la pagina relativa ai [codici di controllo ASCII C0 e C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
+  Caratteri stampabili: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Gli ID "." e ".."

È inoltre possibile usare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario creare la tabella usando il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene usato con l'interfaccia della riga di comando (CLI) per Azure. Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili](/documentation/articles/virtual-machines-command-line-tools/#Mobile_Tables).


### <a name="access-headers"></a>Procedura: Accedere ai parametri personalizzati

Quando si invia una richiesta al servizio mobile, è possibile includere parametri personalizzati nell'URI della richiesta per indicare agli script delle operazioni su tabella come elaborare una determinata richiesta. In seguito si modifica lo script per esaminare il parametro e determinare il percorso di elaborazione.

Ad esempio, il seguente URI per una richiesta POST indica al servizio di non consentire l'inserimento di un nuovo *TodoItem* con lo stesso valore di testo:

		https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

Questi parametri di query personalizzati sono accessibili come valori JSON dalla proprietà **parameters** dell'[oggetto request]. L'oggetto **request** viene fornito da Servizi mobili a qualsiasi funzione registrata per un'operazione su tabella. Il seguente script del server per l'operazione di inserimento controlla il valore del parametro `duplicateText` prima dell'esecuzione dell'operazione di inserimento:

		function insert(item, user, request) {
		    var todoItemTable = tables.getTable('TodoItem');
		    // Check the supplied custom parameter to see if
		    // we should allow duplicate text items to be inserted.		   
		    if (request.parameters.duplicateText === 'false') {
		        // Find all existing items with the same text
		        // and that are not marked 'complete'. 
		        todoItemTable.where({
		            text: item.text,
		            complete: false
		        }).read({
		            success: insertItemIfNotComplete
		        });
		    } else {
		        request.execute();
		    }

		    function insertItemIfNotComplete(existingItems) {
		        if (existingItems.length > 0) {
		            request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
		        } else {
		            // Insert the item as normal. 
		            request.execute();
		        }
		    }
		}

Si noti che in **insertItemIfNotComplete** viene richiamata la funzione **execute** dell'[oggetto request] per inserire l'elemento in assenza di testo duplicato, altrimenti viene richiamata la funzione **respond** per notificare al client la presenza del duplicato. 

Si noti la sintassi della chiamata alla funzione **success** nel codice riportato sopra:

 		        }).read({
		            success: insertItemIfNotComplete
		        });

In JavaScript si tratta di una versione compatta dell'equivalente più lungo: 

		success: function(results) 
		{ 
			insertItemIfNotComplete(results); 
		}


### <a name="work-with-users"></a>Procedura: Usare gli utenti

In Servizi mobili di Azure è possibile usare un provider di identità per autenticare gli utenti. Per altre informazioni, vedere [Introduzione all'autenticazione]. Quando un utente autenticato richiama un'operazione su tabella, Servizi mobili usa l'[oggetto user] per fornire informazioni sull'utente alla funzione dello script registrato. La proprietà **userId** può essere usata per archiviare e recuperare informazioni specifiche sull'utente. Nel seguente esempio viene impostata la proprietà owner di un elemento in base alla proprietà userId di un utente autenticato:

	function insert(item, user, request) {
	    item.owner = user.userId;
	    request.execute();
	}

Nel seguente esempio viene aggiunto un filtro supplementare alla query in base alla proprietà **userId** di un utente autenticato. Questo filtro limita il risultato ai soli elementi che appartengono all'utente corrente:  

	function read(query, user, request) {
	    query.where({
	        owner: user.userId
	    });
	    request.execute();
	}

## <a name="custom-api"></a>API personalizzate

Questa sezione descrive come creare e usare gli endpoint delle API personalizzate e include le seguenti sezioni: 
	
+ [Informazioni generali sulle API personalizzate](#custom-api-overview)
+ [Procedura: Definire un'API personalizzata]
+ [Procedura: Implementare metodi HTTP]
+ [Procedura: Inviare e ricevere dati come XML]
+ [Procedura: Usare utenti e intestazioni in un'API personalizzata]
+ [Procedura: Definire più route in un'API personalizzata]

### <a name="custom-api-overview"></a>Informazioni generali sulle API personalizzate

Un'API personalizzata è un endpoint nel servizio mobile a cui è possibile accedere mediante uno o più metodi HTTP standard: GET, POST, PUT, PATCH, DELETE. È possibile definire un'esportazione di funzioni separata per ogni metodo HTTP supportato dall'API personalizzata, in un unico file di script. Lo script registrato viene richiamato quando viene ricevuta una richiesta all'API personalizzata mediante il metodo specificato. Per altre informazioni, vedere [API personalizzata].

Quando le funzioni API personalizzate vengono chiamate dal runtime di Servizi mobili, vengono forniti sia un oggetto [request][oggetto request] che un oggetto [response][oggetto response]. Questi oggetti espongono la funzionalità della [libreria express.js], che può essere sfruttata dagli script. L'API personalizzata riportata di seguito, denominata **hello**, è un esempio molto semplice che restituisce _Hello, world!_ in risposta a una richiesta POST:

		exports.post = function(request, response) {
		    response.send(200, "{ message: 'Hello, world!' }");
		} 

La funzione **send** nell'[oggetto response] restituisce la risposta desiderata al client. Questo codice viene richiamato inviando una richiesta POST al seguente URL:

		https://todolist.azure-mobile.net/api/hello  

Lo stato globale viene mantenuto tra le esecuzioni. 

### <a name="define-custom-api"></a>Procedura: Definire un'API personalizzata

Gli script del server registrati nei metodi HTTP in un endpoint API personalizzata possono essere definiti in uno dei seguenti modi:

+ Nel [portale di gestione di Azure][portale di gestione]. Gli script di API personalizzate vengono creati e modificati nella scheda **API**. Il codice di script del server si trova nella scheda **Script** di una specifica API personalizzata. Di seguito è riportato lo script che viene richiamato da una richiesta POST all'endpoint dell'API personalizzata `CompleteAll`. 

	![2][2]
	
	Le autorizzazioni di accesso ai metodi delle API personalizzate vengono assegnate nella scheda Autorizzazioni. Per informazioni su come è stata creata questa API personalizzata, vedere [Chiamare un'API personalizzata dal client].  

+ Mediante il controllo del codice sorgente. Dopo aver abilitato il controllo del codice sorgente, è sufficiente creare un file denominato <em>`<custom_api>`</em>.js nella sottocartella .\service\api del repository Git, dove <em>`<custom_api>`</em> è il nome dell'API personalizzata da registrare. Il file di script contiene una funzione _exported_ per ogni metodo HTTP esposto dall'API personalizzata. Le autorizzazioni sono definite in un file con estensione json complementare. Per altre informazioni, vedere [Controllo del codice sorgente e codice condiviso][Controllo del codice sorgente, codice condiviso e funzioni di supporto].

+ Dal prompt dei comandi usando lo strumento da riga di comando di Azure. Per altre informazioni, vedere [Uso dello strumento da riga di comando].

### <a name="handle-methods"></a>Procedura: Implementare metodi HTTP

Un'API personalizzata è in grado di gestire uno o più metodi HTTP, ovvero GET, POST, PUT, PATCH e DELETE. Per ogni metodo HTTP gestito dall'API personalizzata viene definita una funzione esportata. Un singolo file di codice dell'API personalizzata può esportare una o tutte le seguenti funzioni:

		exports.get = function(request, response) { ... };
		exports.post = function(request, response) { ... };
		exports.patch = function(request, response) { ... };
		exports.put = function(request, response) { ... };
		exports.delete = function(request, response) { ... };

L'endpoint API personalizzata non può essere chiamato usando un metodo HTTP non implementato nello script server e viene restituita una risposta di errore 405 (Metodo non consentito). È possibile assegnare livelli di autorizzazione separati per ogni metodo HTTP di supporto.

### <a name="api-return-xml"></a>Procedura: Inviare e ricevere dati come XML

Quando i client archiviano e recuperano dati, Servizi mobili usa JavaScript Object Notation (JSON) per rappresentare i dati nel corpo del messaggio. In alcuni casi, tuttavia, è preferibile usare un payload XML. Ad esempio, le app di Windows Store dispongono di una funzionalità di notifica periodica incorporata che richiede al servizio di generare codice XML. Per altre informazioni, vedere [Definizione di un'API personalizzata che supporta le notifiche periodiche][Definire un'API personalizzata che supporta le notifiche periodiche].

La seguente funzione API personalizzata **OrderPizza** restituisce un semplice documento XML come payload di risposta:

		exports.get = function(request, response) {
		  response.set('content-type', 'application/xml');
		  var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
		  response.send(200, xml);
		};

Questa funzione API personalizzata viene richiamata da una richiesta HTTP GET al seguente endpoint:

		https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a>Procedura: Usare utenti e intestazioni in un'API personalizzata

In Servizi mobili di Azure è possibile usare un provider di identità per autenticare gli utenti. Per altre informazioni, vedere [Introduzione all'autenticazione]. Quando un utente autenticato richiede un'API personalizzata, Servizi mobili usa l'[oggetto user] per fornire informazioni sull'utente al codice dell'API personalizzata. L'[oggetto user] è accessibile dalla proprietà user dell'[oggetto request]. La proprietà **userId** può essere usata per archiviare e recuperare informazioni specifiche sull'utente. 

Nella seguente funzione API personalizzata **OrderPizza** viene impostata la proprietà owner di un elemento in base alla proprietà userId di un utente autenticato:

		exports.post = function(request, response) {
			var userTable = request.service.tables.getTable('user');
			userTable.lookup(request.user.userId, {
				success: function(userRecord) {
					callPizzaAPI(userRecord, request.body, function(orderResult) {
						response.send(201, orderResult);
					});
				}
			});
		
		};

Questa funzione API personalizzata viene richiamata da una richiesta HTTP POST al seguente endpoint:

		https://<service>.azure-mobile.net/api/orderpizza

È anche possibile accedere a una specifica intestazione HTTP dall'[oggetto request], come mostrato nel seguente codice:

		exports.get = function(request, response) {    
    		var header = request.header('my-custom-header');
    		response.send(200, "You sent: " + header);
		};

In questo semplice esempio viene letta un'intestazione personalizzata denominata `my-custom-header` e il valore viene restituito nella risposta.

### <a name="api-routes"></a>Procedura: Definire più route in un'API personalizzata

Servizi mobili consente di definire più percorsi, o route, in un'API personalizzata. Ad esempio, le richiesta HTTP GET ai seguenti URL in un'API personalizzata **calculator** richiameranno rispettivamente una funzione **add** o **subtract**: 

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

Per definire più route viene esportata una funzione **register**, a cui viene passato un oggetto **api** (analogo all'[oggetto express in express.js]) usato per registrare route nell'endpoint API personalizzata. Nel seguente esempio vengono implementati i metodi **add** e **sub** nell'API personalizzata **calculator**: 

		exports.register = function (api) {
		    api.get('add', add);
		    api.get('sub', subtract);
		}
		
		function add(req, res) {
		    var result = parseInt(req.query.a) + parseInt(req.query.b);
		    res.send(200, { result: result });
		}
		
		function subtract(req, res) {
		    var result = parseInt(req.query.a) - parseInt(req.query.b);
		    res.send(200, { result: result });
		}

L'oggetto **api** passato alla funzione **register** espone una funzione per ogni metodo HTTP (**get**, **post**, **put**, **patch**, **delete**). Queste funzioni registrano una route su una funzione definita per un metodo HTTP specifico. Ogni funzione accetta due parametri, il primo è il nome della route e il secondo è la funzione registrata per la route. 

Le due route nell'esempio di API personalizzata precedente possono essere richiamate mediante richieste HTTP GET, come indicato di seguito (viene mostrata anche la risposta):

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

		{"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

		{"result":-2}

## <a name="scheduler-scripts"></a>Utilità di pianificazione processi

Servizi mobili consente di definire script del server che vengono eseguiti come processi secondo una pianificazione fissa oppure su richiesta dal portale di gestione. I processi pianificati sono utili per eseguire attività periodiche, quali la pulizia della tabella dati e l'elaborazione batch. Per altre informazioni, vedere [Pianificare i processi].

Gli script registrati per i processi pianificati hanno una funzione principale il cui nome corrisponde a quello del processo pianificato. Poiché uno script pianificato non viene richiamato da una richiesta HTTP, non esiste un contesto che può essere passato dal runtime del server e la funzione non accetta parametri. Come per altri tipi di script, possono essere presenti funzioni di subroutine e possono essere richiesti moduli condivisi. Per altre informazioni, vedere [Controllo del codice sorgente, codice condiviso e funzioni di supporto].

### <a name="scheduler-scripts"></a>Procedura: Definire script di processi pianificati

È possibile assegnare uno script del server a un processo definito nell'utilità di pianificazione di Servizi mobili. Questi script appartengono al processo e vengono eseguiti in base alla pianificazione del processo. È anche possibile usare il [portale di gestione] per eseguire processi su richiesta. Uno script che definisce un processo pianificato non include parametri perché non riceve dati da Servizi mobili; esso viene eseguito come una normale funzione JavaScript e non interagisce direttamente con Servizi mobili. 

Per definire processi pianificati è possibile procedere in uno dei seguenti modi: 

+ Nel [portale di gestione di Azure][Portale di gestione] nella scheda **Script** dell'utilità di pianificazione:

	![3][3]

	Per altre informazioni a questo proposito, vedere [Pianificare processi back-end in Servizi mobili]. 

+ Dal prompt dei comandi usando lo strumento da riga di comando di Azure. Per altre informazioni, vedere [Uso dello strumento da riga di comando].

>[AZURE.NOTE]Quando il controllo del codice sorgente è abilitato, i file di script dei processi pianificati possono essere modificati direttamente nella sottocartella .\service\scheduler del repository Git. Per altre informazioni, vedere [Procedura: Condividere il codice usando il controllo del codice sorgente].

## <a name="shared-code"></a>Controllo del codice sorgente, codice condiviso e funzioni di supporto

Questa sezione illustra come usare il controllo del codice sorgente per aggiungere moduli Node.js personalizzati, codice condiviso e altre strategie di riutilizzo del codice e include le seguenti sezioni:

+ [Informazioni generali sull'uso di codice condiviso](#leverage-source-control)
+ [Procedura: Caricare moduli Node.js]
+ [Procedura: Usare le funzioni di supporto]
+ [Procedura: Condividere il codice usando il controllo del codice sorgente]
+ [Procedura: Usare le impostazioni app] 

### <a name="leverage-source-control"></a>Informazioni generali sull'uso di codice condiviso

Dato che Servizi mobili usa Node.js sul server, gli script hanno già accesso ai moduli Node.js incorporati. Controllo del codice sorgente consente inoltre di definire moduli personalizzati o di aggiungere altri moduli Node.js al servizio.

Di seguito sono riportati solo alcuni dei moduli più utili che possono essere sfruttati negli script mediante la funzione **require** globale:

+ **azure**: espone la funzionalità di Azure SDK per Node.js. Per altre informazioni, vedere [Azure SDK per Node.js]. 
+ **crypto**: fornisce la funzionalità crittografica di OpenSSL. Per altre informazioni, vedere la [documentazione di Node.js][API crypto].
+ **path**: contiene utilità per l'uso dei percorsi di file. Per altre informazioni, vedere la [documentazione di Node.js][API path].
+ **querystring**: contiene utilità per l'uso delle stringhe di query. Per altre informazioni, vedere la [documentazione di Node.js][API querystring].
+ **request**: invia richieste HTTP a servizi REST esterni, ad esempio Twitter e Facebook. Per altre informazioni, vedere [Inviare una richiesta HTTP].
+ **sendgrid**: invia posta elettronica usando il servizio di posta elettronica Sendgrid in Azure. Per altre informazioni, vedere [Inviare posta elettronica da Servizi mobili con SendGrid][Inviare e-mail da Servizi mobili con SendGrid].
+ **url**: contiene utilità per l'analisi e la risoluzione degli URL. Per altre informazioni, vedere la [documentazione di Node.js][API url].
+ **util**: contiene varie utilità, ad esempio la formattazione di stringhe e il controllo dei tipi di oggetto. Per altre informazioni, vedere la [documentazione di Node.js][API util]. 
+ **zlib**: espone la funzionalità di compressione, ad esempio gzip e deflate. Per altre informazioni, vedere la [documentazione di Node.js][API zlib]. 

### <a name="modules-helper-functions"></a>Procedura: Sfruttare i moduli

Servizi mobili espone un set di moduli che gli script possono caricare usando la funzione **require** globale. Ad esempio, uno script può richiedere **request** per l'esecuzione di richieste HTTP: 

	function update(item, user, request) { 
	    var httpRequest = require('request'); 
	    httpRequest('http://www.google.com', function(err, response, body) { 
	    	... 
	    }); 
	} 


### <a name="shared-code-source-control"></a>Procedura: Condividere il codice usando il controllo del codice sorgente

È possibile usare il controllo del codice sorgente con il gestore di pacchetti di Node.js (npm) per controllare quali moduli sono disponibili per il servizio mobile. A questo scopo è possibile procedere in due modi:

+ Per i moduli che vengono pubblicati in nmp e installati da npm, usare il file package.json per dichiarare i pacchetti che devono essere installati dal servizio mobile. In questo modo, il servizio ha sempre accesso alla versione più recente dei pacchetti necessari. Il file package.json si trova nella directory `.\service`. Per altre informazioni, vedere la pagina relativa al [supporto di package.json in Servizi mobili di Azure].

+ Per i moduli personalizzati o privati, è possibile usare npm per installare manualmente il modulo nella directory `.\service\node_modules` del controllo del codice sorgente. Per un esempio relativo al caricamento manuale di un modulo, vedere [Sfruttare il codice condiviso e i moduli Node.js negli script del server][Usare codice condiviso e moduli Node.js negli script del server].

	>[AZURE.NOTE]Quando `node_modules` esiste già nella gerarchia di directory, NPM vi creerà la sottodirectory `\node-uuid` invece di creare una nuova directory `node_modules` nel repository. In questo caso è sufficiente eliminare la directory `node_modules` esistente.

Dopo avere eseguito il commit del file package.json o dei moduli personalizzati nell'archivio per il servizio mobile, usare **require** per fare riferimento ai moduli in base al nome.   

>[AZURE.NOTE] I moduli specificati in package.json o caricati nel servizio mobile vengono usati solo nel proprio codice di script del server. Questi moduli non vengono usati dal runtime di Servizi mobili.

### <a name="helper-functions"></a>Procedura: Usare le funzioni di supporto

Oltre a richiedere moduli, i singoli script del server possono includere funzioni di supporto. Si tratta di funzioni separate rispetto alla funzione principale, che possono essere usate per includere codice nello script. 

Nel seguente esempio viene registrato uno script tabella nell'operazione di inserimento, che include la funzione di supporto **handleUnapprovedItem**:


	function insert(item, user, request) {
	    if (!item.approved) {
	        handleUnapprovedItem(item, user, request);
	    } else {
	        request.execute();
	    }
	}
	
	function handleUnapprovedItem(item, user, request) {
	    // Do something with the supplied item, user, or request objects.
	}
 
In uno script le funzioni di supporto devono essere dichiarate dopo la funzione principale. Nello script è necessario dichiarare tutte le variabili. Le variabili non dichiarate causano un errore.

Le funzioni di supporto possono anche essere definite una volta e condivise tra gli script del server. Per condividere una funzione tra gli script, è necessario che le funzioni vengano esportate e che il file di script sia presente nella directory `.\service\shared\`. Di seguito è riportato un modello per l'esportazione di una funzione condivisa in un file `.\services\shared\helpers.js`:

		exports.handleUnapprovedItem = function (tables, user, callback) {
		    
		    // Do something with the supplied tables or user objects and 
			// return a value to the callback function.
		};
 
È quindi possibile usare una funzione analoga alla seguente in uno script di operazione su tabella:

		function insert(item, user, request) {
		    var helper = require('../shared/helper');
		    helper.handleUnapprovedItem(tables, user, function(result) {
		        	
					// Do something based on the result.
		            request.execute();
		        }
		    }
		}

In questo esempio è necessario passare sia un [oggetto tables] che un [oggetto user] alla funzione condivisa. Questo è dovuto al fatto che gli script condivisi non possono accedere all'[oggetto tables] globale e l'[oggetto user] esiste solo nel contesto di una richiesta.

I file di script vengono caricati nella directory condivisa usando il [controllo del codice sorgente][Procedura: Condividere il codice usando il controllo del codice sorgente] oppure lo [strumento da riga di comando][Uso dello strumento da riga di comando].

### <a name="app-settings"></a>Procedura: Usare le impostazioni app

Servizi mobili consente di archiviare in modo sicuro valori come le impostazioni app, a cui è possibile accedere mediante gli script del server in fase di esecuzione.  Quando si aggiungono dati alle impostazioni app del servizio mobile, le coppie nome/valore vengono archiviate crittografate e sono accessibili negli script del server senza impostarle come hardcoded nel file di script. Per altre informazioni, vedere [Impostazioni app].

Nell'esempio di API personalizzata riportato di seguito viene usato l'[oggetto service] fornito per recuperare un valore di impostazione app.  

		exports.get = function(request, response) {
		
			// Get the MY_CUSTOM_SETTING value from app settings.
		    var customSetting = 
		        request.service.config.appSettings.my_custom_setting;
				
			// Do something and then send a response.

		}

Nel seguente codice viene usato il modulo di configurazione per recuperare i valori dei token di accesso Twitter, archiviati nelle impostazioni app, che vengono usati in uno script di processo pianificato:

		// Get the service configuration module.
		var config = require('mobileservice-config');

		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

Si noti che questo codice recupera anche i valori delle chiavi consumer di Twitter archiviate nella scheda **Identità** del portale. Dato che un **oggetto config** non è disponibile negli script di operazioni su tabella e processi pianificati, è necessario richiedere che il modulo di configurazione acceda alle impostazioni app. Per un esempio completo, vedere [Pianificare processi back-end in Servizi mobili].

<h2><a name="command-prompt"></a>Uso dello strumento da riga di comando</h2>

In Servizi mobili è possibile creare, modificare ed eliminare script del server usando lo strumento da riga di comando di Azure. Prima di caricare gli script, assicurarsi che sia in uso la seguente struttura di directory:

![4][4]

Si noti che questa struttura di directory è uguale a quella del repository Git quando si usa il controllo del codice sorgente. 

Quando si caricano file di script dallo strumento da riga di comando, è necessario prima passare alla directory `.\services\`. Il seguente comando carica uno script denominato `todoitem.insert.js` dalla sottodirectory `table`:

		~$azure mobile script upload todolist table/todoitem.insert.js
		info:    Executing command mobile script upload
		info:    mobile script upload command OK

Il seguente comando restituisce informazioni su ogni file di script mantenuto nel servizio mobile:

		~$ azure mobile script list todolist
		info:    Executing command mobile script list
		+ Retrieving script information
		info:    Table scripts
		data:    Name                       Size
		data:    -------------------------  ----
		data:    table/channels.insert      1980
		data:    table/TodoItem.insert      5504
		data:    table/TodoItem.read        64
		info:    Shared scripts
		data:    Name              Size
		data:    ----------------  ----
		data:    shared/helper.js  62
		data:    shared/uuid.js    7452
		info:    Scheduled job scripts
		data:    Job name    Script name           Status    Interval     Last run  Next run
		data:    ----------  --------------------  --------  -----------  --------  --------
		data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
		info:    Custom API scripts
		data:    Name                    Get          Put          Post         Patch        Delete
		data:    ----------------------  -----------  -----------  -----------  -----------  -----------
		data:    completeall             application  application  application  application  application
		data:    register_notifications  application  application  user         application  application
		info:    mobile script list command OK

Per altre informazioni, vedere la descrizione dei [comandi per gestire Servizi mobili di Azure][Comandi per la gestione di Servizi mobili di Azure]. 

## <a name="working-with-tables"></a>Uso delle tabelle

Questa sezione descrive le strategie per l'uso diretto dei dati delle tabelle nel database SQL e include le seguenti sezioni:

+ [Informazioni generali sull'uso delle tabelle](#overview-tables)
+ [Procedura: Accedere alle tabelle dagli script]
+ [Procedura: Eseguire inserimenti bulk]
+ [Procedura: Mappare tipi JSON a tipi di database]
+ [Uso di Transact-SQL per accedere alle tabelle]

### <a name="overview-tables"></a>Informazioni generali sull'uso delle tabelle

Molti scenari in Servizi mobili richiedono script del server per accedere alle tabelle nel database. Ad esempio, dato che in Servizi mobili non viene conservato lo stato tra le esecuzioni di script, i dati che devono essere mantenuti tra le esecuzioni di script devono essere archiviati in tabelle. Può anche essere necessario esaminare le voci in una tabella di autorizzazioni o archiviare dati di controllo anziché scrivere semplicemente nel log, dove i dati hanno una durata limitata e non sono accessibili a livello di codice. 

Servizi mobili prevede due modi per accedere alle tabelle, ovvero usando un proxy di [oggetto table] oppure componendo query Transact-SQL mediante l'[oggetto mssql]. L'[oggetto table] semplifica l'accesso ai dati delle tabelle dal codice di script del server, ma l'[oggetto mssql] supporta operazioni più complesse sui dati e offre una maggiore flessibilità. 

### <a name="access-tables"></a>Procedura: Accedere alle tabelle dagli script

Il modo più semplice per accedere alle tabelle da script consiste nell'uso dell'[oggetto tables]. La funzione **getTable** restituisce un'istanza dell'[oggetto table] che è un proxy per accedere alla tabella richiesta. È quindi possibile chiamare funzioni sul proxy per accedere ai dati e modificarli. 

Gli script registrati in operazioni su tabelle e processi pianificati possono accedere all'[oggetto tables] come oggetto globale. Questa riga di codice ottiene un proxy per la tabella *TodoItems* dall'[oggetto tables] globale: 

		var todoItemsTable = tables.getTable('TodoItems');

Gli script delle API personalizzate possono accedere all'[oggetto tables] dalla proprietà <strong>service</strong> dell'[oggetto request] fornito. Questa riga di codice ottiene l'[oggetto tables] dalla richiesta:

		var todoItemsTable = request.service.tables.getTable('TodoItem');

> [AZURE.NOTE] Le funzioni condivise non possono accedere direttamente all'**oggetto tables**. In una funzione condivisa è necessario passare l'oggetto tables alla funzione.

Quando è disponibile un [oggetto table], è possibile chiamare una o più funzioni di operazioni su tabella: In questo esempio vengono lette le autorizzazioni utente da una tabella di autorizzazioni:

	function insert(item, user, request) {
		var permissionsTable = tables.getTable('permissions');
	
		permissionsTable
			.where({ userId: user.userId, permission: 'submit order'})
			.read({ success: checkPermissions });
			
		function checkPermissions(results) {
			if(results.length > 0) {
				// Permission record was found. Continue normal execution.
				request.execute();
			} else {
				console.log('User %s attempted to submit an order without permissions.', user.userId);
				request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
			}
		}
	}

Nel seguente esempio vengono scritte informazioni di controllo in una tabella **audit**:

	function update(item, user, request) {
		request.execute({ success: insertAuditEntry });
		
		function insertAuditEntry() {
			var auditTable = tables.getTable('audit');
			var audit = {
				record: 'checkins',
				recordId: item.id,
				timestamp: new Date(),
				values: JSON.stringify(item)
			};
			auditTable.insert(audit, {
				success: function() {
					// Write to the response now that all data operations are complete
					request.respond();
				}
			});
		}
	}

Un ultimo esempio è disponibile nel codice di esempio riportato in [Procedura: Accedere ai parametri personalizzati][Procedura: Aggiungere parametri personalizzati].

### <a name="bulk-inserts"></a>Procedura: Eseguire inserimenti bulk

Se si usa un loop **for** o **while** per inserire direttamente un numero elevato di elementi, ad esempio 1000, in una tabella, è possibile che si raggiunga un limite di connessioni SQL che impedisce alcuni inserimenti. È possibile che la richiesta non venga mai completata o che restituisca un errore HTTP 500 - Errore interno del server.  Per evitare questo problema, è possibile inserire gli elementi in batch da circa 10. Al termine dell'inserimento del primo batch, inviare il successivo e così via.

Il seguente script consente di impostare la dimensione di un batch di record da inserire in parallelo. È consigliabile contenere il numero di record. La funzione **insertItems** chiama se stessa in modo ricorsivo al termine di un batch di inserimento asincrono. Il loop for alla fine inserisce un record alla volta e chiama **insertComplete** in caso di esito positivo e **errorHandler** in caso di errore. **insertComplete** controlla se verrà chiamata ricorsivamente la funzione **insertItems** per il batch successivo o se il processo è concluso e lo script deve terminare.

		var todoTable = tables.getTable('TodoItem');
		var recordsToInsert = 1000;
		var batchSize = 10; 
		var totalCount = 0;
		var errorCount = 0; 
		
		function insertItems() {        
		    var batchCompletedCount = 0;  
		
		    var insertComplete = function() { 
		        batchCompletedCount++; 
		        totalCount++; 
		        if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
		            if(totalCount < recordsToInsert) {
		                // kick off the next batch 
		                insertItems(); 
		            } else { 
		                // or we are done, report the status of the job 
		                // to the log and don't do any more processing 
		                console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
		            } 
		        } 
		    }; 
		
		    var errorHandler = function(err) { 
		        errorCount++; 
		        console.warn("Ignoring insert failure as part of batch.", err); 
		        insertComplete(); 
		    };
		
		    for(var i = 0; i < batchSize; i++) { 
		        var item = { text: "This is item number: " + totalCount + i }; 
		        todoTable.insert(item, { 
		            success: insertComplete, 
		            error: errorHandler 
		        }); 
		    } 
		} 
		
		insertItems(); 


L'intero esempio di codice, con la discussione associata, è disponibile in questo [post di blog](http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx). Se si usa questo codice, è possibile adattarlo alla propria situazione specifica e testarlo accuratamente.

### <a name="JSON-types"></a>Procedura: Mappare tipi JSON a tipi di database

Le raccolte di tipi di dati nel client e nella tabella di database di Servizi mobili sono diverse. Talvolta il mapping tra di esse è semplice mentre in altri casi non è così. Servizi mobili esegue numerose trasformazioni di tipi nel mapping:

- I tipi specifici del linguaggio del client vengono serializzati in JSON.
- La rappresentazione JSON viene tradotta in JavaScript prima di comparire negli script del server.
- I tipi di dati JavaScript vengono convertiti in tipi di database SQL quando vengono salvati usando l'[oggetto tables].

La trasformazione da schema client a JSON varia nelle diverse piattaforme.  JSON.NET è usato in Windows Store e nei client Windows Phone. Il client Android usa la libreria gson.  Il client iOS usa la classe NSJSONSerialization. Viene usato il comportamento di serializzazione predefinito di ognuna di queste librerie, tranne per il fatto che gli oggetti date vengono convertiti in stringhe JSON che contengono la data codificata usando ISO 8601.

Quando si scrivono script del server che usano le funzioni [insert], [update], [read] o [delete], è possibile accedere alla rappresentazione JavaScript dei dati. Servizi mobili usa [JSON.parse](http://es5.github.io/#x15.12), la funzione di deserializzazione di Node.js, per trasformare le stringhe JSON in oggetti JavaScript. Servizi mobili esegue tuttavia una trasformazione per estrarre gli oggetti **Date** dalle stringhe ISO 8601.

Quando si usa l'[oggetto tables] o l'[oggetto mssql] o si consente semplicemente l'esecuzione degli script tabella, gli oggetti JavaScript deserializzati vengono inseriti nel database SQL. In tale processo, le proprietà dell'oggetto vengono mappate ai tipi T-SQL:

<table border="1">
<tr>
<td>Proprietà JavaScript</td>
<td>Tipo T-SQL</td>
</tr><tr>
<td>Number</td>
<td>Float(53)</td>
</tr><tr>
<td>Boolean</td>
<td>Bit</td>
</tr><tr>
<td>Date</td>
<td>DateTimeOffset(3)</td>
</tr>
<tr>
<td>String</td>
<td>Nvarchar(max)</td>
</tr>
<tr>
<td>Buffer</td>
<td>Non supportato</td>
</tr><tr>
<td>Object</td>
<td>Non supportato</td>
</tr><tr>
<td>Array</td>
<td>Non supportato</td>
</tr><tr>
<td>Stream</td>
<td>Non supportato</td>
</tr>
</table> 

### <a name="TSQL"></a>Uso di Transact-SQL per accedere alle tabelle

Il modo più semplice per usare i dati di tabella dagli script del server consiste nell'uso di un proxy dell'[oggetto table]. Alcuni scenari più avanzati, tuttavia, non sono supportati dall'[oggetto table], ad esempio le query di tipo join e altre query complesse e il richiamo di stored procedure. In questi casi è necessario eseguire le istruzioni Transact-SQL direttamente sulla tabella relazionale usando l'[oggetto mssql]. Questo oggetto fornisce le seguenti funzioni:

- **query**: esegue una query, specificata da una stringa TSQL e i risultati vengono restituiti al callback **success** sull'oggetto **options**. La query può includere parametri se è presente il parametro *params*.
- **queryRaw**: like *query* tranne per il fatto che il set di risultati restituito dalla query è in formato "non elaborato" (vedere l'esempio riportato di seguito).
- **open**: permette di ottenere una connessione al database di Servizi mobili. L'oggetto connection può quindi essere usato per richiamare operazioni di database come le transazioni.

Questi metodi offrono un controllo di livello progressivamente più basso sull'elaborazione della query.

+ [Procedura: Eseguire una query statica]
+ [Procedura: Eseguire una query dinamica]
+ [Procedura: Creare un join tra tabelle relazionali]
+ [Procedura: Eseguire una query che restituisce risultati *raw*]
+ [Procedura: Ottenere l'accesso a una connessione di database]	

#### <a name="static-query"></a>Procedura: Eseguire una query statica

La seguente query non include parametri e restituisce tre record dalla tabella `statusupdate`. Il set di righe è in formato JSON standard.

		mssql.query('select top 3 * from statusupdates', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});


#### <a name="dynamic-query"></a>Procedura: Eseguire una query con parametri dinamici

Nel seguente esempio viene implementata l'autorizzazione personalizzata leggendo le autorizzazioni per ogni utente dalla tabella corrispondente. Il segnaposto (?) viene sostituito dal parametro fornito quando viene eseguita la query.

		    var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
		    mssql.query(sql, [user.userId], {
		        success: function(results) {
		            if (results.length > 0) {
		                // Permission record was found. Continue normal execution. 
		                request.execute();
		            } else {
		                console.log('User %s attempted to submit an order without permissions.', user.userId);
		                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
		            }
		        },
            	error: function(err) {
                	console.log("error is: " + err);
				}	
		    });


#### <a name="joins"></a>Procedura: Creare un join tra tabelle relazionali

È possibile creare un join tra due tabelle usando il metodo **query** dell'[oggetto mssql] per passare il codice TSQL che implementa il join. Si supponga che nella tabella **ToDoItem** siano presenti alcuni elementi e che ognuno di essi contenga una proprietà **priority**, che corrisponde a una colonna nella tabella. Un elemento può essere analogo al seguente:

		{ text: 'Take out the trash', complete: false, priority: 1}

Si supponga inoltre di disporre di una tabella aggiuntiva denominata **Priority** con righe contenenti un numero di priorità (**number**) e una descrizione testuale (**description**). Ad esempio, al numero di priorità 1 può essere associata la descrizione "Critical" e l'oggetto può risultare come segue:

		{ number: 1, description: 'Critical'}

A questo punto è possibile sostituire il numero relativo a **priority** nell'elemento con la descrizione testuale del numero di priorità. Questa operazione viene eseguita con un join relazionale delle due tabelle.

		mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
			success: function(results) {
				console.log(results);
			},
            error: function(err) {
                console.log("error is: " + err);
		});
	
Lo script crea il join tra le due tabelle e scrive i risultati nel log. Gli oggetti risultanti possono essere analoghi a quanto segue:

		{ text: 'Take out the trash', complete: false, description: 'Critical'}


#### <a name="raw"></a>Procedura: Eseguire una query che restituisce risultati *raw*

In questo esempio viene eseguita la query, come in precedenza, ma il set di risultati viene restituito in formato "non elaborato", che richiede la successiva analisi, riga per riga e colonna per colonna. Uno scenario possibile è rappresentato dal caso in cui sia necessario accedere a tipi di dati non supportati da Servizi mobili. Questo codice scrive semplicemente l'output nel log della console affinché sia possibile esaminare il formato non elaborato.

		mssql.queryRaw('SELECT * FROM ToDoItem', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});

Di seguito è riportato l'output ottenuto eseguendo la query. Contiene i metadati relativi a ogni colonna della tabella, seguiti da una rappresentazione delle righe e delle colonne.

		{ meta: 
		   [ { name: 'id',
		       size: 19,
		       nullable: false,
		       type: 'number',
		       sqlType: 'bigint identity' },
		     { name: 'text',
		       size: 0,
		       nullable: true,
		       type: 'text',
		       sqlType: 'nvarchar' },
		     { name: 'complete',
		       size: 1,
		       nullable: true,
		       type: 'boolean',
		       sqlType: 'bit' },
		     { name: 'priority',
		       size: 53,
		       nullable: true,
		       type: 'number',
		       sqlType: 'float' } ],
		  rows: 
		   [ [ 1, 'good idea for the future', null, 3 ],
		     [ 2, 'this is important but not so much', null, 2 ],
		     [ 3, 'fix this bug now', null, 0 ],
		     [ 4, 'we need to fix this one real soon now', null, 1 ],
		   ] }

#### <a name="connection"></a>Procedura: Ottenere l'accesso a una connessione di database

È possibile usare il metodo **open** per ottenere l'accesso alla connessione di database. Questo può essere necessario, ad esempio, per usare le transazioni di database.

Se il metodo **open** viene eseguito con esito positivo, la connessione di database viene passata coma parametro nella funzione **success**. È possibile richiamare una qualsiasi delle seguenti funzioni sull'oggetto **connection**: *close*, *queryRaw*, *query*, *beginTransaction*, *commit* e *rollback*.

		    mssql.open({
		        success: function(connection) {
		            connection.query(//query to execute);
		        },
	            error: function(err) {
	                console.log("error is: " + err);
				}
		    });

## <a name="debugging"></a>Debug e risoluzione dei problemi

Il modo principale per eseguire il debug e risolvere i problemi relativi agli script del server consiste nella scrittura nel log del servizio. Per impostazione predefinita, gli errori che si verificano durante l'esecuzione degli script di servizio vengono scritti nei log del servizio da Servizi mobili. Anche gli script personalizzati possono scrivere nei log. La scrittura nei log è un ottimo modo per eseguire il debug degli script e assicurarsi che il loro comportamento corrisponda alle aspettative.

### <a name="write-to-logs"></a>Procedura: Scrivere l'output nei log del servizio mobile

Per scrivere nei log, usare l'[oggetto console] globale. Usare la funzione **log** o **info** per registrare avvisi a livello di informazioni. Le funzioni **warning** e **error** registrano i rispettivi livelli, che sono descritti nei log. 

> [AZURE.NOTE] Per visualizzare i log per il proprio servizio mobile, accedere al [portale di gestione](https://manage.windowsazure.com/), selezionare il servizio mobile, quindi scegliere la scheda **Log**.

È anche possibile usare la funzione di registrazione dell'[oggetto console] per formattare i messaggi mediante parametri. Nel seguente esempio un oggetto JSON viene fornito come parametro alla stringa del messaggio:

	function insert(item, user, request) {
	    console.log("Inserting item '%j' for user '%j'.", item, user);  
	    request.execute();
	}

Si noti che la stringa `%j` viene usata come segnaposto per un oggetto JSON e che i parametri vengono forniti in ordine sequenziale. 

Per evitare di sovraccaricare il log, è consigliabile rimuovere o disabilitare le chiamate a console.log() che non sono necessarie per l'uso in produzione.

<!-- Anchors. -->
[Introduzione]: #intro
[Operazioni su tabella]: #table-scripts
[operazioni su tabella di base]: #basic-table-ops
[Procedura: Eseguire la registrazione per le operazioni su tabelle]: #register-table-scripts
[Procedura: Definire script di tabella]: #execute-operation
[Procedura: Eseguire l'override della risposta predefinita]: #override-response
[Procedura: Modificare un'operazione]: #modify-operation
[Procedura: Eseguire l'override di esito positivo e negativo]: #override-success-error
[Procedura: Eseguire l'override della riuscita di execute]: #override-success
[Procedura: Eseguire l'override della gestione degli errori predefinita]: #override-error
[Procedura: Accedere alle tabelle dagli script]: #access-tables
[Procedura: Aggiungere parametri personalizzati]: #access-headers
[Procedura: Usare gli utenti]: #work-with-users
[Procedura: Definire script di processi pianificati]: #scheduler-scripts
[Procedura: Ridefinire l'accesso alle tabelle]: #authorize-tables
[Uso di Transact-SQL per accedere alle tabelle]: #TSQL
[Procedura: Eseguire una query statica]: #static-query
[Procedura: Eseguire una query dinamica]: #dynamic-query
[Procedura: Eseguire una query che restituisce risultati *raw*]: #raw
[Procedura: Ottenere l'accesso a una connessione di database]: #connection
[Procedura: Creare un join tra tabelle relazionali]: #joins
[Procedura: Eseguire inserimenti bulk]: #bulk-inserts
[Procedura: Mappare tipi JSON a tipi di database]: #JSON-types
[Procedura: Caricare moduli Node.js]: #modules-helper-functions
[Procedura: Scrivere l'output nei log del servizio mobile]: #write-to-logs
[Controllo del codice sorgente, codice condiviso e funzioni di supporto]: #shared-code
[Uso dello strumento da riga di comando]: #command-prompt
[Uso delle tabelle]: #working-with-tables
[Ancoraggio di API personalizzate]: #custom-api
[Procedura: Definire un'API personalizzata]: #define-custom-api
[Procedura: Condividere il codice usando il controllo del codice sorgente]: #shared-code-source-control
[Procedura: Usare le funzioni di supporto]: #helper-functions
[Debug e risoluzione dei problemi]: #debugging
[Procedura: Implementare metodi HTTP]: #handle-methods
[Procedura: Usare utenti e intestazioni in un'API personalizzata]: #get-api-user
[Procedura: Accedere alle intestazioni di richiesta delle API personalizzate]: #get-api-headers
[Utilità di pianificazione processi]: #scheduler-scripts
[Procedura: Definire più route in un'API personalizzata]: #api-routes
[Procedura: Inviare e ricevere dati come XML]: #api-return-xml
[Procedura: Usare le impostazioni app]: #app-settings

[1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
[2]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
[3]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
[4]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://msdn.microsoft.com/library/windowsazure/jj554226.aspx
[Pianificare processi back-end in Servizi mobili]: /develop/mobile/tutorials/schedule-backend-tasks/
[oggetto request]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[oggetto response]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[oggetto user]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[oggetto push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[funzione insert]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[insert]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[funzione update]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[funzione delete]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[funzione read]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[update]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[oggetto query]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[oggetto apns]: http://msdn.microsoft.com/library/windowsazure/jj839711.aspx
[oggetto mpns]: http://msdn.microsoft.com/library/windowsazure/jj871025.aspx
[oggetto wns]: http://msdn.microsoft.com/library/windowsazure/jj860484.aspx
[oggetto table]: http://msdn.microsoft.com/library/windowsazure/jj554210.aspx
[oggetto tables]: http://msdn.microsoft.com/library/windowsazure/jj614364.aspx
[oggetto mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx
[oggetto console]: http://msdn.microsoft.com/library/windowsazure/jj554209.aspx
[Leggere e scrivere dati]: http://msdn.microsoft.com/library/windowsazure/jj631640.aspx
[Convalidare i dati]: http://msdn.microsoft.com/library/windowsazure/jj631638.aspx
[Modificare la richiesta]: http://msdn.microsoft.com/library/windowsazure/jj631635.aspx
[Modificare la risposta]: http://msdn.microsoft.com/library/windowsazure/jj631631.aspx
[portale di gestione]: https://manage.windowsazure.com/
[Pianificare i processi]: http://msdn.microsoft.com/library/windowsazure/jj860528.aspx
[Convalidare e modificare i dati in Servizi mobili mediante script del server]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
[Comandi per la gestione di Servizi mobili di Azure]: /documentation/articles/virtual-machines-command-line-tools/#Mobile_Scripts
[Push di Windows Store]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Push di Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Push di iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Push di Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Azure SDK per Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[Inviare una richiesta HTTP]: http://msdn.microsoft.com/library/windowsazure/jj631641.aspx
[Inviare e-mail da Servizi mobili con SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Introduzione all'autenticazione]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[API crypto]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[API path]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[API querystring]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[API url]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[API util]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[API zlib]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[API personalizzata]: http://msdn.microsoft.com/library/windowsazure/dn280974.aspx
[Chiamare un'API personalizzata dal client]: /develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
[libreria express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[Definire un'API personalizzata che supporta le notifiche periodiche]: /develop/mobile/tutorials/create-pull-notifications-dotnet/
[oggetto express in express.js]: http://expressjs.com/api.html#express
[Archiviare script del server nel controllo del codice sorgente]: /develop/mobile/tutorials/store-scripts-in-source-control/
[Usare codice condiviso e moduli Node.js negli script del server]: /develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
[oggetto service]: http://msdn.microsoft.com/library/windowsazure/dn303371.aspx
[Impostazioni app]: http://msdn.microsoft.com/library/dn529070.aspx
[modulo di configurazione]: http://msdn.microsoft.com/library/dn508125.aspx
[Supporto di package.json in Servizi mobili di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=391036

<!--HONumber=47-->
