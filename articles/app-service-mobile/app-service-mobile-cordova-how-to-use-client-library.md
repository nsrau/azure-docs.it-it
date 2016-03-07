<properties
	pageTitle="Come usare il plug-in Apache Cordova per le app per dispositivi mobili di Azure"
	description="Come usare il plug-in Apache Cordova per le app per dispositivi mobili di Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Come usare la libreria client Apache Cordova per le app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [plug-in Apache Cordova per le app per dispositivi mobili di Azure]. Se non si ha familiarità con le app per dispositivi mobili di Azure, completare prima di tutto l'[esercitazione introduttiva sulle app per dispositivi mobili di Azure] per creare un back-end e una tabella e per scaricare un progetto Apache Cordova predefinito. In questa guida si esaminerà il plug-in Apache Cordova.

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni. Si presuppone anche che il plug-in Apache Cordova sia stato aggiunto al codice. In caso contrario, è possibile aggiungere il plug-in Apache Cordova al progetto nella riga di comando:

```
cordova plugin add ms-azure-mobile-apps
```

Per altre informazioni sulla creazione della [prima app Apache Cordova], vedere la relativa documentazione.

##<a name="create-client"></a>Procedura: creare Client

Creare una connessione client creando un oggetto `WindowsAzure.MobileServicesClient`. Sostituire `appUrl` con l'URL dell'app per dispositivi mobili.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Procedura: Creare un riferimento alla tabella

Per l'accesso o l'aggiornamento dei dati, creare un riferimento alla tabella di back-end. Sostituire `tableName` con il nome della tabella.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Procedura: Eseguire query su un riferimento a tabella

Dopo aver creato un riferimento a tabella, è possibile usarlo per eseguire una query sui dati nel server. Le query vengono eseguite in un linguaggio "simile a LINQ". Per restituire tutti i dati dalla tabella, usare quanto segue:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La funzione success viene chiamata con l'oggetto results. Non usare `for (var i in results)` nella funzione success, perché scorre le informazioni incluse nei risultati quando si usano altre funzioni di query, ad esempio `.includeTotalCount()`.

Per altre informazioni sulla sintassi di Query, vedere la [documentazione relativa all'oggetto Query].

### Filtro dei dati nel server

È possibile usare una clausola `where` nel riferimento a tabella:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

È anche possibile usare una funzione che filtra l'oggetto. In questo caso la variabile `this` viene assegnata all'oggetto che si sta filtrando. A livello funzionale, l'esempio seguente è equivalente al precedente:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Paging dei dati

Usare i metodi take() e skip(). Ad esempio, se si vuole dividere la tabella in record di 100 righe:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Il metodo `.includeTotalCount()` viene usato per aggiungere un campo totalCount all'oggetto results. Se non si usa il paging, il campo totalCount viene compilato con il numero totale di record restituiti.

Si potrà quindi usare la variabile pages e alcuni pulsanti dell'interfaccia utente per fornire un elenco di pagine. Usare loadPage() per caricare i nuovi record per ogni pagina. È necessario implementare una sorta di caching per velocizzare l'accesso ai record già caricati.


###<a name="sorting-data"></a>Procedura: Restituire dati ordinati

Usare i metodi di query .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Per altre informazioni sull'oggetto Query, vedere la [documentazione relativa all'oggetto Query].

##<a name="inserting"></a>Procedura: Inserire dati

Creare un oggetto JavaScript con la data appropriata e chiamare table.insert() in modo asincrono:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Una volta completato l'inserimento, viene restituito l'elemento inserito con i campi aggiuntivi necessari per le operazioni di sincronizzazione. È necessario aggiornare la cache con queste informazioni per gli aggiornamenti successivi.

Si noti che Node. js Server SDK per le app per dispositivi mobili supporta lo schema dinamico per scopi di sviluppo. Nel caso di schema dinamico, lo schema della tabella viene aggiornato in tempo reale, consentendo di aggiungere colonne alla tabella specificandole semplicemente in un'operazione di inserimento o aggiornamento. È consigliabile disattivare lo schema dinamico prima di trasferire l'applicazione in produzione.

