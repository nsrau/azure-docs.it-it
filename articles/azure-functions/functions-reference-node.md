---
title: Guida di riferimento per gli sviluppatori JavaScript di Funzioni di Azure | Microsoft Docs
description: Informazioni su come sviluppare funzioni con JavaScript.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: chrande, glenga
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 641afd78aae145c5e1b16a08567a22c1aafe59a8
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="azure-functions-javascript-developer-guide"></a>Guida per gli sviluppatori JavaScript di Funzioni di Azure
> [!div class="op_single_selector"]
> * [Script C#](functions-reference-csharp.md)
> * [Script F#](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

L'esperienza JavaScript per Funzioni di Azure semplifica l'esportazione di una funzione, che viene passata come oggetto `context` per la comunicazione con il runtime e per la ricezione e l'invio di dati tramite associazioni.

Questo articolo presuppone che l'utente abbia già letto [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Esportazione di una funzione
Tutte le funzioni JavaScript devono esportare una singola `function` tramite `module.exports` per consentire al runtime di trovare la funzione ed eseguirla. Questa funzione deve sempre includere un oggetto `context` .

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Le associazioni di `direction === "in"` vengono passate come argomenti della funzione, pertanto è possibile usare [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) per gestire in modo dinamico nuovi input, ad esempio usando `arguments.length` per l'iterazione di tutti gli input. Questa funzionalità è utile se si ha solo un trigger e nessun input aggiuntivo, in quanto consente di accedere ai dati del trigger in modo prevedibile senza fare riferimento all'oggetto `context`.

Gli argomenti vengono sempre passati insieme alla funzione nell'ordine in cui sono indicati nel file *function.json*, anche se non vengono specificati nell'istruzione exports. Se ad esempio si ha `function(context, a, b)` e viene modificato in `function(context, a)`, è comunque possibile ottenere il valore di `b` nel codice della funzione facendo riferimento a `arguments[3]`.

Anche tutte le associazioni, indipendentemente dalla direzione, vengono passate sull'oggetto `context` (vedere lo script seguente). 

## <a name="context-object"></a>Oggetto context
Il runtime usa un oggetto `context` per passare dati dalla e alla funzione e consentire la comunicazione con il runtime.

L'oggetto context è sempre il primo parametro di una funzione e deve essere incluso, in quanto contiene i metodi necessari per usare correttamente il runtime, ad esempio `context.done` e `context.log`. È possibile assegnare all'oggetto un nome qualsiasi, ad esempio `ctx` o `c`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Proprietà context.bindings

```
context.bindings
```
Restituisce un oggetto denominato che contiene tutti i dati di input e output. Data la seguente definizione di associazione in *function.json*, è ad esempio possibile accedere al contenuto della coda tramite l'oggetto `context.bindings.myInput`. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
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

### <a name="contextdone-method"></a>Metodo context.done
```
context.done([err],[propertyBag])
```

Comunica al runtime che l'esecuzione è stata completata. La chiamata a `context.done` è necessaria. In caso contrario, il runtime non saprà mai che la funzione è stata completata e l'esecuzione raggiungerà il timeout. 

Il metodo `context.done` consente di passare di nuovo al runtime sia un errore definito dall'utente sia un contenitore delle proprietà con proprietà che sovrascriveranno quelle presenti nell'oggetto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Metodo context.log  

```
context.log(message)
```
Consente di scrivere nei log della console in streaming a livello di traccia predefinito. In `context.log` sono disponibili altri metodi di registrazione che consentono di scrivere nel log della console ad altri livelli di traccia:


| Metodo                 | Descrizione                                |
| ---------------------- | ------------------------------------------ |
| **error(_messaggio_)**   | Scrive nella registrazione a livello di errore o inferiore.   |
| **warn(_messaggio_)**    | Scrive nella registrazione a livello di avviso o inferiore. |
| **info(_messaggio_)**    | Scrive nella registrazione a livello di informazioni o inferiore.    |
| **verbose(_messaggio_)** | Scrive nella registrazione a livello dettagliato.           |

L'esempio seguente scrive nella console a livello di traccia di avviso:

```javascript
context.log.warn("Something has happened."); 
```
È possibile impostare la soglia del livello di traccia per la registrazione nel file host.json o disattivarla.  Per altre informazioni su come scrivere nei log, vedere la sezione successiva.

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

Altre opzioni per `dataType` sono `stream` e `string`.

## <a name="writing-trace-output-to-the-console"></a>Scrittura dell'output di traccia nella console 

In Funzioni usare i metodi `context.log` per scrivere l'output di traccia nella console. A questo punto, non è possibile usare `console.log` per scrivere nella console.

Quando si chiama `context.log()`, il messaggio viene scritto nella console a livello di traccia predefinito, ovvero il livello di traccia _info_. Il codice seguente scrive nella console a livello di traccia informazioni:

```javascript
context.log({hello: 'world'});  
```

Il codice precedente equivale al seguente:

```javascript
context.log.info({hello: 'world'});  
```

Il codice seguente scrive nella console a livello di errore:

```javascript
context.log.error("An error has occurred.");  
```

Poiché _error_ è il livello di traccia più alto, questa traccia viene scritta nell'output a tutti i livelli di traccia, fintantoché la registrazione è abilitata.  


Tutti i metodi `context.log` supportano lo stesso formato di parametri supportato dal metodo Node.js [ util.format](https://nodejs.org/api/util.html#util_util_format_format). Si consideri il codice seguente, che scrive nella console usando il livello di traccia predefinito:

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

I valori di **consoleLevel** corrispondono ai nomi dei metodi `context.log`. Per disabilitare tutta la registrazione traccia nella console, impostare **consoleLevel** su _off_. Per altre informazioni sul file host.json, vedere l'[argomento di riferimento host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).

## <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.  

### <a name="request-object"></a>Oggetto della richiesta

Di seguito sono elencate le proprietà dell'oggetto `request`:

| Proprietà      | Descrizione                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Oggetto che contiene il corpo della richiesta.               |
| _headers_     | Oggetto che contiene le intestazioni della richiesta.                   |
| _method_      | Metodo HTTP della richiesta.                                |
| _originalUrl_ | URL della richiesta.                                        |
| _params_      | Oggetto che contiene i parametri di routing della richiesta. |
| _query_       | Oggetto che contiene i parametri di query della richiesta.                  |
| _rawBody_     | Il corpo del messaggio sotto forma di stringa.                           |


### <a name="response-object"></a>Oggetto della risposta

Di seguito sono elencate le proprietà dell'oggetto `response`:

| Proprietà  | Descrizione                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Oggetto che contiene il corpo della risposta.         |
| _headers_ | Oggetto che contiene le intestazioni della risposta.             |
| _isRaw_   | Indica che la formattazione viene ignorata per la risposta.    |
| _Stato_  | Codice di stato HTTP della risposta.                     |

### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta 

Quando si lavora con i trigger HTTP, è possibile accedere agli oggetti richiesta e risposta HTTP in uno qualsiasi dei tre modi seguenti:

+ Dalle associazioni di input e di output denominate. In questo modo, il trigger e le associazioni HTTP funzionano come qualsiasi altra associazione. L'esempio seguente imposta l'oggetto risposta usando un'associazione `response` denominata. 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Dalle proprietà `req` e `res` sull'oggetto `context`. In questo modo per accedere ai dati HTTP dall'oggetto di contesto è possibile usare il modello convenzionale anziché il modello `context.bindings.name` completo. L'esempio seguente illustra come accedere agli oggetti `req` e `res` nell'oggetto `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Oppure chiamando `context.done()`. Un tipo speciale di associazione HTTP restituisce la risposta che viene passata al metodo `context.done()`. L'associazione di output HTTP seguente definisce un parametro di output `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Questa associazione di output prevede che l'utente dia la risposta quando chiama `done()`, come indicato di seguito:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Versione di Node e gestione dei pacchetti
La versione di Node è attualmente bloccata alla `6.5.0`. Si sta analizzando la possibilità di aggiungere il supporto per altre versioni e renderle configurabili.

I passaggi seguenti consentono di includere i pacchetti nell'app per le funzioni: 

1. Passare a `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **Debug Console** > **CMD** (Console di debug > CMD).

3. Passare a `D:\home\site\wwwroot`, quindi trascinare il file package.json nella cartella **wwwroot** nella metà superiore della pagina.  
    È possibile caricare i file nell'app per le funzioni anche in altri modi. Per altre informazioni, vedere [Come aggiornare i file delle app per le funzioni](functions-reference.md#fileupdate). 

4. Dopo aver caricato il file package.json, eseguire il comando `npm install` nella  **console di esecuzione remota Kudu**.  
    Questa azione scarica i pacchetti indicati nel file package.json e riavvia l'app per le funzioni.

Una volta che i pacchetti necessari sono installati, è possibile importarli nella funzione chiamando il metodo `require('packagename')`, come nell'esempio seguente:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

È necessario definire un file `package.json` nella directory principale dell'app per le funzioni. La definizione del file consente a tutte le funzioni dell'app di condividere gli stessi pacchetti memorizzati nella cache garantendo prestazioni migliori. In caso di conflitto di versione, è possibile risolverlo aggiungendo un file `package.json` nella cartella di una funzione specifica.  

## <a name="environment-variables"></a>Variabili di ambiente
Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `process.env`come illustrato nell'esempio di codice seguente:

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
## <a name="considerations-for-javascript-functions"></a>Considerazioni per le funzioni JavaScript

Quando si usano le funzioni JavaScript, tenere presente le considerazioni nelle due sezioni che seguono.

### <a name="choose-single-core-app-service-plans"></a>Scegliere i piani di servizio app single core

Quando si crea un'app per le funzioni che usa il piano di servizio app, è consigliabile selezionare un piano single core anziché uno con più core. Oggi Funzioni esegue funzioni JavaScript in modo più efficiente in macchine virtuali single core e l'uso di macchine virtuali di dimensioni maggiori non produce i miglioramenti delle prestazioni previsti. Se necessario, è possibile scalare orizzontalmente manualmente aggiungendo altre istanze di macchine virtuali single core oppure abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Supporto TypeScript e CoffeeScript
Dal momento che non è ancora disponibile il supporto diretto per la compilazione automatica di TypeScript o CoffeeScript tramite il runtime, il supporto deve essere gestito all'esterno del runtime al momento della distribuzione. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#](functions-reference-fsharp.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)


