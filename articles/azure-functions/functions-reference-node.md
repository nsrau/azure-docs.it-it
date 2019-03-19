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
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 04653dcdf0fb64e8b935cda18c01198ec91c548d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807474"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guida per gli sviluppatori JavaScript di Funzioni di Azure

Questa guida contiene informazioni sulle complessità correlate alla scrittura di funzioni di Azure con JavaScript.

Una funzione JavaScript è un oggetto `function` esportato che viene eseguito quando viene attivato ([i trigger vengono configurati in function.json](functions-triggers-bindings.md)). Il primo argomento passato a ogni funzione è un `context` oggetto, che viene usato per la ricezione e trasmissione data binding, la registrazione e la comunicazione con il runtime.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). Completare l'avvio rapido di funzioni per creare la prima funzione, usando [Visual Studio Code](functions-create-first-function-vs-code.md) oppure [nel portale di](functions-create-first-azure-function.md).

Questo articolo supporta anche [lo sviluppo di app TypeScript](#typescript).

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle di output per un progetto JavaScript ha un aspetto simile alla seguente. Questa impostazione predefinita non può essere modificata. Per altre informazioni, vedere la sezione [scriptFile](#using-scriptfile) di seguito.

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

Nella radice del progetto è presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Per ogni funzione è presente una cartella con il file di codice (JS) e il file di configurazione delle associazioni (function.json) correlati. Il nome della directory padre di `function.json` è sempre il nome della funzione.

Le estensioni di associazione richieste nella [versione 2.x](functions-versions.md) del runtime di Funzioni sono definite nel file `extensions.csproj`, con gli effettivi file di libreria inclusi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](./functions-bindings-register.md#local-development-azure-functions-core-tools). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

## <a name="exporting-a-function"></a>Esportazione di una funzione

Le funzioni JavaScript devono essere esportate tramite [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (o [`exports`](https://nodejs.org/api/modules.html#modules_exports)). La funzione esportata deve essere una funzione JavaScript che viene eseguita quando attivata.

Per impostazione predefinita, il runtime di Funzioni cerca la funzione in `index.js`, dove `index.js` condivide la stessa directory padre del file `function.json` corrispondente. Nel caso predefinito la funzione esportata deve essere l'unica esportazione dal relativo file, ovvero l'esportazione denominata `run` o `index`. Per configurare il percorso del file e il nome di esportazione della funzione, vedere [Configurare il punto di ingresso della funzione](functions-reference-node.md#configure-function-entry-point) più avanti.

Alla funzione esportata vengono passati vari di argomenti per l'esecuzione. Il primo argomento accettato è sempre un oggetto `context`. Se la funzione è sincrona (non restituisce un oggetto Promise), è necessario passare l'oggetto `context`, perché la chiamata di `context.done` è richiesta per un uso corretto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Esportazione di una funzione asincrona
Quando si usano la dichiarazione [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) o semplici oggetti [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) JavaScript nella versione 2.x del runtime di Funzioni, non è necessario chiamare in modo esplicito il callback [`context.done`](#contextdone-method) per segnalare che la funzione è stata completata. La funzione viene completata al termine della funzione asincrona esportata o di Promise. Per le funzioni destinate alla versione 1.x del runtime, è necessario chiamare comunque [`context.done`](#contextdone-method) al termine dell'esecuzione del codice.

L'esempio seguente è una semplice funzione che registra che è stata attivata e termina immediatamente l'esecuzione.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Quando si esporta una funzione asincrona, è possibile anche configurare il valore `return` per un'associazione di output. Questa procedura è consigliata in presenza di una sola associazione di output.

Per assegnare un output usando `return`, modificare la proprietà `name` in `$return` nel file `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In questo caso, la funzione dovrebbe essere simile all'esempio seguente:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Associazioni 
In JavaScript le [associazioni](functions-triggers-bindings.md) vengono configurate e definite nel file function.json di una funzione. Le funzioni interagiscono con le associazioni in molti modi.

### <a name="inputs"></a>Input
In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Le associazioni di trigger e di altri input (associazioni di `direction === "in"`) possono essere lette da una funzione in tre modi:
 - **_[Consigliato]_  Come parametri passati alla funzione.** Vengono passati alla funzione nell'ordine in cui sono definiti in *function.json*. Il `name` definita nella proprietà *Function. JSON* non devono corrispondere al nome del parametro, anche se è necessario.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Come membri dell'oggetto [`context.bindings`](#contextbindings-property).** Ogni membro è denominato in base alla proprietà `name` definita in *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Come input tramite l'oggetto JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx).** Ciò equivale essenzialmente a passare gli input come parametri, ma consente di gestire in modo dinamico gli input.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Output
Gli output (associazioni di `direction === "out"`) possono essere scritti da una funzione in diversi modi. In tutti i casi, la proprietà `name` dell'associazione come definita in *function.json* corrisponde al nome del membro dell'oggetto scritto nella funzione. 

È possibile assegnare i dati per le associazioni di output in uno dei modi seguenti (non combinare i due metodi):

- **_[Consigliato per più output]_  Restituzione di un oggetto.** Se si usa una funzione asincrona o che restituisce oggetti Promise, è possibile restituire un oggetto con i dati di output assegnati. Nell'esempio seguente, le associazioni di output sono denominate "httpResponse" e "queueOutput" in *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Se si usa una funzione sincrona, è possibile restituire questo oggetto usando [`context.done`](#contextdone-method) (vedere l'esempio).
- **_[Consigliato per singolo output]_  Restituzione diretta di un valore e uso del nome di associazione $return.** Questo approccio vale solo per le funzioni asincrone o che restituiscono oggetti Promise. Vedere l'esempio in [Esportazione di una funzione asincrona](#exporting-an-async-function). 
- **Assegnazione di valori a `context.bindings`** È possibile assegnare valori direttamente a context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo di dati delle associazioni

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

## <a name="context-object"></a>Oggetto context
Il runtime usa un oggetto `context` per passare dati dalla e alla funzione e consentire la comunicazione con il runtime. L'oggetto context può essere usato per la lettura e l'impostazione dei dati dalle associazioni, la scrittura di log e l'uso del callback `context.done` quando la funzione esportata è sincrona.

L'oggetto `context` è sempre il primo parametro per una funzione. Deve essere incluso perché contiene metodi importanti, come `context.done` e `context.log`. È possibile assegnare all'oggetto un nome qualsiasi, ad esempio `ctx` o `c`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Proprietà context.bindings

```js
context.bindings
```

Restituisce un oggetto denominato che contiene tutti i dati di input e output. Ad esempio, le definizioni di associazione seguenti in function.json consentono di accedere al contenuto di una coda da `context.bindings.myInput` e di assegnare output a una coda tramite `context.bindings.myOutput`.

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

È possibile scegliere di definire i dati di associazione di output usando il metodo `context.done` invece dell'oggetto `context.binding` (vedere di seguito).

### <a name="contextbindingdata-property"></a>Proprietà context.bindingData

```js
context.bindingData
```

Restituisce un oggetto denominato che contiene i metadati del trigger e i dati di chiamata della funzione (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Per un esempio dei metadati del trigger, vedere questo [esempio di hub eventi](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Metodo context.done

```js
context.done([err],[propertyBag])
```

Segnala al runtime che il codice è stato completato. Quando la funzione usa la dichiarazione [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), non è necessario usare `context.done()`. Il callback `context.done` viene chiamato in modo implicito. Le funzioni asincrone sono disponibili in Node 8 o versione successiva, che richiede la versione 2.x del runtime di Funzioni.

Se la funzione non è asincrona, **è necessario chiamare** `context.done` per informare il runtime che la funzione è completa. Si verifica il timeout dell'esecuzione se manca.

Il metodo `context.done` permette di restituire un errore definito dall'utente al runtime e un oggetto JSON contenente i dati di associazione di output. Le proprietà passate a `context.done` sovrascrivono qualsiasi impostazione nell'oggetto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Metodo context.log  

```js
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

Con Funzioni 1.x è possibile definire la soglia del livello di traccia per la scrittura nella console. Ciò consente di controllare più facilmente il modo in cui le tracce vengono scritte nella console dalla funzione. Per impostare la soglia per tutte le tracce scritte nella console, usare la proprietà `tracing.consoleLevel` nel file host.json. Questa impostazione si applica a tutte le funzioni dell'app per le funzioni. L'esempio seguente imposta la soglia di traccia per abilitare la registrazione dettagliata:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

I valori di **consoleLevel** corrispondono ai nomi dei metodi `context.log`. Per disabilitare tutta la registrazione traccia nella console, impostare **consoleLevel** su _off_. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json-v1.md).

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

+ **Dalle proprietà `req` e `res` per l'oggetto `context`.** In questo modo per accedere ai dati HTTP dall'oggetto di contesto è possibile usare il modello convenzionale anziché il modello `context.bindings.name` completo. L'esempio seguente illustra come accedere agli oggetti `req` e `res` nell'oggetto `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Dalle associazioni di input e di output denominate.** In questo modo, il trigger e le associazioni HTTP funzionano come qualsiasi altra associazione. L'esempio seguente imposta l'oggetto risposta usando un'associazione `response` denominata. 

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
+ **_[Solo risposta]_ Chiamando `context.res.send(body?: any)`.** Viene creata una risposta HTTP con input `body` come corpo della risposta. Viene chiamato `context.done()` in modo implicito.

+ **_[Solo risposta]_ Chiamando `context.done()`.** Un tipo speciale di associazione HTTP restituisce la risposta che viene passata al metodo `context.done()`. L'associazione di output HTTP seguente definisce un parametro di output `$return`:

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
| 2.x  | Versioni di Node.js _Active LTS_ e _correnti_ con numero pari (8.11.1 e 10.14.1 consigliate). Impostare la versione usando l'[impostazione dell'app](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

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

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando `process.env`, come illustrato di seguito nella funzione `GetEnvironmentVariable`:

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

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Per l'esecuzione in locale, le impostazioni dell'app vengono lette dal file di progetto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="configure-function-entry-point"></a>Configurare il punto di ingresso della funzione

È possibile usare le proprietà `scriptFile` e `entryPoint` di `function.json` per configurare il percorso e il nome della funzione esportata. Queste proprietà possono essere importanti quando JavaScript viene sottoposto a transpile.

### <a name="using-scriptfile"></a>Usando `scriptFile`

Per impostazione predefinita, viene eseguita una funzione JavaScript da `index.js`, un file che condivide la stessa directory padre del file `function.json` corrispondente.

È possibile usare `scriptFile` per ottenere una struttura di cartelle simile a quella nell'esempio seguente:

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

Questo aspetto può essere configurato tramite `entryPoint` in `function.json`, come illustrato nell'esempio seguente:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In Funzioni v2.x, che supporta il parametro `this` nelle funzioni utente, il codice della funzione può quindi essere simile all'esempio seguente:

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

In questo esempio, è importante notare che anche se un oggetto viene esportato, non vi sono garanzie che consentono di mantenere lo stato tra le esecuzioni.

## <a name="typescript"></a>TypeScript

Quando si destina versione 2.x del runtime di funzioni, entrambe [funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md) e il [Azure Functions Core Tools](functions-run-local.md) consentono di creare App per le funzioni usando un modello che supportano Progetti di app di funzione di TypeScript. Il modello genera `package.json` e `tsconfig.json` i file di progetto che rendono più semplice per eseguire il transpile, eseguire e pubblicano le funzioni JavaScript da codice TypeScript con questi strumenti.

Un oggetto generato `.funcignore` file viene usato per indicare quali file sono escluse quando viene pubblicato un progetto in Azure.  

File TypeScript (. TS) vengono convertito tramite transpile in file JavaScript (js) nel `dist` directory di output. I modelli di TypeScript usano il [ `scriptFile` parametro](#using-scriptfile) nel `function.json` per indicare il percorso del file con estensione js corrispondente nel `dist` cartella. Il percorso di output viene impostato dal modello usando `outDir` parametro il `tsconfig.json` file. Se si modifica questa impostazione o il nome della cartella, il runtime non è in grado di trovare il codice da eseguire.

> [!NOTE]
> Il supporto sperimentale per TypeScript esiste versione 1.x del runtime di funzioni. La versione sperimentale transpiles file TypeScript nei file JavaScript quando viene richiamata la funzione. Nella versione 2.x, il supporto sperimentale è stato sostituito dal metodo basato su strumento che esegue volta prima che venga inizializzato l'host e durante il processo di distribuzione.

Il modo in cui in locale sviluppa e Distribuisci da un progetto TypeScript dipende da uno strumento di sviluppo.

### <a name="visual-studio-code"></a>Visual Studio Code

Il [funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) estensione consente di sviluppare le funzioni con TypeScript. Gli strumenti di base è un requisito dell'estensione di funzioni di Azure.

Per creare un'app per le funzioni TypeScript in Visual Studio Code, è sufficiente scegliere `TypeScript` quando si crea un'app per le funzioni e viene richiesto di scegliere la lingua.

Quando si preme **F5** per eseguire l'app in locale, volta viene eseguito prima che venga inizializzato l'host (func.exe). 

Quando si distribuisce l'app per le funzioni in Azure usando il **Distribuisci in app per le funzioni...**  pulsante, l'estensione di funzioni di Azure genera l'errore prima di tutto una build pronta per la produzione di file JavaScript dai file di origine TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Per creare un progetto di app di funzione di TypeScript usando strumenti di base, è necessario specificare l'opzione di linguaggio typescript quando crei app per le funzioni. È possibile eseguire questa operazione in uno dei modi seguenti:

- Eseguire la `func init` comando, seleziona `node` come lo stack di linguaggio e quindi selezionare `typescript`.

- Eseguire il comando `func init --worker-runtime typescript`.

Per eseguire il codice dell'app funzioni in locale usando strumenti di base, usare il `npm start` comando, invece di `func host start`. Il `npm start` comando equivale ai comandi seguenti:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Prima di usare la [ `func azure functionapp publish` ] comando per distribuire in Azure, è necessario eseguire prima il `npm run build:production` comando. Questo comando crea una build pronta per la produzione di file JavaScript dal file di origine TypeScript che possono essere distribuiti tramite [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Considerazioni per le funzioni JavaScript

Quando si usano funzioni JavaScript, tenere presente le considerazioni indicate nelle due sezioni seguenti.

### <a name="choose-single-vcpu-app-service-plans"></a>Scegliere i piani di servizio app con una singola vCPU

Quando si crea un'app per le funzioni che usa il piano di servizio app, è consigliabile selezionare un piano con una singola vCPU anziché uno con più vCPU. Oggi Funzioni esegue funzioni JavaScript in modo più efficiente in macchine virtuali con una singola vCPU e l'uso di macchine virtuali di dimensioni maggiori non produce i miglioramenti delle prestazioni previsti. Se necessario, è possibile scalare orizzontalmente manualmente aggiungendo altre istanze di macchina virtuale singola vCPU oppure è possibile abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Avvio a freddo

Quando si sviluppano funzioni di Azure in un modello di hosting serverless, gli avvii a freddo sono una realtà. Il termine *avvio a freddo* si riferisce al fatto che quando viene avviata per la prima volta dopo un periodo di inattività, l'app per le funzioni richiede più tempo per l'avvio. Per le funzioni di JavaScript con alberi delle dipendenze estesi, in particolare, l'avvio a freddo può essere significativo. Per velocizzare il processo di avvio a freddo [eseguire le funzioni come un file di pacchetto](run-functions-from-deployment-package.md) quando possibile. Molti metodi di distribuzione usano il modello di esecuzione dal pacchetto per impostazione predefinita, ma in presenza di numerosi avvii a freddo e se questo non è il modello di esecuzione in uso, questa modifica può offrire miglioramenti significativi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

+ [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
+ [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
+ [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
