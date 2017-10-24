---
title: Guida di riferimento per gli sviluppatori Java per Funzioni di Azure | Microsoft Docs
description: Informazioni sullo sviluppo di funzioni con Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/20/2017
ms.author: routlaw
ms.openlocfilehash: f8812c2e8ac3398dabd17feaf97897efca5566f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>Modello di programmazione 

Una funzione di Azure deve essere un metodo della classe senza stato che elabora un input e restituisce un output. Sebbene sia possibile scrivere metodi di istanza, la funzione non deve dipendere da alcun campi di istanza della classe. Tutti i metodi di funzione devono avere un modificatore di accesso `public`.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

In genere una funzione di Azure viene richiamata a causa di un trigger esterno. La funzione deve elaborare tale trigger e gli input associati e restituire uno o più output.

Le annotazioni Java sono incluse nel pacchetto `azure-functions-java-core` per associare input e output ai metodi dell'utente. I trigger di input e le annotazioni di associazione di output supportati vengono inclusi nella tabella seguente:

Associazione | Annotazione
---|---
Cosmos DB | N/D
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
App per dispositivi mobili | N/D
Hub di notifica | N/D
Archiviazione BLOB | <ul><li>`BlobTrigger`</li><li>`BlobOutput`</li><li>`BlobOutput`</li></ul>
Coda di archiviazione | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabella di archiviazione | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Timer | <ul><li>`TimerTrigger`</li></ul>
Twilio | N/D

I trigger di input e output possono essere definiti anche nel file [function.json](/azure/azure-functions/functions-reference#function-code) per l'applicazione.

> [!IMPORTANT] 
> È necessario configurare un account di Archiviazione di Azure nel file [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire i trigger di BLOB del servizio di archiviazione di Azure o di code e tabelle di Azure.

Esempio di uso di annotazioni:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

La stessa funzione scritta senza annotazioni:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

con i corrispondenti `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>Tipi di dati

Come dati di input e output, è possibile usare tutti i tipi di dati Java, inclusi i tipi nativi, i tipi Java personalizzati e i tipi Azure speciali definiti nel pacchetto `azure-functions-java-core`. Il runtime di Funzioni di Azure tenta di convertire l'input ricevuto nel tipo richiesto dal codice.

### <a name="strings"></a>Stringhe

Per i valori passati nei metodi di funzione sarà possibile eseguire il cast in stringhe se il tipo di parametro di input corrispondente per la funzione è `String`. 

### <a name="plain-old-java-objects-pojos"></a>Oggetti Java precedenti normali (POJO, Plain Old Java Object)

Per le stringhe in formato JSON verrà eseguito il cast in tipi Java se l'input del metodo di funzione prevede tale tipo. Questa conversione consente di passare input JSON alle funzioni e di usare i tipi Java nel codice senza dover implementare la conversione nel codice.

I tipi POJO usati come input per le funzioni devono usare lo stesso modificatore di accesso `public` come i metodi di funzione in cui vengono usati. Non è necessario dichiarare i campi `public` delle classi POJO. Una stringa JSON `{ "x": 3 }`, ad esempio, può essere convertito nel tipo POJO seguente:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Dati binari

I dati binari viene rappresentati come un elemento `byte[]` nel codice di Funzioni di Azure. Per associare dati di input/output binari alle funzioni, impostare il campo `dataType` nel file function.json su `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Usare quindi il campo nel codice:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Usare il tipo `OutputBinding<byte[]>` per eseguire un'associazione di output binario.


## <a name="function-method-overloading"></a>Overload di metodi di funzioni

È consentito eseguire l'overload di metodi di funzione con lo stesso nome, ma con tipi diversi. È possibile ad esempio che in una classe siano presenti sia `String echo(String s)` che `String echo(MyType s)` e che il runtime di Funzioni di Azure decida quale richiamare esaminando il tipo di input effettivo (per un input HTTP il tipo MIME `text/plain` determina `String`, mentre `application/json` rappresenta `MyType`).

## <a name="inputs"></a>Input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono diversi in `function.json`, l'uso nel codice Java è diverso. Si consideri il frammento di codice di esempio seguente:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String in, @BindingName("item") MyObject obj) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

L'annotazione `@BindingName` accetta una proprietà `String` che rappresenta il nome del trigger o dell'associazione definito in `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Di conseguenza, quando questa funzione viene richiamata il payload della richiesta HTTP passa un elemento `String` per l'argomento `in` e un tipo `MyObject` dell'archiviazione tabelle di Azure passato all'argomento `obj`.

## <a name="outputs"></a>outputs

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Il valore restituito è la forma più semplice di output, perché è possibile restituire solo un valore di qualsiasi tipo e il runtime di Funzioni di Azure runtime tenterà di eseguire il marshalling nel tipo effettivo, ad esempio una risposta HTTP. In `functions.json` usare `$return` come nome dell'associazione di output.

Per generare più valori di output, usare il tipo `OutputParameter<T>` definito nel pacchetto `azure-functions-java-core`. Se è necessario restituire una risposta HTTP ed eseguire anche il push di un messaggio a una coda, è possibile scrivere un codice simile al seguente:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputParameter;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, @BindingName("message") OutputParameter<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

e definire l'associazione di output in `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "myqueue",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Tipi specializzati

Talvolta una funzione deve disporre di controllo dettagliato sui dati di input e output. I tipi specializzati nel pacchetto `azure-functions-java-core` sono disponibili al fine di modificare le informazioni sulla richiesta e di personalizzare lo stato restituito di un trigger HTTP:

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage`  |    Trigger HTTP     | Metodo Get, intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Stato restituito diverso da 200   |

> [!NOTE] 
> È anche possibile usare l'annotazione `@BindingName` per ottenere le intestazioni e le query HTTP. `@Bind("name") String query` consente ad esempio di scorrere le intestazioni delle richieste e le query HTTP e di passare tale valore al metodo. `query` sarà `"test"`, ad esempio, se l'URL richiesto è `http://example.org/api/echo?name=test`.

## <a name="functions-execution-context"></a>Contesto di esecuzione di Funzioni di Azure

Per interagire con l'ambiente di esecuzione di Funzioni di Azure, usare l'oggetto `ExecutionContext` definito nel pacchetto `azure-functions-java-core`. L'oggetto `ExecutionContext` consente di usare le informazioni di chiamata e quelle del runtime delle funzioni nel codice.

### <a name="logging"></a>Registrazione

Per accedere al logger di runtime di Funzioni di Azure, è possibile usare l'oggetto `ExecutionContext`. Il logger è associato a Monitoraggio di Azure e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione delle funzioni.

L'esempio di codice seguente registra un messaggio di avviso quando il corpo della richiesta ricevuto è vuoto.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received in " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
