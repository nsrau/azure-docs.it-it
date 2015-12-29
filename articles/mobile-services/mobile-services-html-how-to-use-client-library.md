<properties
	pageTitle="Come usare un client HTML con i Servizi mobili di Azure | Microsoft Azure"
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
	ms.date="10/23/2015"
	ms.author="glenga"/>

# Come usare un client HTML/JavaScript per Servizi mobili di Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Panoramica

Questa guida illustra come eseguire scenari comuni usando un client HTML/JavaScript per Servizi mobili di Azure, inclusi Windows Store JavaScript e app PhoneGap/Cordova. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Se non si ha familiarità con Servizi mobili, si consiglia di completare prima il progetto di [guida introduttiva per Servizi mobili](mobile-services-html-get-started.md). L'esercitazione relativa alla guida introduttiva è utile per configurare il proprio account e creare il primo servizio mobile.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Procedura: Creare il client di Servizi mobili

La procedura per aggiungere un riferimento al client di Servizi mobili dipende dalla piattaforma per app usata, come illustrato di seguito:

- Per un'app basata su Web, aprire il file HTML e aggiungere il codice seguente ai riferimenti allo script per la pagina:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js"></script>

- Per un'app di Windows Store scritta in JavaScript/HTML, aggiungere il pacchetto NuGet **WindowsAzure.MobileServices.WinJS** al progetto.

- Per un'app PhoneGap o Cordova, aggiungere il [plug-in Servizi mobili](https://github.com/Azure/azure-mobile-services-cordova) al progetto. Questo plug-in supporta le [notifiche push](#push-notifications).

Nell'editor aprire o creare un file JavaScript e aggiungere il codice seguente per definire la variabile `MobileServiceClient`, infine specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore `MobileServiceClient`, senza modificare l'ordine.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

È necessario sostituire il segnaposto `AppUrl` con l'URL dell'applicazione del servizio mobile e `AppKey` con la chiave dell'applicazione, che è possibile ottenere dal [portale di Azure classico](http://manage.windowsazure.com/).

>[AZURE.IMPORTANT]La chiave dell'applicazione consente di filtrare le richieste casuali nel servizio mobile e viene distribuita con l'applicazione. Poiché questa chiave non è crittografata, non può essere considerata sicura. Per proteggere i dati del servizio mobile, è necessario invece autenticare gli utenti prima di consentire l'accesso. Per altre informazioni, vedere la sezione [Procedura: Autenticare gli utenti](#authentication).

##<a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile

Tutto il codice che accede o modifica i dati nella tabella del database SQL chiama funzioni sull'oggetto `MobileServiceTable`. Per ottenere un riferimento alla tabella, chiamare la funzione `getTable()` su un'istanza dell'oggetto `MobileServiceClient`.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente illustra come filtrare i dati includendo una clausola `where` in una query. Il codice restituisce tutti gli elementi di `todoItemTable` il cui campo completo è uguale a `false`. `todoItemTable` è il riferimento alla tabella del servizio mobile creata in precedenza. La funzione where applica un predicato di filtro di riga alla query sulla tabella. Accetta come argomento un oggetto o una funzione JSON che definisce il filtro di riga e restituisce una query che può essere composta ulteriormente.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Eseguendo una chiamata alla funzione `where` sull'oggetto Query e passando un oggetto come parametro, si specifica che Servizi mobili dovrà restituire solo le righe la cui colonna `complete` contiene il valore `false`. Nell'URI della richiesta riportato di seguito si noterà inoltre che viene modificata la stringa di query stessa:

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

Esiste tuttavia un altro modo per scrivere la stessa query. Una chiamata a `.where` nell'oggetto Query aggiungerà un'espressione `AND` alla clausola `WHERE`, pertanto la query può anche essere scritta in tre righe:

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

I due metodi si equivalgono e possono essere utilizzati in modo intercambiabile. Tutte le chiamate a `where` descritte finora usano un oggetto con alcuni parametri e vengono confrontate con i dati del database per trovare uguaglianze. Esiste tuttavia un altro overload per il metodo della query, che utilizza una funzione anziché l'oggetto. In questa funzione è quindi possibile scrivere espressioni più complesse, utilizzando operatori come quello di disuguaglianza e altre operazioni relazionali. In queste funzioni la parola chiave `this` esegue l'associazione all'oggetto server.

