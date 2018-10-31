---
title: Guida di riferimento per gli sviluppatori JavaScript di Funzioni di Azure | Microsoft Docs
description: Informazioni su come sviluppare funzioni con JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: eb9387cec98621e27aff7dcb40b8897e326c6706
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353493"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guida per gli sviluppatori JavaScript di Funzioni di Azure
Questa guida contiene informazioni sulle complessità correlate alla scrittura di funzioni di Azure con JavaScript.

Una funzione JavaScript è un oggetto `function` esportato che viene eseguito quando viene attivato ([i trigger vengono configurati in function.json](functions-triggers-bindings.md)). A ogni funzione viene passato un oggetto `context`, usato per la ricezione e l'invio di dati di associazione, la registrazione e la comunicazione con il runtime.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È anche consigliabile aver seguito un'esercitazione inclusa nelle guide introduttive su come [creare la prima funzione](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle di output per un progetto JavaScript ha un aspetto simile alla seguente. Questa struttura predefinita può essere modificata: vedere la sezione [scriptFile](functions-reference-node.md#using-scriptfile) di seguito per altre informazioni.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```

Nella radice del progetto è presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Per ogni funzione è presente una cartella con il file di codice (JS) e il file di configurazione delle associazioni (function.json) correlati.

Le estensioni di associazione richieste nella [versione 2.x](functions-versions.md) del runtime di Funzioni di Azure sono definite nel file `extensions.csproj`, con gli effettivi file di libreria inclusi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

## <a name="exporting-a-function"></a>Esportazione di una funzione

Le funzioni JavaScript devono essere esportate tramite [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (o [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Nel caso predefinito la funzione esportata deve essere l'unica esportazione dal relativo file, ovvero l'esportazione denominata `run` o quella denominata `index`. La posizione predefinita della funzione è `index.js`, dove `index.js` condivide la stessa directory padre del file `function.json` corrispondente. Si noti che il nome della directory padre di `function.json` è sempre il nome della funzione. 

Per configurare il percorso del file e il nome di esportazione della funzione, vedere [Configurare il punto di ingresso della funzione](functions-reference-node.md#configure-function-entry-point) più avanti.

Il punto di ingresso della funzione esportata deve sempre accettare un oggetto `context` come primo parametro.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```
```javascript
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

I trigger e le associazioni di input (associazioni di `direction === "in"`) possono essere passati alla funzione come parametri. Vengono passati alla funzione nell'ordine in cui sono definiti in *function.json*. È anche possibile gestire in modo dinamico gli input usando l'oggetto [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript. Se ad esempio si ha `function(context, a, b)` e viene modificato in `function(context, a)`, è comunque possibile ottenere il valore di `b` nel codice della funzione facendo riferimento a `arguments[2]`.

Anche tutte le associazioni, indipendentemente dalla direzione, vengono passate sull'oggetto `context` usando la proprietà `context.bindings`.

### <a name="exporting-an-async-function"></a>Esportazione di una funzione asincrona
Quando si usa la dichiarazione JavaScript [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) o le normali [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) JavaScript (non disponibili con Funzioni di Azure v1.x), non è necessario chiamare in modo esplicito il [`context.done`](#contextdone-method) callback per segnalare che la funzione è stata completata. La funzione verrà completata al termine della funzione asincrona esportata o della promessa.

Ad esempio, questo è una semplice funzione che registra che è stata attivata e termina immediatamente l'esecuzione.
``` javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Quando si esporta una funzione asincrona, è possibile anche configurare il valore delle associazioni di output su `return`. Si tratta di un approccio alternativo rispetto ad assegnare gli output usando la proprietà [`context.bindings`](#contextbindings-property).

Per assegnare un output usando `return`, modificare la proprietà `name` in `$return` nel file `function.json`.
```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```
Il codice della funzione Javascript potrebbe avere un aspetto simile al seguente:
```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="context-object"></a>Oggetto context
Il runtime usa un oggetto `context` per passare dati dalla e alla funzione e consentire la comunicazione con il runtime.

L'oggetto `context` è sempre il primo parametro di una funzione e deve essere incluso, in quanto contiene i metodi necessari per usare correttamente il runtime, ad esempio `context.done` e `context.log`. È possibile assegnare all'oggetto un nome qualsiasi, ad esempio `ctx` o `c`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Proprietà context.bindings

```
context.bindings
```
Restituisce un oggetto denominato che contiene tutti i dati di input e output. Ad esempio, le definizioni di associazione seguenti in *function.json* permettono di accedere al contenuto di una coda da `context.bindings.myInput` e di assegnare output a una coda tramite `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Si noti che è possibile scegliere di definire i dati di associazione di output usando il metodo `context.done` invece dell'oggetto `context.binding` (vedere di seguito).

### <a name="contextbindingdata-property"></a>Proprietà context.bindingData

```
context.bindingData
```
Restituisce un oggetto denominato che contiene i metadati del trigger e i dati di chiamata della funzione (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Per un esempio dei metadati del trigger, vedere questo [esempio di hub eventi](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Metodo context.done
```
context.done([err],[propertyBag])
```

Comunica al runtime che l'esecuzione è stata completata. Se la funzione usa la dichiarazione [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) JavaScript (disponibile con Node 8+ in Funzioni di Azure versione 2.x), non è necessario usare `context.done()`. Il callback `context.done` viene chiamato in modo implicito.

Se la funzione non è asincrona, **è necessario chiamare** `context.done` per informare il runtime che la funzione è completa. Se la funzione risulta assente, l'esecuzione raggiungerà il timeout.

Il metodo `context.done` permette di restituire un errore definito dall'utente al runtime e un oggetto JSON contenente i dati di associazione di output. Le proprietà passate a `context.done` sovrascrivono qualsiasi impostazione nell'oggetto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Metodo context.log  

```
context.log(message)
```
Permette di scrivere nei log della console di streaming nel livello di traccia predefinito. In `context.log` sono disponibili altri metodi di registrazione che permettono di scrivere log di funzioni in altri livelli di traccia:


| Metodo                 | DESCRIZIONE                                |
| ---------------------- | ------------------------------------------ |
| **error(_messaggio_)**   | Scrive nella registrazione a livello di errore o inferiore.   |
| **warn(_messaggio_)**    | Scrive nella registrazione a livello di avviso o inferiore. |
| **info(_messaggio_)**    | Scrive nella registrazione a livello di informazioni o inferiore.    |
| **verbose(_messaggio_)** | Scrive nella registrazione a livello dettagliato.           |

L'esempio seguente scrive un log nel livello di traccia di avviso:

```javascript
context.log.warn("Something has happened."); 
```
È possibile [configurare la soglia del livello di traccia per la registrazione](#configure-the-trace-level-for-console-logging) nel file host.json. Per altre informazioni sulla scrittura di log, vedere [Scrittura dell'output di traccia nella console](#writing-trace-output-to-the-console) più avanti.

Vedere [Monitoraggio di Funzioni di Azure](functions-monitoring.md) per altre informazioni sulla visualizzazione e sull'esecuzione di query su log di funzioni.

## <a name="binding-data-type"></a>Associazione del tipo di dati

Per definire il tipo di dati per un'associazione di input, usare la proprietà `dataType` nella definizione dell'associazione. Ad esempio, per leggere il contenuto di una richiesta HTTP in formato binario, usare il tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Le opzioni per `dataType` sono: `binary`, `stream` e `string`.

## <a name="writing-trace-output-to-the-console"></a>Scrittura dell'output di traccia nella console 

In Funzioni usare i metodi `context.log` per scrivere l'output di traccia nella console. In Funzioni di Azure v2.x gli output di traccia tramite `console.log` vengono acquisiti a livello di app per le funzioni. Questo significa che gli output di `console.log` non sono associati a una chiamata di funzione specifica e di conseguenza non vengono visualizzati nei log di una funzione specifica. Vengono tuttavia propagati in Application Insights. In Funzioni di Azure v1.x non è possibile usare `console.log` per scrivere nella console. 

Quando si chiama `context.log()`, il messaggio viene scritto nella console a livello di traccia predefinito, ovvero il livello di traccia _info_. Il codice seguente scrive nella console a livello di traccia informazioni:

```javascript
context.log({hello: 'world'});  
```

Questo codice equivale al codice mostrato sopra:

```javascript
context.log.info({hello: 'world'});  
```

Questo codice scrive nella console a livello di errore:

```javascript
context.log.error("An error has occurred.");  
```

Poiché _error_ è il livello di traccia più alto, questa traccia viene scritta nell'output a tutti i livelli di traccia, fintantoché la registrazione è abilitata.

Tutti i metodi `context.log` supportano lo stesso formato di parametri supportato dal metodo Node.js [ util.format](https://nodejs.org/api/util.html#util_util_format_format). Si consideri il codice seguente, che scrive log di funzioni usando il livello di traccia predefinito:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

È possibile scrivere lo stesso codice anche nel formato seguente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurare il livello di traccia per la registrazione della console

Funzioni consente di definire la soglia del livello di traccia per la scrittura nella console; questo consente di controllare più facilmente il modo in cui le tracce vengono scritte nella console dalle funzioni. Per impostare la soglia per tutte le tracce scritte nella console, usare la proprietà `tracing.consoleLevel` nel file host.json. Questa impostazione si applica a tutte le funzioni dell'app per le funzioni. L'esempio seguente imposta la soglia di traccia per abilitare la registrazione dettagliata:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

I valori di **consoleLevel** corrispondono ai nomi dei metodi `context.log`. Per disabilitare tutta la registrazione traccia nella console, impostare **consoleLevel** su _off_. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.  

### <a name="request-object"></a>Oggetto della richiesta

L'oggetto (richiesta) `context.req` ha le proprietà seguenti:

| Proprietà      | DESCRIZIONE                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Oggetto che contiene il corpo della richiesta.               |
| _headers_     | Oggetto che contiene le intestazioni della richiesta.                   |
| _method_      | Metodo HTTP della richiesta.                                |
| _originalUrl_ | URL della richiesta.                                        |
| _params_      | Oggetto che contiene i parametri di routing della richiesta. |
| _query_       | Oggetto che contiene i parametri di query della richiesta.                  |
| _rawBody_     | Il corpo del messaggio sotto forma di stringa.                           |


### <a name="response-object"></a>Oggetto della risposta

L'oggetto (risposta) `context.res` ha le proprietà seguenti:

| Proprietà  | DESCRIZIONE                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Oggetto che contiene il corpo della risposta.         |
| _headers_ | Oggetto che contiene le intestazioni della risposta.             |
| _isRaw_   | Indica che la formattazione viene ignorata per la risposta.    |
| _Stato_  | Codice di stato HTTP della risposta.                     |

### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta 

Quando si usano trigger HTTP, è possibile accedere agli oggetti richiesta e risposta HTTP in diversi modi:

+ Dalle proprietà `req` e `res` sull'oggetto `context`. In questo modo per accedere ai dati HTTP dall'oggetto di contesto è possibile usare il modello convenzionale anziché il modello `context.bindings.name` completo. L'esempio seguente illustra come accedere agli oggetti `req` e `res` nell'oggetto `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Dalle associazioni di input e di output denominate. In questo modo, il trigger e le associazioni HTTP funzionano come qualsiasi altra associazione. L'esempio seguente imposta l'oggetto risposta usando un'associazione `response` denominata. 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[Solo risposta]_ Chiamando `context.res.send(body?: any)`. Viene creata una risposta HTTP con input `body` come corpo della risposta. Viene chiamato `context.done()` in modo implicito.

+ _[Solo risposta]_ Chiamando `context.done()`. Un tipo speciale di associazione HTTP restituisce la risposta che viene passata al metodo `context.done()`. L'associazione di output HTTP seguente definisce un parametro di output `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versione del nodo

La tabella seguente mostra la versione di Node.js usata da ogni versione principale del runtime di Funzioni:

| Versione di Funzioni | Versione di Node.js | 
|---|---|
| 1.x | 6.11.2 (bloccata dal runtime) |
| 2.x  | Versioni di Node.js _Active LTS_ e _corrente_ Node.js (8.11.1 e 10.6.0 consigliate). Impostare la versione usando l'[impostazione dell'app](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

È possibile visualizzare la versione corrente usata dal runtime controllando l'impostazione dell'app seguente o stampando `process.version` da qualsiasi funzione.

## <a name="dependency-management"></a>Gestione delle dipendenze
Per poter usare librerie della community nel codice JavaScript, come mostrato nell'esempio seguente, è necessario assicurarsi che tutte le dipendenze siano installate nell'app per le funzioni in Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> È necessario definire un file `package.json` nella directory principale dell'app per le funzioni. La definizione del file consente a tutte le funzioni dell'app di condividere gli stessi pacchetti memorizzati nella cache garantendo prestazioni migliori. In caso di conflitto di versione, è possibile risolverlo aggiungendo un file `package.json` nella cartella di una funzione specifica.  

Quando si distribuiscono le app per le funzioni dal controllo del codice sorgente, durante la distribuzione qualsiasi file `package.json` presente nel repository attiverà un comando `npm install` nella cartella in cui si trova. Ma quando si esegue una distribuzione tramite il portale o l'interfaccia della riga di comando, è necessario installare il pacchetto manualmente.

Esistono due modi per installare pacchetti nell'app per le funzioni: 

### <a name="deploying-with-dependencies"></a>Distribuzione con le dipendenze
1. Installare tutti i pacchetti necessari in locale eseguendo `npm install`.

2. Distribuire il codice e assicurarsi che la cartella `node_modules` sia inclusa nella distribuzione. 


### <a name="using-kudu"></a>Tramite Kudu
1. Passare a `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **Debug Console** > **CMD** (Console di debug > CMD).

3. Passare a `D:\home\site\wwwroot`, quindi trascinare il file package.json nella cartella **wwwroot** nella metà superiore della pagina.  
    È possibile caricare i file nell'app per le funzioni anche in altri modi. Per altre informazioni, vedere [Come aggiornare i file delle app per le funzioni](functions-reference.md#fileupdate). 

4. Dopo aver caricato il file package.json, eseguire il comando `npm install` nella  **console di esecuzione remota Kudu**.  
    Questa azione scarica i pacchetti indicati nel file package.json e riavvia l'app per le funzioni.

## <a name="environment-variables"></a>Variabili di ambiente
Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `process.env`come illustrato qui nella funzione `GetEnvironmentVariable`:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>Configurare il punto di ingresso della funzione

È possibile usare le proprietà `scriptFile` e `entryPoint` di `function.json` per configurare il percorso e il nome della funzione esportata. Queste informazioni possono essere importanti se JavaScript viene transcompilato.

### <a name="using-scriptfile"></a>Usando `scriptFile`

Per impostazione predefinita, viene eseguita una funzione JavaScript da `index.js`, un file che condivide la stessa directory padre del file `function.json` corrispondente.

È possibile usare `scriptFile` per ottenere una struttura di cartelle simile alla seguente:
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Il file `function.json` per `myNodeFunction` deve includere una proprietà `scriptFile` che punta al file con la funzione esportata da eseguire.
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando `entryPoint`

In `scriptFile` (o `index.js`) una funzione deve essere esportata tramite `module.exports` per poter essere trovata ed eseguita. Per impostazione predefinita, la funzione eseguita quando viene attivata è l'unica esportazione dal file, ovvero l'esportazione denominata `run` o quella denominata `index`.

Questa può essere configurata tramite `entryPoint` in `function.json`:
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In Funzioni v2.x, che supporta il parametro `this` nelle funzioni utente, il codice della funzione può quindi essere il seguente:
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In questo esempio è importante notare che anche se viene esportato un oggetto, non esiste alcuna garanzia riguardo al mantenimento dello stato tra esecuzioni.

## <a name="considerations-for-javascript-functions"></a>Considerazioni per le funzioni JavaScript

Quando si usano funzioni JavaScript, tenere presente le considerazioni indicate nelle due sezioni seguenti.

### <a name="choose-single-vcpu-app-service-plans"></a>Scegliere i piani di servizio app con una singola vCPU

Quando si crea un'app per le funzioni che usa il piano di servizio app, è consigliabile selezionare un piano con una singola vCPU anziché uno con più vCPU. Oggi Funzioni esegue funzioni JavaScript in modo più efficiente in macchine virtuali con una singola vCPU e l'uso di macchine virtuali di dimensioni maggiori non produce i miglioramenti delle prestazioni previsti. Se necessario, è possibile scalare orizzontalmente manualmente aggiungendo altre istanze di macchine virtuali con una singola vCPU oppure abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Supporto TypeScript e CoffeeScript
Dal momento che non è ancora disponibile il supporto diretto per la compilazione automatica di TypeScript o CoffeeScript tramite il runtime, il supporto deve essere gestito all'esterno del runtime al momento della distribuzione. 

### <a name="cold-start"></a>Avvio a freddo
Quando si sviluppano funzioni di Azure in un modello di hosting serverless, gli avvii a freddo sono una realtà. Il termine "avvio a freddo" si riferisce al fatto che quando viene avviata per la prima volta dopo un periodo di inattività, l'app per le funzioni richiederà più tempo per l'avvio. Per le funzioni JavaScript con alberi di dipendenze di grandi dimensioni, in particolare, questo comportamento può provocare rallentamenti notevoli. Per accelerare il processo, se possibile, [eseguire le funzioni come file di pacchetto](run-functions-from-deployment-package.md). Molti metodi di distribuzione scelgono esplicitamente questo modello per impostazione predefinita, ma se si riscontrano avvii a freddo prolungati e l'esecuzione non viene eseguita da un file di pacchetto, questo approccio può costituire un miglioramento sostanziale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

