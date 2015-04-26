<properties 
	pageTitle="Come usare un client HTML - Servizi mobili di Azure" 
	description="Informazioni sull'uso di un client HTML per Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>


# Come usare un client HTML/JavaScript per Servizi mobili di Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/it-it/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/it-it/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/it-it/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/it-it/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


In questa guida viene illustrato come eseguire scenari comuni usando un client HTML/JavaScript per Servizi mobili di Azure. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Per i nuovi utenti di Servizi mobili si consiglia di completare prima il progetto di [guida introduttiva JavaScript per Windows Store] o [guida introduttiva HTML] per Servizi mobili. L'esercitazione relativa alla guida introduttiva è utile per configurare il proprio account e creare il primo servizio mobile.


## Sommario

- [Informazioni su Servizi mobili]
- [Concetti]
- [Procedura: Creare il client di Servizi mobili]
- [Procedura: Eseguire query sui dati da un servizio mobile]
	- [Filtrare i dati restituiti]
    - [Ordinare i dati restituiti]
	- [Restituire i dati in pagine]
	- [Selezionare colonne specifiche]
	- [Cercare dati in base all'ID]
	- [Eseguire un'operazione di query OData]
- [Procedura: Inserire dati in un servizio mobile]
- [Procedura: Modificare dati in un servizio mobile]
- [Procedura: Eliminare dati in un servizio mobile]
- [Procedura: Visualizzare i dati nell'interfaccia utente]
- [Procedura: Autenticare gli utenti]
- [Procedura: Gestire gli errori]
- [Procedura: Usare le promesse]
- [Procedura: Personalizzare le intestazioni di richieste]
- [Procedura: Usare la condivisione di risorse tra origini]
- [Passaggi successivi]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Procedura: Creare il client di Servizi mobili



Aprire il file HTML nell'editor Web e aggiungere il codice seguente ai riferimenti allo script per la pagina:

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

> [AZURE.NOTE] Per un'app di Windows Store scritta in JavaScript/HTML, è sufficiente aggiungere il pacchetto **WindowsAzure.MobileServices.WinJS** al progetto.

Nell'editor aprire o creare un file JavaScript e aggiungere il codice seguente per definire la variabile `MobileServiceClient`, infine specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore `MobileServiceClient`, senza modificare l'ordine.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

È necessario sostituire il segnaposto `AppUrl` con l'URL dell'applicazione del servizio mobile e `AppKey` con la chiave dell'applicazione. Per informazioni su come recuperare l'URL e la chiave dell'applicazione del servizio mobile, vedere l'esercitazione [Introduzione ai dati in JavaScript per Windows Store] o [Introduzione ai dati in HTML/JavaScript].

##<a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile

Tutto il codice che accede o modifica i dati nella tabella del database SQL chiama funzioni sull'oggetto `MobileServiceTable`. Per ottenere un riferimento alla tabella, chiamare la funzione `getTable()` su un'istanza dell'oggetto `MobileServiceClient`.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente illustra come filtrare i dati includendo una clausola `where` in una query. Il codice restituisce tutti gli elementi di `todoItemTable` il cui campo completo è uguale a `false`. `todoItemTable` è il riferimento alla tabella di Servizi mobili creata in precedenza. La funzione where applica un predicato di filtro di riga alla query sulla tabella. Accetta come argomento un oggetto o una funzione JSON che definisce il filtro di riga e restituisce una query che può essere composta ulteriormente.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Aggiungendo una chiamata alla funzione `where` sull'oggetto Query e passando un oggetto come parametro, si specifica che Servizi mobili dovrà restituire solo le righe la cui colonna `complete` contiene il valore `false`. Nell'URI della richiesta riportato di seguito si noterà inoltre che viene modificata la stringa di query stessa:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

È possibile visualizzare l'URI della richiesta inviata al servizio mobile usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o Fiddler.

Normalmente, questa richiesta verrebbe convertita approssimativamente nella query SQL seguente sul lato server:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

L'oggetto passato al metodo `where` può avere un numero arbitrario di parametri. Tutti verranno interpretati come clausole AND per la query. Ad esempio, la riga seguente:

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Viene convertita approssimativamente (per la stessa richiesta) in

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

L'istruzione `where` e la query SQL sopra riportate trovano gli elementi incompleti assegnati a "david" con livello di difficoltà "medium".

Esiste tuttavia un altro modo per scrivere la stessa query. Una chiamata a `.where` nell'oggetto Query aggiunge un'espressione `AND` alla clausola `WHERE`, pertanto la query può anche essere scritta in tre righe:

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

Oppure tramite l'API Fluent:

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

I due metodi si equivalgono e possono essere usati in modo intercambiabile. Tutte le chiamate a `where` descritte finora usano un oggetto con alcuni parametri e vengono confrontate con i dati del database per trovare uguaglianze. Esiste tuttavia un altro overload per il metodo della query, che usa una funzione anziché l'oggetto. In questa funzione è quindi possibile scrivere espressioni più complesse, usando operatori come quello di disuguaglianza e altre operazioni relazionali. In queste funzioni la parola chiave `this` esegue l'associazione all'oggetto server.

Il corpo della funzione viene convertito in un'espressione booleana OData (Open Data Protocol), che viene passata a un parametro di stringa della query. È possibile passare una funzione senza parametri, come:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Se si passa una funzione con parametri, tutti gli argomenti dopo la clausola `where` vengono associati ai parametri della funzione, nell'ordine indicato. Tutti gli oggetti che provengono dall'esterno dell'ambito della funzione DEVONO essere passati come parametri, in quanto la funzione non può acquisire le variabili esterne. Nei due esempi seguenti, l'argomento "david" è associato al parametro `name` e nel primo esempio l'argomento "medium" è anche associato al parametro `level`. La funzione deve inoltre essere costituita da un'unica istruzione `return` con un'espressione supportata, come segue:

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Pertanto, sempre seguendo le regole, è possibile aggiungere filtri più complessi alle query del database, come illustrato di seguito:

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

È possibile combinare `where` con `orderBy`, `take` e `skip`. Per informazioni dettagliate, vedere la sezione che segue.

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice seguente illustra come ordinare i dati includendo una funzione `orderBy` o `orderByDescending` nella query. L'operazione restituisce elementi della tabella `todoItemTable` in ordine crescente in base al campo `text`. Per impostazione predefinita, il server restituisce solo i primi 50 elementi.

> [AZURE.NOTE] Per impostazione predefinita, viene usata una dimensione di pagina basata sul server, per evitare la restituzione di tutti gli elementi. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. 
È possibile aumentare il numero di elementi da restituire chiamando `take` come descritto nella sezione seguente. `todoItemTable` è il riferimento alla tabella di Servizi mobili creata in precedenza.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Procedura: Restituire i dati in pagine

Il codice seguente illustra come implementare il paging nei dati restituiti tramite le clausole `take` e `skip` nella query.  La query seguente, se eseguita, restituisce le prime tre voci della tabella.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Si noti che il metodo `take(3)` è stato convertito nell'opzione di query `$top=3` nell'URI della query.

La query modificata riportata di seguito ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile. Si noti che il metodo `skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

Si tratta di uno scenario in cui il passaggio di valori di paging hardcoded alle funzioni `take` e `skip` è stato semplificato. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola `select`. Ad esempio, il codice seguente restituisce le proprietà `id`, `complete` e `text` per ogni riga della tabella `todoItemTable`:

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

In questo caso, i parametri della funzione select sono i nomi delle colonne della tabella che si desidera restituire.


Tutte le funzioni descritte in precedenza sono additive, di conseguenza è possibile ripetere la chiamata per influire maggiormente sulla query. Un altro esempio:

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Procedura: Cercare dati in base all'ID

La funzione `lookup` usa solo il valore `id` e restituisce l'oggetto del database con tale ID. Le tabelle del database vengono create con una colonna `id` di tipo integer o stringa. L'impostazione predefinita è una colonna `id` di tipo stringa.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Eseguire un'operazione di query OData

Servizi mobili usa le convenzioni URI della query OData per la creazione e l'esecuzione di query REST.  Non tutte le query OData possono essere create mediante le funzioni di query integrate, in particolare le operazioni di filtro complesse come la ricerca di una sottostringa in una proprietà. Per query complesse di questo tipo, è possibile passare un'eventuale stringa di opzione di query OData valida alla funzione `read` nel modo seguente:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(ite
	ms.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Quando si specifica una stringa di opzione di query OData nella funzione `read`, non è possibile usare anche i metodi del generatore di query nella stessa query. In questo caso, è necessario creare l'intera query come stringa di query OData. Per altre informazioni sulle opzioni di query del sistema OData, vedere il [riferimento alle opzioni di query del sistema OData].

<h2><a name="inserting"></a>Procedura: Inserire dati in un servizio mobile</h2>

Il codice seguente illustra come inserire nuove righe in una tabella. Il client richiede che una riga di dati venga inserita inviando una richiesta POST al servizio mobile. Il corpo della richiesta contiene i dati da inserire come oggetto JSON.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

Questa operazione inserisce i dati dall'oggetto JSON specificato nella tabella. È inoltre possibile specificare una funzione di richiamata da richiamare al termine dell'inserimento:

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});


Servizi mobili supporta valori di stringa univoci personalizzati per l'ID della tabella. In tal modo alle applicazioni è consentito usare valori personalizzati come indirizzi di posta elettronica o nomi utente per la colonna ID di una tabella di Servizi mobili. Se ad esempio si vuole identificare ogni record con un indirizzo di posta elettronica, è possibile usare l'oggetto JSON seguente.

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

Se al momento dell'inserimento di nuovi record in una tabella non viene specificato un valore ID di stringa, Servizi mobili genera un valore univoco per l'ID.

Il supporto di ID di stringa comporta i seguenti vantaggi per gli sviluppatori

+ È possibile generare ID senza generare un round trip del database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Per impostare i valori di ID, è inoltre possibile usare script del server. L'esempio di script seguente genera un GUID personalizzato e lo assegna all'ID di un nuovo record. Si tratta di un valore di ID simile a quello che verrebbe generato da Servizi mobili se non venisse passato un valore per l'ID di un record.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Il valore di ID specificato da un'applicazione verrà archiviato da Servizi mobili così com'è. Sono inclusi spazi vuoti iniziali o finali. Lo spazio vuoto non verrà rimosso dal valore.

Il valore per `id` deve essere univoco e non deve includere caratteri appartenenti ai set seguenti:

+ Caratteri di controllo: [0x0000-0x001F] e [0x007F-0x009F]. Per altre informazioni, vedere la pagina relativa ai [codici di controllo ASCII C0 e C1].
+  Caratteri stampabili: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Gli ID "." e ".."

In alternativa è possibile usare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario usare il comando `mobile table create` e l'opzione `--integerId` per creare la tabella. Questo comando viene usato con l'interfaccia della riga di comando (CLI) per Azure. Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili].


<h2><a name="modifying"></a>Procedura: Modificare dati in un servizio mobile</h2>

Il codice seguente illustra come aggiornare i dati in una tabella. Il client richiede che una riga di dati venga aggiornata inviando una richiesta PATCH al servizio mobile. Il corpo della richiesta contiene i campi specifici da aggiornare come oggetto JSON. Viene aggiornato un elemento esistente nella tabella `todoItemTable`.

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

Il primo parametro specifica l'istanza da aggiornare nella tabella, come indicato dal relativo ID.

È inoltre possibile specificare una funzione di richiamata da richiamare al termine dell'aggiornamento:

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="deleting"></a>Procedura: Eliminare dati in un servizio mobile</h2>

Il codice seguente illustra come eliminare i dati da una tabella. Il client richiede che una riga di dati venga eliminata inviando una richiesta DELETE al servizio mobile. Viene eliminato un elemento esistente nella tabella todoItemTable.

			todoItemTable.del({
			   id: idToDelete
			})

Il primo parametro specifica l'istanza da eliminare nella tabella, come indicato dal relativo ID.

È inoltre possibile specificare una funzione di richiamata da richiamare al termine dell'eliminazione:

			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="binding"></a>Procedura: Visualizzare i dati nell'interfaccia utente</h2>

In questa sezione viene illustrato come visualizzare gli oggetti dati restituiti usando elementi dell'interfaccia utente. Per eseguire una query sugli elementi nella tabella `todoItemTable` e visualizzare i risultati in un elenco molto semplice, è possibile eseguire il codice di esempio seguente. Non vengono eseguite operazioni di selezione, filtro o ordinamento di alcun tipo.

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoIte
	ms.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfIte
	ms.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

In un'app di Windows Store, i risultati di una query possono essere usati per creare un oggetto [WinJS.Binding.List], che può essere associato come origine dati per un oggetto [ListView]. Per altre informazioni, vedere [Associazione dati (app di Windows Store con JavaScript e HTML)].

<h2><a name="caching"></a>Procedura: Autenticare gli utenti</h2>

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti delle app tramite diversi provider di identità esterni: Facebook, Google, account Microsoft e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile usare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione [Introduzione all'autenticazione].

Sono supportati due flussi di autenticazione, un flusso _server flow_ e un flusso _client flow_. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

<h3>Flusso server</h3>
Per consentire a Servizi mobili di gestire il processo di autenticazione nell'app di Windows Store o HTML5,
è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio mobile è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione "Introduzione all'autenticazione" ([Windows Store][Introduzione all'autenticazione - Windows Store]/[HTML][Introduzione all'autenticazione]).

Dopo aver effettuato la registrazione del provider di identità, è sufficiente chiamare il [metodo LoginAsync] con il valore [MobileServiceAuthenticationProvider] del provider. Per accedere ad esempio con Facebook, usare il codice seguente.

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo `login` riportato in precedenza con uno dei seguenti: `microsoftaccount`, `facebook`, `twitter`, `google` o `windowsazureactivedirectory`.

In questo caso, Servizi mobili gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione di Servizi mobili una volta eseguito correttamente l'accesso con il provider di identità. La funzione [login], quando è completa, restituisce un oggetto JSON (**user**) che espone l'ID utente e il token di autenticazione di Servizi mobili nei campi **userId** e **authenticationToken** rispettivamente. È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per altre informazioni, vedere [Memorizzazione nella cache del token di autenticazione].

> [AZURE.NOTE] **App di Windows Store**
Quando si usa il provider di accesso con account Microsoft per autenticare gli utenti dell'app di Windows Store, è inoltre necessario registrare il pacchetto dell'app con Servizi mobili. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Per informazioni su come registrare il pacchetto dell'app di Windows Store, vedere [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft](/it-it/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Dopo la registrazione delle informazioni del pacchetto con Servizi mobili, per riutilizzare le credenziali è necessario chiamare il metodo [login](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank") specificando il valore **true** per il parametro <em>useSingleSignOn</em>.

<h3>Flusso client</h3>
L'app può inoltre contattare il provider di identità in modo indipendente e fornire il token restituito a Servizi mobili per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

Nell'esempio seguente viene usato Live SDK, che supporta Single-Sign-On per le app di Windows Store tramite un account Microsoft:

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount",
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

Questo esempio semplificato ottiene un token da Live Connect, che viene passato a Servizi mobili chiamando la funzione [login]. Per un esempio più completo di uso di un account Microsoft per consentire l'uso di un unico accesso, vedere [Autenticare un'app con Single-Sign-On].

Se si usano le API di Facebook o Google per l'autenticazione client, l'esempio cambia leggermente.

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

In questo esempio si presuppone che il token fornito dall'SDK del rispettivo provider sia archiviato nella variabile `token`.
Non è al momento possibile usare Twitter per l'autenticazione client.

<h3>Memorizzazione nella cache del token di autenticazione</h3>
In alcuni casi, è possibile evitare la chiamata al metodo di accesso dopo la prima autenticazione dell'utente. È possibile usare [sessionStorage] o [localStorage] per memorizzare nella cache l'identità dell'utente corrente al primo accesso e a ogni controllo della presenza dell'identità dell'utente nella cache effettuato in seguito. Se la cache è vuota o le chiamate non vengono eseguite (in quanto la sessione di accesso corrente è scaduta), è comunque necessario ripetere la procedura di accesso.

        // After logging in
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Log in
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Regular login flow
       }

         // Log out	
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a>Procedura: Gestire gli errori</h2>

In Servizi mobili è possibile rilevare, convalidare e risolvere gli errori in diversi modi.

Ad esempio, gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. Si consideri ad esempio la definizione e la registrazione di uno script del server per la convalida e la modifica dei dati:

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

Questo script sul lato server convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso quelle più lunghe di 10 caratteri.

Ora che il servizio mobile convalida i dati e invia risposte di errore sul lato server, è possibile aggiornare l'app HTML per gestire le risposte di errore risultanti dalla convalida.

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


Per risolvere ulteriormente la situazione, passare il gestore errori come secondo argomento ogni volta che si esegue l'accesso ai dati:

			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>Procedura: Usare le promesse</h2>

Le promesse offrono un meccanismo per pianificare il lavoro da eseguire su un valore che non è ancora stato calcolato. Si tratta di un'astrazione per la gestione delle interazioni con le API asincrone.

La promessa `done` viene eseguita non appena la funzione specificata viene completata o riceve un errore. A differenza della promessa `then`, genera sicuramente gli errori non gestiti all'interno della funzione e, dopo che i gestori hanno terminato l'esecuzione, la funzione genera gli errori che sarebbero stati restituiti da then come promessa nello stato di errore. Per altre informazioni, vedere [done].

			promise.done(onComplete, onError);

In questo modo:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

La promessa `then` corrisponde alla promessa `done` ma, a differenza della promessa `then`, `done` garantisce la generazione di errori non gestiti all'interno della funzione. Se non si fornisce un gestore errori a `then` e l'operazione include un errore, non viene generata un'eccezione, ma viene restituita invece una promessa nello stato di errore. Per altre informazioni, vedere [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

In questo modo:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

È possibile usare le promesse in molti modi diversi. È possibile concatenare operazioni di promessa chiamando `then` o `done` sulla promessa restituita dalla funzione `then` precedente. Usare `then` per una fase intermedia dell'operazione, ad esempio `.then().then()`, e `done` per la fase finale dell'operazione, ad esempio `.then().then().done()`.  È possibile concatenare più funzioni `then`, in quanto `then` restituisce una promessa. Non è possibile concatenare più di un metodo `done`, in quanto tale metodo restituisce un valore indefinito. [Altre informazioni sulle differenze tra then e done].

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>Procedura: Personalizzare le intestazioni di richieste client</h2>

È possibile inviare intestazioni di richieste personalizzate tramite la funzione `withFilter`, leggendo e scrivendo le proprietà arbitrarie della richiesta in fase di invio nel filtro. Aggiungere tale intestazione HTTP personalizzata se è necessaria o consigliabile per uno script sul lato server.

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

I filtri sono usati per molte altre operazioni oltre alla personalizzazione delle intestazioni di richieste. Consentono di esaminare o modificare le richieste, esaminare o modificare le risposte, ignorare le chiamate di rete, inviare più chiamate e così via.

<h2><a name="hostnames"></a>Procedura: Usare la condivisione di risorse tra origini</h2>

Per controllare quali siti Web possono interagire con il servizio mobile e inviare richieste a quest'ultimo, assicurarsi di aggiungere il nome host del sito Web usato per ospitare il servizio nell'elenco di condivisione di risorse tra le origini (CORS) tramite la scheda Configure. È possibile usare i caratteri jolly, se necessario. Per impostazione predefinita, il nuovo servizio mobile indica al browser di consentire l'accesso solo da `localhost`, mentre CORS consente al codice JavaScript in esecuzione in un browser su un nome host esterno di interagire con il servizio mobile.  Questa configurazione non è necessaria per le applicazioni WinJS.

<h2><a name="nextsteps"></a>Passaggi successivi</h2>

Dopo aver completato questo argomento di riferimento per i concetti e le procedure, è possibile eseguire importanti attività in Servizi mobili:

* [Introduzione a Servizi mobili]
  <br/>Informazioni sulle nozioni di base per l'uso di Servizi mobili.

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

* [Autorizzare gli utenti con gli script]
  <br/>Informazioni sul valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato, che verrà usato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Informazioni su Servizi mobili]: #what-is
[Concetti]: #concepts
[Procedura: Creare il client di Servizi mobili]: #create-client
[Procedura: Eseguire query sui dati da un servizio mobile]: #querying
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[Cercare dati in base all'ID]: #lookingup
[Procedura: Visualizzare i dati nell'interfaccia utente]: #binding
[Procedura: Inserire dati in un servizio mobile]: #inserting
[Procedura: Modificare dati in un servizio mobile]: #modifying
[Procedura: Eliminare dati in un servizio mobile]: #deleting
[Procedura: Autenticare gli utenti]: #caching
[Procedura: Gestire gli errori]: #errors
[Procedura: Usare le promesse]: #promises
[Procedura: Personalizzare le intestazioni di richieste]: #customizing
[Procedura: Usare la condivisione di risorse tra origini]: #hostnames
[Passaggi successivi]: #nextsteps
[Eseguire un'operazione di query OData]: #odata-query



<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-html
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Introduzione ai dati]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-html/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html
[Introduzione all'autenticazione - Windows Store]: /it-it/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Altre informazioni sulle differenze tra then e done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[come gestire gli errori nelle promesse]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Associazione dati (app di Windows Store con JavaScript e HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[Guida introduttiva JavaScript per Windows Store]: http://azure.microsoft.com/develop/mobile/tutorials/get-started
[Guida introduttiva HTML]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-html
[Introduzione ai dati in JavaScript per Windows Store]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-js
[Introduzione ai dati in HTML/JavaScript]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-html/
[Un esempio completo di come configurare questo scenario è disponibile qui]: http://azure.microsoft.com/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-html
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/library/windowsazure/jj554236.aspx
[Autenticare un'app con Single-Sign-On]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[Codici di controllo ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI per la gestione delle tabelle di Servizi mobili]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Riferimento alle opzioni di query del sistema OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502



<!--HONumber=42-->