Il corpo della funzione viene convertito in un'espressione booleana OData (Open Data Protocol), che viene passata a un parametro di stringa della query. È possibile passare una funzione senza parametri, come:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Se si passa una funzione con parametri, tutti gli argomenti dopo la clausola `where` vengono associati ai parametri della funzione, nell'ordine indicato. Tutti gli oggetti che provengono dall'esterno dell'ambito della funzione DEVONO essere passati come parametri, in quanto la funzione non può acquisire le variabili esterne. Nei due esempi seguenti l'argomento "david" è associato al parametro `name` e nel primo esempio l'argomento "medium" è anche associato al parametro `level`. La funzione deve inoltre essere costituita da un'unica istruzione `return` con un'espressione supportata, come segue:

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

È possibile combinare `where` con `orderBy`, `take` e `skip` Per informazioni dettagliate, vedere la sezione che segue.

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice seguente illustra come ordinare i dati includendo una funzione `orderBy` o `orderByDescending` nella query. L'operazione restituisce elementi della tabella `todoItemTable` in ordine crescente in base al campo `text`. Per impostazione predefinita, il server restituisce solo i primi 50 elementi.

> [AZURE.NOTE]Per impostazione predefinita, viene utilizzata una dimensione di pagina basata sul server, per evitare la restituzione di tutti gli elementi. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. È possibile aumentare il numero di elementi da restituire chiamando `take` come descritto nella sezione seguente. `todoItemTable` è il riferimento alla tabella del servizio mobile creata in precedenza.

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

Per impostazione predefinita, Servizi mobili restituisce solo 50 righe in una richiesta specificata, a meno che il client richieda in modo esplicito più dati nella risposta. Il codice seguente illustra come implementare il paging nei dati restituiti tramite le clausole `take` e `skip` nella query. La query seguente, se eseguita, restituisce le prime tre voci della tabella.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Si noti che il metodo `take(3)` è stato convertito nell'opzione di query `$top=3` nell'URI della query.

La query modificata riportata di seguito ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile. Si noti che il metodo `skip(3)` è stato convertito nell'opzione di query `$skip=3` nell'URI della query.

Si tratta di uno scenario in cui il passaggio di valori di paging hardcoded alle funzioni `take` e `skip` è stato semplificato. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola `select`. Il codice seguente restituisce ad esempio le proprietà `id`, `complete`, e `text` per ogni riga della tabella `todoItemTable`:

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

Servizi mobili usa le convenzioni URI della query OData per la creazione e l'esecuzione di query REST. Non tutte le query OData possono essere create mediante le funzioni di query integrate, in particolare le operazioni di filtro complesse come la ricerca di una sottostringa in una proprietà. Per query complesse di questo tipo, è possibile passare un'eventuale stringa di opzione di query OData alla funzione `read` nel modo seguente:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Quando si specifica una stringa di opzione di query OData nella funzione `read`, non è possibile usare anche i metodi del generatore di query nella stessa query. In questo caso, è necessario creare l'intera query come stringa di query OData. Per altre informazioni sulle opzioni di query del sistema OData, vedere il [riferimento alle opzioni di query del sistema OData].

##<a name="inserting"></a>Procedura: Inserire dati in un servizio mobile

Nel codice seguente viene illustrato come inserire nuove righe in una tabella. Il client richiede che una riga di dati venga inserita inviando una richiesta POST al servizio mobile. Il corpo della richiesta contiene i dati da inserire come oggetto JSON.

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

###Uso di valori ID

Servizi mobili supporta valori stringa univoci personalizzati per la colonna di tabella **id**. Ciò consente alle applicazioni di usare valori personalizzati come indirizzi e-mail o nomi utente per l'ID. Il codice seguente inserisce ad esempio un nuovo elemento come oggetto JSON, dove l'ID univoco è un indirizzo e-mail:

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

