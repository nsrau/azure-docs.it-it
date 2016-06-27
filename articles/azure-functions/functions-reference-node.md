<properties
	pageTitle="Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come sviluppare Funzioni di Azure in NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)

L'esperienza Node/JavaScript per Funzioni di Azure semplifica l'esportazione di una funzione a cui viene passato un oggetto `context` per la comunicazione con il runtime e per la ricezione e l'invio di dati tramite associazioni.

Questo articolo presuppone che l'utente abbia già letto [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md).

## Esportazione di una funzione

Tutte le funzioni JavaScript devono esportare una singola `function` tramite `module.exports` per consentire al runtime di trovare la funzione ed eseguirla. Questa funzione deve sempre includere un oggetto `context`.

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

Le associazioni di `direction === "in"` vengono passate come argomenti della funzione, ovvero è possibile usare [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) per gestire in modo dinamico nuovi input, ad esempio il metodo `arguments.length` per l'iterazione di tutti gli input. Questa funzionalità è molto utile se si ha un solo trigger senza input aggiuntivi, perché è possibile accedere ai dati del trigger in modo prevedibile senza fare riferimento all'oggetto `context`.

Gli argomenti vengono sempre passati insieme alla funzione nell'ordine in cui indicati nel file *function.json*, anche se non vengono specificati nell'istruzione exports. Ad esempio, se si ha `function(context, a, b)` e viene modificato in `function(context, a)`, è comunque possibile ottenere il valore di `b` nel codice della funzione facendo riferimento a `arguments[3]`.

Tutte le associazioni, indipendentemente dalla direzione, vengono passate anche sull'oggetto `context` (vedere di seguito).

## Oggetto context

Il runtime usa un oggetto `context` per passare dati dalla e alla funzione e consentire la comunicazione con il runtime.

L'oggetto context è sempre il primo parametro di una funzione e deve sempre essere incluso, perché contiene i metodi, ad esempio `context.done` e `context.log`, necessari per usare correttamente il runtime. È possibile assegnare all'oggetto il nome desiderato, ad esempio `ctx` o `c`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

L'oggetto `context.bindings` raccoglie tutti i dati di input e output. I dati vengono aggiunti all'oggetto `context.bindings` tramite la proprietà `name` dell'associazione. Ad esempio, data la seguente definizione di associazione in *function.json*, è possibile accedere al contenuto della coda tramite `context.bindings.myInput`.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

La funzione `context.done` comunica al runtime che l'esecuzione è stata completata. È importante eseguire questa chiamata una volta completata la funzione. In caso contrario, il runtime non saprà mai che la funzione è stata completata.

La funzione `context.done` consente di passare di nuovo al runtime un errore definito dall'utente, nonché un contenitore delle proprietà con proprietà che sovrascriveranno quelle nell'oggetto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(messaggio)

Il metodo `context.log` consente di generare istruzioni log correlate tra loro a scopo di registrazione. Se si usa `console.log`, i messaggi saranno visualizzati solo per la registrazione a livello di processo, che non è particolarmente utile.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

Il metodo `context.log` supporta lo stesso formato di parametri supportato dal [metodo util.format](https://nodejs.org/api/util.html#util_util_format_format) in Node. Ad esempio, un codice simile al seguente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

può essere scritto così:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## Trigger HTTP: context.req e context.res

Nel caso di trigger HTTP, essendo questo un modello comune per l'uso di `req` e `res` per gli oggetti di richiesta e risposta HTTP, è stato deciso di rendere più semplice l'accessp a quelli contenuti nell'oggetto context, invece di dover usare il modello `context.bindings.name` completo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Versione di Node e gestione dei pacchetti

La versione di Node è attualmente bloccata alla `5.9.1`. Si sta analizzando la possibilità di aggiungere il supporto per altre versioni e renderle configurabili.

È possibile includere pacchetti nella funzione caricando un file *package.json* nella cartella della funzione nel file system dell'app per le funzioni. Per istruzioni sul caricamento di file, vedere la sezione **Come aggiornare i file dell'app per le funzioni** dell'argomento [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md#fileupdate).

È anche possibile usare `npm install` nell'interfaccia della riga di comando SCM (Kudu) dell'app per le funzioni:

1. Accedere a `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **Debug Console (Console debug) > CMD**.

3. Accedere a `D:\home\site\wwwroot<function_name>`.

4. Eseguire `npm install`.

Una volta che i pacchetti necessari sono installati è possibile importarli nella funzione con i metodi normali (ad esempio con `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Variabili di ambiente

Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `process.env` come illustrato nell'esempio di codice seguente:

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

## Supporto di TypeScript/CoffeeScript

Non è ancora disponibile il supporto diretto per la compilazione automatica di TypeScript/CoffeeScript tramite il runtime, quindi queste operazioni devono essere tutte gestite all'esterno del runtime al momento della distribuzione.

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0615_2016-->