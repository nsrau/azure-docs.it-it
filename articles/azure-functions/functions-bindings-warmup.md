---
title: Trigger di riscaldamento di funzioni di Azure
description: Informazioni su come usare il trigger di riscaldamento in funzioni di Azure.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funzioni di Azure, funzioni, elaborazione di eventi, riscaldamento, avvio a freddo, Premium, calcolo dinamico, architettura senza server
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167323"
---
# <a name="azure-functions-warm-up-trigger"></a>Trigger di riscaldamento di funzioni di Azure

Questo articolo illustra come usare il trigger di riscaldamento in funzioni di Azure. Il trigger riscaldamento è supportato solo per le app per le funzioni in esecuzione in un [piano Premium](functions-premium-plan.md). Quando viene aggiunta un'istanza per ridimensionare un'app per le funzioni in esecuzione, viene richiamato un trigger di riscaldamento. È possibile usare un trigger di riscaldamento per precaricare le dipendenze personalizzate durante il [processo di pre-riscaldamento](./functions-premium-plan.md#pre-warmed-instances) , in modo che le funzioni siano pronte per l'avvio immediato dell'elaborazione delle richieste. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages-Functions 2. x e versioni successive

È necessario il pacchetto NuGet [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , versione **3.0.5 o successiva** . Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Il trigger riscaldamento consente di definire una funzione che verrà eseguita in una nuova istanza quando viene aggiunta all'app in esecuzione. È possibile usare una funzione di riscaldamento per aprire le connessioni, caricare le dipendenze o eseguire qualsiasi altra logica personalizzata prima che l'app inizi a ricevere il traffico. 

Il trigger riscaldamento è progettato per creare dipendenze condivise che verranno usate da altre funzioni nell'app. [Vedere esempi di dipendenze condivise qui](./manage-connections.md#client-code-examples).

Si noti che il trigger di riscaldamento viene chiamato solo durante le operazioni di scalabilità orizzontale, non durante i riavvii o altre startup senza scalabilità. È necessario assicurarsi che la logica possa caricare tutte le dipendenze necessarie senza usare il trigger di riscaldamento. Il caricamento lazy è un modello valido per ottenere questo risultato.

## <a name="trigger---example"></a>Trigger - esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che verrà eseguita in ogni nuova istanza quando viene aggiunta all'app. Un attributo del valore restituito non è obbligatorio.


* La funzione deve essere denominata ```warmup``` (senza distinzione tra maiuscole e minuscole) ed è possibile che esista una sola funzione di riscaldamento per ogni app.
* Per usare riscaldamento come funzione della libreria di classi .NET, assicurarsi di disporre di un riferimento a un pacchetto per **Microsoft. Azure. webjobs. Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


I commenti segnaposto indicano dove nell'applicazione dichiarare e inizializzare le dipendenze condivise. 
[Altre informazioni sulle dipendenze condivise](./manage-connections.md#client-code-examples)sono disponibili qui.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Script C#](#tab/csharp-script)


L'esempio seguente mostra un trigger di riscaldamento in un file *Function. JSON* e una [funzione script C#](functions-reference-csharp.md) che verrà eseguita in ogni nuova istanza quando viene aggiunta all'app.

La funzione deve essere denominata ```warmup``` (senza distinzione tra maiuscole e minuscole) ed è possibile che esista una sola funzione di riscaldamento per ogni app.

Ecco il file *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice script C# associato a un oggetto `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un trigger di riscaldamento in un file *Function. JSON* e una [funzione JavaScript](functions-reference-node.md) che verrà eseguita in ogni nuova istanza quando viene aggiunta all'app.

La funzione deve essere denominata ```warmup``` (senza distinzione tra maiuscole e minuscole) ed è possibile che esista una sola funzione di riscaldamento per ogni app.

Ecco il file *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un trigger di riscaldamento in un file *Function. JSON* e una [funzione Python](functions-reference-python.md) che verrà eseguita in ogni nuova istanza quando viene aggiunta all'app.

La funzione deve essere denominata ```warmup``` (senza distinzione tra maiuscole e minuscole) ed è possibile che esista una sola funzione di riscaldamento per ogni app.

Ecco il file *function.json*:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

L'esempio seguente mostra un trigger di riscaldamento che viene eseguito quando ogni nuova istanza viene aggiunta all'app.

La funzione deve essere denominata `warmup` (senza distinzione tra maiuscole e minuscole) ed è possibile che esista una sola funzione di riscaldamento per ogni app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md), `WarmupTrigger` l'attributo è disponibile per configurare la funzione.

# <a name="c"></a>[C#](#tab/csharp)

Questo esempio illustra come usare l'attributo [riscaldamento](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Si noti che la funzione deve essere ```Warmup``` chiamata e che può essere presente una sola funzione di riscaldamento per app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Per un esempio completo, vedere l' [esempio di trigger](#trigger---example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Il trigger di riscaldamento non è supportato in Java come attributo.

---

## <a name="trigger---configuration"></a>Trigger - configurazione

La tabella seguente illustra le proprietà di configurazione dell'associazione impostate nel file *Function. JSON* e nell' `WarmupTrigger` attributo.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
| **type** | n/d| Obbligatoria. Deve essere impostata su `warmupTrigger`. |
| **direzione** | n/d| Obbligatoria. Deve essere impostata su `in`. |
| **name** | n/d| Obbligatoria: nome della variabile usato nel codice della funzione.|

## <a name="trigger---usage"></a>Trigger - uso

Non viene fornita alcuna informazione aggiuntiva a una funzione attivata da riscaldamento quando viene richiamata.

## <a name="trigger---limits"></a>Trigger - Limiti

* Il trigger riscaldamento è disponibile solo per le app in esecuzione nel [piano Premium](./functions-premium-plan.md).
* Il trigger di riscaldamento viene chiamato solo durante le operazioni di scalabilità verticale, non durante i riavvii o altre startup senza scalabilità. È necessario assicurarsi che la logica possa caricare tutte le dipendenze necessarie senza usare il trigger di riscaldamento. Il caricamento lazy è un modello valido per ottenere questo risultato.
* Non è possibile richiamare il trigger riscaldamento una volta che un'istanza è già in esecuzione.
* Per ogni app per le funzioni può essere presente una sola funzione di trigger di riscaldamento.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
