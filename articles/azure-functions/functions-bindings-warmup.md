---
title: Trigger di riscaldamento di Funzioni di AzureAzure Functions warmup trigger
description: Informazioni su come usare il trigger di riscaldamento in Funzioni di Azure.Understand how to use the warmup trigger in Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funzioni, funzioni, elaborazione di eventi, riscaldamento, avvio a freddo, premium, calcolo dinamico, architettura senza server
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
# <a name="azure-functions-warm-up-trigger"></a>Trigger di riscaldamento Funzioni di AzureAzure Functions warm-up trigger

Questo articolo illustra come usare il trigger di riscaldamento in Funzioni di Azure.This article explains how to work with the warmup trigger in Azure Functions. Il trigger di riscaldamento è supportato solo per le app per le funzioni in esecuzione in un [piano Premium](functions-premium-plan.md). Un trigger di riscaldamento viene richiamato quando viene aggiunta un'istanza per ridimensionare un'app per le funzioni in esecuzione. È possibile usare un trigger di riscaldamento per precaricare le dipendenze personalizzate durante il processo di [preriscaldamento](./functions-premium-plan.md#pre-warmed-instances) in modo che le funzioni siano pronte per avviare immediatamente l'elaborazione delle richieste. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacchetti - Funzioni 2.x e successive

È necessario il pacchetto [NuGet Microsoft.Azure.WebJobs.Extensions,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) versione **3.0.5 o successiva.** Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Il trigger di riscaldamento consente di definire una funzione che verrà eseguita in una nuova istanza quando viene aggiunta all'app in esecuzione. Puoi usare una funzione di riscaldamento per aprire connessioni, caricare dipendenze o eseguire qualsiasi altra logica personalizzata prima che l'app inizi a ricevere traffico. 

Il trigger di riscaldamento ha lo scopo di creare dipendenze condivise che verranno usate dalle altre funzioni dell'app. [Vedere esempi di dipendenze condivise qui](./manage-connections.md#client-code-examples).

Si noti che il trigger di riscaldamento viene chiamato solo durante le operazioni di scalabilità orizzontale, non durante i riavvii o altri avvii non scalabili. È necessario assicurarsi che la logica possa caricare tutte le dipendenze necessarie senza usare il trigger di riscaldamento. Il caricamento lazy è un buon modello per raggiungere questo obiettivo.

## <a name="trigger---example"></a>Trigger - esempio

# <a name="c"></a>[C #](#tab/csharp)

Nell'esempio seguente viene illustrata una [funzione di C,](functions-dotnet-class-library.md) che verrà eseguita in ogni nuova istanza quando viene aggiunta all'app. Un attributo di valore restituito non è obbligatorio.


* La funzione deve ```warmup``` essere denominata (senza distinzione tra maiuscole e minuscole) e può essere presente una sola funzione di riscaldamento per ogni app.
* Per usare il riscaldamento come funzione della libreria di classi .NET, assicurarsi di disporre di un riferimento al pacchetto per **Microsoft.Azure.WebJobs.Extensions >3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


I commenti segnaposto mostrano dove nell'applicazione dichiarare e inizializzare le dipendenze condivise. 
[Ulteriori informazioni sulle dipendenze condivise sono disponibili qui](./manage-connections.md#client-code-examples).

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


L'esempio seguente mostra un trigger di riscaldamento in un file *function.json* e una funzione di [script di C,](functions-reference-csharp.md) che verrà eseguito in ogni nuova istanza quando viene aggiunta all'app.

La funzione deve ```warmup``` essere denominata (senza distinzione tra maiuscole e minuscole) e può essere presente una sola funzione di riscaldamento per ogni app.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente mostra un trigger di riscaldamento in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che verrà eseguito in ogni nuova istanza quando viene aggiunto all'app.

La funzione deve ```warmup``` essere denominata (senza distinzione tra maiuscole e minuscole) e può essere presente una sola funzione di riscaldamento per ogni app.

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

L'esempio seguente mostra un trigger di riscaldamento in un file *function.json* e una [funzione Python](functions-reference-python.md) che verrà eseguito in ogni nuova istanza quando viene aggiunto all'app.

La funzione deve ```warmup``` essere denominata (senza distinzione tra maiuscole e minuscole) e può essere presente una sola funzione di riscaldamento per ogni app.

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

La funzione deve `warmup` essere denominata (senza distinzione tra maiuscole e minuscole) e può essere presente una sola funzione di riscaldamento per ogni app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi di C,](functions-dotnet-class-library.md)l'attributo `WarmupTrigger` è disponibile per configurare la funzione.

# <a name="c"></a>[C #](#tab/csharp)

In questo esempio viene illustrato come utilizzare l'attributo [warmup.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Si noti che ```Warmup``` la funzione deve essere chiamata e può essere presente una sola funzione di riscaldamento per ogni app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Per un esempio completo, vedere [l'esempio](#trigger---example)di trigger .

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Il trigger di riscaldamento non è supportato in Java come attributo.

---

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `WarmupTrigger` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
| **type** | n/d| Obbligatoria. Deve essere impostata su `warmupTrigger`. |
| **direction** | n/d| Obbligatoria. Deve essere impostata su `in`. |
| **name** | n/d| Obbligatorio: il nome della variabile utilizzata nel codice della funzione.|

## <a name="trigger---usage"></a>Trigger - uso

Non vengono fornite informazioni aggiuntive a una funzione attivata di riscaldamento quando viene richiamata.

## <a name="trigger---limits"></a>Trigger - Limiti

* Il trigger di riscaldamento è disponibile solo per le app in esecuzione nel [piano Premium.](./functions-premium-plan.md)
* Il trigger di riscaldamento viene chiamato solo durante le operazioni di scalabilità verticale, non durante i riavvii o altri avvii non scalabili. È necessario assicurarsi che la logica possa caricare tutte le dipendenze necessarie senza usare il trigger di riscaldamento. Il caricamento lazy è un buon modello per raggiungere questo obiettivo.
* Il trigger di riscaldamento non può essere richiamato una volta che un'istanza è già in esecuzione.
* Può essere presente una sola funzione di trigger di riscaldamento per ogni app per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
