---
title: Spostamento da Express.js a funzioni di Azure
description: Informazioni su come effettuare il refactoring di Express.js endpoint in funzioni di Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810225"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Spostamento da Express.js a funzioni di Azure

Express.js è uno dei framework di Node.js più diffusi per gli sviluppatori Web e rimane un'ottima scelta per la creazione di app che servono endpoint API.

Quando si esegue la migrazione del codice a un'architettura senza server, il refactoring Express.js endpoint influiscono sulle aree seguenti:

- **Middleware**: Express.js offre una raccolta affidabile di middleware. Molti moduli middleware non sono più necessari con funzioni di Azure e funzionalità di [gestione API di Azure](../api-management/api-management-key-concepts.md) . Prima di eseguire la migrazione degli endpoint, assicurarsi di poter replicare o sostituire qualsiasi logica gestita da middleware essenziale.

- **API diverse**: l'API usata per elaborare le richieste e le risposte differisce tra funzioni di Azure e Express.js. Nell'esempio seguente vengono illustrate in dettaglio le modifiche necessarie.

- **Route predefinita**: per impostazione predefinita, gli endpoint di funzioni di Azure vengono esposti sotto la `api` Route. Le regole di routing sono configurabili tramite [ `routePrefix` nel _host.jssu_ file](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Configurazione e convenzioni**: un'app per le funzioni usa il _function.jssu_ file per definire i verbi HTTP, definire i criteri di sicurezza e può configurare l' [input e l'output](./functions-triggers-bindings.md)della funzione. Per impostazione predefinita, il nome della cartella che contiene i file di funzione definisce il nome dell'endpoint, ma è possibile modificare il nome tramite la `route` proprietà nell' [function.jssu](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) file.

> [!TIP]
> Per altre informazioni, vedere l'esercitazione interattiva [refactoring Node.js ed Express API per le API senza server con funzioni di Azure](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Esempio

### <a name="expressjs"></a>Express.js

Nell'esempio seguente viene illustrato un tipico `GET` endpoint Express.js.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Quando viene `GET` inviata una richiesta a `/hello` , `HTTP 200` `Success` viene restituita una risposta contenente. Se l'endpoint rileva un errore, la risposta è un oggetto `HTTP 500` con i dettagli dell'errore.

### <a name="azure-functions"></a>Funzioni di Azure

Funzioni di Azure organizza i file di configurazione e di codice in un'unica cartella per ogni funzione. Per impostazione predefinita, il nome della cartella determina il nome della funzione.

Ad esempio, una funzione denominata `hello` ha una cartella con i file seguenti.

``` files
| - hello
|  - function.json
|  - index.js
```

Nell'esempio seguente viene implementato lo stesso risultato dell'endpoint Express.js, ma con funzioni di Azure.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Quando si esegue il passaggio alle funzioni, vengono apportate le modifiche seguenti:

- **Modulo:** Il codice della funzione viene implementato come un modulo JavaScript.

- **Contesto e oggetto Response**: [`context`](./functions-reference-node.md#context-object) consente di comunicare con il runtime della funzione. Dal contesto è possibile leggere i dati della richiesta e impostare la risposta della funzione. Il codice sincrono richiede `context.done()` la chiamata per completare l'esecuzione, mentre `asyc` le funzioni risolvono in modo implicito la richiesta.

- **Convenzione di denominazione**: il nome della cartella usata per contenere i file di funzioni di Azure viene usato come nome dell'endpoint per impostazione predefinita (è possibile eseguirne l'override nella [function.jsin](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Configurazione**: i verbi HTTP vengono definiti nella [function.jssu](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) file, ad esempio `POST` o `PUT` .

Il _function.jsseguente nel_ file include informazioni di configurazione per la funzione.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Definendo `get` nella `methods` matrice, la funzione è disponibile per le richieste HTTP `GET` . Se si vuole che l'API accetti richieste di supporto `POST` , è possibile aggiungere `post` anche all'array.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più con l'esercitazione interattiva [refactoring Node.js ed Express API per le API senza server con funzioni di Azure](/learn/modules/shift-nodejs-express-apis-serverless/)