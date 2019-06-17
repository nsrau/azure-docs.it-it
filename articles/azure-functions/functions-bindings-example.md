---
title: Esempio di trigger e associazione di funzioni Azure
description: Informazioni su come configurare le associazioni di funzioni di Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8372becd614e35bead9e98e405e2694f3f16db4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472321"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Esempio di trigger e associazione di funzioni Azure

Questo articolo viene illustrato come configurare un [trigger e associazioni](./functions-triggers-bindings.md) in una funzione di Azure.

Si supponga di voler scrivere una nuova riga in archiviazione tabelle di Azure ogni volta che viene visualizzato un messaggio nuovo in archiviazione code di Azure. Questo scenario può essere implementato tramite un trigger di archiviazione code di Azure e un'associazione di output di archiviazione tabelle di Azure. 

Ecco un file *function.json* per questo scenario. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Il primo elemento nella matrice `bindings` è il trigger di archiviazione code. Le proprietà `type` e `direction` identificano il trigger. La proprietà `name` identifica il parametro della funzione che riceve il contenuto del messaggio della coda. Il nome della coda da monitorare si trova in `queueName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

Il secondo elemento nella matrice `bindings` è l'associazione di output dell'archiviazione tabelle di Azure. Le proprietà `type` e `direction` identificano l'associazione. La proprietà `name` specifica in che modo la funzione fornisce la nuova riga di tabella, in questo caso usando il valore restituito della funzione. Il nome della tabella si trova in `tableName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

Per visualizzare e modificare i contenuti della *funzione .json* nel portale di Azure, fare clic sull'opzione **Editor avanzato** nella scheda **Integrazione** della funzione.

> [!NOTE]
> Il valore di `connection` corrisponde al nome di un'impostazione app che contiene la stringa di connessione e non alla stringa di connessione stessa. Le associazioni usano stringhe di connessione archiviate nelle impostazioni app per imporre la procedura consigliata che prevede che il file *function.json* non contenga i segreti del servizio.

## <a name="c-script-example"></a>Esempio di script C#

Ecco il codice dello script C# per questo trigger e questa associazione. Si noti che il nome del parametro che fornisce il contenuto del messaggio della coda è `order`. Questo nome è obbligatorio perché il valore della proprietà `name` nel file *function.json* è `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Esempio JavaScript

Lo stesso *Function. JSON* file può essere usato con una funzione JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Esempio di libreria di classi

In una libreria di classi le stesse informazioni sui trigger e sulle associazioni, ovvero i nomi di coda e tabella, gli account di archiviazione, i parametri di funzione per l'input e l'output, vengono fornite dagli attributi anziché da un file function.json. Ad esempio:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

È ora disponibile una funzione lavoro che viene attivata da una coda di Azure e restituisce i dati nell'archiviazione tabelle di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [I modelli di espressione di associazione di funzioni di Azure](./functions-bindings-expressions-patterns.md)