Gli ID stringa offrono i seguenti vantaggi:

+ È possibile generare ID senza generare un round trip del database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Quando un valore ID di stringa non è impostato su un record inserito, Servizi mobili genera un valore univoco per l'ID. Per altre informazioni su come generare valori ID personalizzati sul client o in un back-end .NET, vedere la sezione [Procedura: Generare valori ID univoci](mobile-services-how-to-use-server-scripts.md#generate-guids).

È inoltre possibile usare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario creare la tabella con il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene utilizzato con l'interfaccia della riga di comando (CLI) per Azure. Per ulteriori informazioni sull'utilizzo dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Procedura: Modificare dati in un servizio mobile

Nel codice seguente viene illustrato come aggiornare i dati in una tabella. Il client richiede che una riga di dati venga aggiornata inviando una richiesta PATCH al servizio mobile. Il corpo della richiesta contiene i campi specifici da aggiornare come oggetto JSON. Viene aggiornato un elemento esistente nella tabella `todoItemTable`.

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

##<a name="deleting"></a>Procedura: Eliminare dati in un servizio mobile

Nel codice seguente viene illustrato come eliminare i dati da una tabella. Il client richiede che una riga di dati venga eliminata inviando una richiesta DELETE al servizio mobile. Viene eliminato un elemento esistente nella tabella todoItemTable.

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

##<a name="binding"></a>Procedura: Visualizzare dati nell'interfaccia utente

In questa sezione viene illustrato come visualizzare gli oggetti dati restituiti utilizzando elementi dell'interfaccia utente. Per eseguire una query sugli elementi nella tabella `todoItemTable` e visualizzare i risultati in un elenco molto semplice, è possibile avviare il codice di esempio seguente. Non vengono eseguite operazioni di selezione, filtro o ordinamento di alcun tipo.

	var query = todoItemTable;

	query.read().then(function (todoItems) {
	   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
	   var listOfItems = document.getElementById('placeToInsert');
	   for (var i = 0; i < todoItems.length; i++) {
	      var li = document.createElement('li');
	      var div = document.createElement('div');
	      div.innerText = todoItems[i].text;
	      li.appendChild(div);
	      listOfItems.appendChild(li);
	   }
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

In un'app di Windows Store, i risultati di una query possono essere utilizzati per creare un oggetto [WinJS.Binding.List], che può essere associato come origine dati per un oggetto [ListView]. Per ulteriori informazioni, vedere [Associazione dati (app di Windows Store con JavaScript e HTML)].

##<a name="custom-api"></a>Procedura: Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON. Per un esempio di come creare un'API personalizzata nel servizio mobile, vedere [Procedura: definire un endpoint API personalizzato](mobile-services-dotnet-backend-define-custom-api.md).

È possibile chiamare un'API personalizzata dal client chiamando il metodo [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) su **MobileServiceClient**. Ad esempio, la riga di codice seguente invia una richiesta POST all'API **completeAll** sul Servizio mobile:

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });


