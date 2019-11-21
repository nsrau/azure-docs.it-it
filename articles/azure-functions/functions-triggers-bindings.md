---
title: Trigger e associazioni in Funzioni di Azure
description: Learn to use triggers and bindings to connect your Azure Function to online events and cloud-based services.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226554"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In this article you learn the high-level concepts surrounding functions triggers and bindings.

Triggers are what cause a function to run. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function. 

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as *input bindings*, *output bindings*, or both. I dati dei binding vengono forniti alla funzione come parametri.

You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.

Triggers and bindings let you avoid hardcoding access to other services. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Consider the following examples of how you could implement different functions.

| Scenario di esempio | Trigger | Input binding | Output binding |
|-------------|---------|---------------|----------------|
| A new queue message arrives which runs a function to write to another queue. | Queue<sup>*</sup> | *Nessuno* | Queue<sup>*</sup> |
|A scheduled job reads Blob Storage contents and creates a new Cosmos DB document. | Timer | Archiviazione BLOB | Cosmos DB |
|The Event Grid is used to read an image from Blob Storage and a document from Cosmos DB to send an email. | Griglia di eventi | Blob Storage and  Cosmos DB | SendGrid |
| A webhook that uses Microsoft Graph to update an Excel sheet. | http | *Nessuno* | Microsoft Graph |

<sup>\*</sup> Represents different queues

These examples are not meant to be exhaustive, but are provided to illustrate how you can use triggers and bindings together.

###  <a name="trigger-and-binding-definitions"></a>Trigger and binding definitions

Triggers and bindings are defined differently depending on the development approach.

| Piattaforma | Triggers and bindings are configured by... |
|-------------|--------------------------------------------|
| C# class library | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorating methods and parameters with C# attributes |
| All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

The portal provides a UI for this configuration, but you can edit the file directly by opening the **Advanced editor** available via the **Integrate** tab of your function.

In .NET, the parameter type defines the data type for input data. For instance, use `string` to bind to the text of a queue trigger, a byte array to read as binary and a custom type to de-serialize to an object.

Per i linguaggi tipizzati in modo dinamico, ad esempio JavaScript, usare la proprietà `dataType` nel file *function.json*. Ad esempio, per eseguire la lettura del contenuto di una richiesta HTTP in formato binario, impostare `dataType` su `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Altre opzioni per `dataType` sono `stream` e `string`.

## <a name="binding-direction"></a>Direzione dell'associazione

Tutti i trigger e le associazioni hanno una proprietà `direction` nel file [function.json](./functions-reference.md):

- Per i trigger, la direzione è sempre `in`
- Le associazioni di input e di output usano `in` e `out`
- Alcune associazioni supportano una direzione speciale `inout`. If you use `inout`, only the **Advanced editor** is available via the **Integrate** tab in the portal.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="resources"></a>resources
- [Binding expressions and patterns](./functions-bindings-expressions-patterns.md)
- [Using the Azure Function return value](./functions-bindings-return-value.md)
- [How to register a binding expression](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Handling binding errors](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Register Azure Functions binding extensions](./functions-bindings-register.md)