##<a name="modifying"></a>Procedura: Modificare dati

In modo analogo al metodo .insert(), è consigliabile creare un oggetto Update e quindi chiamare .update(). L'oggetto update deve contenere l'ID del record da aggiornare, che si ottiene durante la lettura del record o quando si chiama .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Procedura: Eliminare dati

Chiamare il metodo .del() per eliminare un record. Passare l'ID in un riferimento all'oggetto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Procedura: Autenticare gli utenti

Il servizio app di Azure supporta l'autenticazione e l'autorizzazione degli utenti di app usando diversi provider di identità esterni, a esempio Facebook, Google, account Microsoft e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere l'esercitazione [Introduzione all'autenticazione].

Quando si usa l'autenticazione in un'app Apache Cordova, devono essere disponibili i plug-in Cordova seguenti:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Sono supportati due flussi di autenticazione, ovvero un flusso server e un flusso client. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

##<a name="server-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso server)

Per consentire a Servizi mobili di gestire il processo di autenticazione nell'app di Windows Store o HTML5, è necessario registrare l'app con il provider di identità. Nel proprio servizio app di Azure è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Dopo la registrazione del provider di identità, è sufficiente chiamare il metodo .login() con il nome del provider. Per accedere ad esempio con Facebook, utilizzare il codice seguente.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo login precedente con uno dei seguenti: `microsoftaccount`, `facebook`, `twitter`, `google` o `aad`

In questo caso, il servizio app di Azure gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione del servizio app una volta eseguito correttamente l'accesso con il provider di identità. La funzione login, una volta completata, restituisce un oggetto JSON (user) che espone l'ID utente e il token di autenticazione del servizio app rispettivamente nei campi userId e authenticationToken. È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza.

##<a name="client-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso client)

L'app può anche contattare il provider di identità in modo indipendente e quindi fornire il token restituito al servizio app per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

### Esempio di base di autenticazione tramite social network

Questo esempio utilizza il client SDK di Facebook per l'autenticazione:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
In questo esempio si presuppone che il token fornito dall'SDK del rispettivo provider sia archiviato nella variabile token.

### Esempio di account Microsoft

Nell'esempio seguente viene utilizzato Live SDK, che supporta Single-Sign-On per le app di Windows Store tramite un account Microsoft:

```
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
```

Questo esempio ottiene un token da Live Connect, che viene fornito al servizio app chiamando la funzione login.

##<a name="auth-getinfo"></a>Procedura: Ottenere informazioni relative all'utente autenticato

Le informazioni di autenticazione per l'utente corrente possono essere recuperate dall'endpoint `/.auth/me` usando un metodo AJAX qualsiasi. Ad esempio, per usare l'API fetch:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

È anche possibile usare jQuery o un'altra API AJAX per recuperare le informazioni. I dati saranno ricevuti come oggetto JSON.

##<a name="register-for-push"></a>Procedura: Registrarsi per le notifiche push

Installare [phonegap-plug-in-push] per gestire le notifiche push. Può essere facilmente aggiunto usando il comando `cordova plugin add` nella riga di comando o tramite il programma di installazione del plug-in Git in Visual Studio. Il codice seguente nell'app Apache Cordova registrerà il dispositivo per le notifiche push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Usare Notification Hubs SDK per inviare notifiche push dal server. Non inviare mai le notifiche push direttamente dai client, perché potrebbero essere usate per attivare un attacco Denial of Service nei confronti di Hub di notifica o PNS.

<!-- URLs. -->
[esercitazione introduttiva sulle app per dispositivi mobili di Azure]: app-service-mobile-cordova-get-started.md
[Introduzione all'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Aggiungere l'autenticazione all'app di Servizi mobili]: app-service-mobile-cordova-get-started-users.md

[plug-in Apache Cordova per le app per dispositivi mobili di Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[prima app Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentazione relativa all'oggetto Query]: https://msdn.microsoft.com/it-IT/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->