Per esempi più realistici e una discussione completa sul metodo **invokeApi**, vedere l'articolo relativo all'[API personalizzata negli SDK dei client per Servizi mobili di Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

##<a name="authentication"></a>Procedura: Autenticare gli utenti

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti di app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione [Introduzione all'autenticazione].

>[AZURE.NOTE]Quando si usa l'autenticazione in un'app PhoneGap o Cordova, è anche necessario aggiungere al progetto i plug-in seguenti:
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


Sono supportati due flussi di autenticazione, un _flusso server_ e un _flusso client_. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

###Flusso server
Per consentire a Servizi mobili di gestire il processo di autenticazione nell'app di Windows Store o HTML5, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio mobile è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](mobile-services-html-get-started-users.md).

Dopo aver effettuato la registrazione del provider di identità, è sufficiente chiamare il metodo [LoginAsync] con il valore del provider [MobileServiceAuthenticationProvider]. Per accedere ad esempio con Facebook, utilizzare il codice seguente.

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo `login` riportato in precedenza con uno dei seguenti: `microsoftaccount`, `facebook`, `twitter`, `google` o `windowsazureactivedirectory`.

In questo caso, Servizi mobili gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione di Servizi mobili una volta eseguito correttamente l'accesso con il provider di identità. La funzione [login], quando è completa, restituisce un oggetto JSON (**user**) che espone l'ID utente e il token di autenticazione di Servizi mobili nei campi **userId** e **authenticationToken**, rispettivamente. È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per ulteriori informazioni, vedere [Memorizzare nella cache il token di autenticazione].

###Flusso client
L'applicazione può inoltre contattare il provider di identità in modo indipendente e fornire il token restituito a Servizi mobili per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

####Esempio di base SDK Facebook/Google

Questo esempio utilizza il client SDK di Facebook per l'autenticazione:

	client.login(
	     "facebook",
	     {"access_token": token})
	.done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

In questo esempio si presuppone che il token fornito dall'SDK del rispettivo provider sia archiviato nella variabile `token`. Non è al momento possibile utilizzare Twitter per l'autenticazione client.

####Esempio di base di Account Microsoft
Nell'esempio seguente viene utilizzato Live SDK, che supporta Single-Sign-On per le app di Windows Store tramite un account Microsoft:

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

Questo esempio semplificato ottiene un token da Live Connect, che viene passato a Servizi mobili chiamando la funzione [login].


####Esempio completo di Account Microsoft

Nell'esempio seguente viene illustrato come utilizzare il Live SDK con APIs WinJS per offrire un'esperienza avanzata di accesso singolo:

	// Set the mobileClient variable to client variable generated by the tooling.
	var mobileClient = <yourClient>;

	var session = null;
	var login = function () {
		return new WinJS.Promise(function (complete) {
			WL.login({ scope: "wl.basic" }).then(function (result) {
				session = result.session;

				WinJS.Promise.join([
					WL.api({ path: "me", method: "GET" }),
					mobileClient.login(result.session.authentication_token)
				]).done(function (results) {
					// Build the welcome message from the Microsoft account info.
					var profile = results[0];
					var title = "Welcome " + profile.first_name + "!";
					var message = "You are now logged in as: "
						+ mobileClient.currentUser.userId;
					var dialog = new Windows.UI.Popups.MessageDialog(message, title);
					dialog.showAsync().then(function () {
						// Reload items from the mobile service.
						refreshTodoItems();
					}).done(complete);

				}, function (error) {

				});
			}, function (error) {
				session = null;
				var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
				dialog.showAsync().done(complete);
			});
		});
	}

	var authenticate = function () {
		// Block until sign-in is successful.
		login().then(function () {
			if (session === null) {
				// Authentication failed, try again.
				authenticate();
			}
		});
	}

	// Initialize the Live client.
	WL.init({
		redirect_uri: mobileClient.applicationUrl
	});

	// Start the sign-in process.
	authenticate();

Questo codice inizializza il client Live Connect, invia una nuova richiesta di accesso all'account Microsoft, invia il token di autenticazione restituito a Servizi mobili e quindi visualizza informazioni sull'utente connesso. L'app non viene avviata fino a che l'autenticazione non ha esito positivo.
<!--- //this guidance may be bad from an XSS vulnerability standpoint. We need to find better guidance for this
###Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. We can use [sessionStorage] or [localStorage] to cache the current user identity the first time they log in and every subsequent time we check whether we already have the user identity in our cache. If the cache is empty or calls fail (meaning the current login session has expired), we still need to go through the login process.

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
-->

##<a name="push-notifications"></a>Procedura: Registrarsi per le notifiche push

Quando l'applicazione è un'app PhoneGap o Apache Cordova scritta in HTML/JavaScript, la piattaforma nativa per dispositivi mobili consente di ricevere notifiche push nel dispositivo. Il [ plug-in Apache Cordova per Servizi mobili di Azure](https://github.com/Azure/azure-mobile-services-cordova) consente di registrarsi per le notifiche push con Hub di notifica di Azure. Il servizio di notifica specifico usato dipende dalla piattaforma nativa del dispositivo in cui viene eseguito il codice. Per un esempio di come eseguire questa operazione, vedere l'esempio relativo all'[uso di Microsoft Azure per inviare notifiche push alle app Cordova](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle).

>[AZURE.NOTE]Questo plug-in attualmente supporta solo dispositivi iOS e Android. Per una soluzione che includa anche dispositivi Windows, vedere l'articolo relativo all'[invio di notifiche push alle app PhoneGap tramite l'integrazione di Hub di notifica](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx).

##<a name="errors"></a>Procedura: Gestire gli errori

In Servizi mobili è possibile rilevare, convalidare e risolvere gli errori in diversi modi.

Ad esempio, gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. Si consideri ad esempio la definizione e la registrazione di uno script del server per la convalida e la modifica dei dati:

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

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>Procedura: Usare le promesse

Le promesse offrono un meccanismo per pianificare il lavoro da eseguire su un valore che non è ancora stato calcolato. Si tratta di un'astrazione per la gestione delle interazioni con le API asincrone.

La promessa `done` viene eseguita non appena la funzione specificata viene completata o riceve un errore. A differenza della promessa `then`, genera sicuramente gli errori non gestiti all'interno della funzione e, dopo che i gestori hanno terminato l'esecuzione, la funzione genera gli errori che sarebbero stati restituiti da then come promessa nello stato di errore. Per ulteriori informazioni, vedere [done].

	promise.done(onComplete, onError);

In questo modo:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

La promessa `then` corrisponde alla promessa `done` ma, a differenza della promessa `then`, `done` garantisce la generazione di errori non gestiti all'interno della funzione. Se non si fornisce un gestore errori a `then` e l'operazione include un errore, non viene generata un'eccezione, ma viene restituita invece una promessa nello stato di errore. Per ulteriori informazioni, vedere [then].

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

In questo modo:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

È possibile utilizzare le promesse in molti modi diversi. È possibile concatenare operazioni di promessa chiamando `then` o `done` sulla promessa restituita dalla funzione `then` precedente. Usare `then` per una fase intermedia dell'operazione, ad esempio `.then().then()`, e `done` per la fase finale dell'operazione, ad esempio `.then().then().done()`. È possibile concatenare più funzioni `then`, in quanto `then` restituisce una promessa. Non è possibile concatenare più di un metodo `done`, in quanto restituisce un valore indefinito. [Ulteriori informazioni sulle differenze tra then e done].

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>Procedura: Personalizzare le intestazioni delle richieste client

È possibile inviare intestazioni di richieste personalizzate tramite la funzione `withFilter`, leggendo e scrivendo le proprietà arbitrarie della richiesta in fase di invio nel filtro. Aggiungere tale intestazione HTTP personalizzata se è necessaria o consigliabile per uno script sul lato server.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

I filtri sono utilizzati per molte altre operazioni oltre alla personalizzazione delle intestazioni di richieste. Consentono di esaminare o modificare le richieste, esaminare o modificare le risposte, ignorare le chiamate di rete, inviare più chiamate e così via.

##<a name="hostnames"></a>Procedura: Usare la condivisione di risorse tra origini

Per controllare quali siti Web possono interagire con il servizio mobile e inviare richieste a quest'ultimo, assicurarsi di aggiungere il nome host del sito Web usato per ospitare il servizio nell'elenco di condivisione di risorse tra le origini (CORS). Per un servizio mobile back-end JavaScript, è possibile configurare l'elenco nella scheda Configura del [portale di Azure classico](https://manage.windowsazure.com). È possibile utilizzare i caratteri jolly, se necessario. Per impostazione predefinita, il nuovo servizio mobile indica al browser di consentire l'accesso solo da `localhost`, mentre CORS consente al codice JavaScript in esecuzione in un browser su un nome host esterno di interagire con il servizio mobile. Questa configurazione non è necessaria per le applicazioni WinJS.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Ulteriori informazioni sulle differenze tra then e done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Associazione dati (app di Windows Store con JavaScript e HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[riferimento alle opzioni di query del sistema OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!---HONumber=AcomDC_1217_2015--->