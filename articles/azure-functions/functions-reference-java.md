---
title: Guida di riferimento per gli sviluppatori Java per Funzioni di Azure | Microsoft Docs
description: Informazioni sullo sviluppo di funzioni con Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 9e07cddb9d446ea24143d3a6dec5e310d3ed6f1c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802118"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Modello di programmazione 

Una funzione di Azure deve essere un metodo della classe senza stato che elabora un input e restituisce un output. Anche se è possibile scrivere metodi di istanza, la funzione non deve dipendere da campi di istanza della classe. Tutti i metodi di funzione devono avere un modificatore di accesso `public`.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle per un progetto Java ha un aspetto simile al seguente:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

È presente un file [host.json] (functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. L'elemento FunctionApp nella directory di destinazione viene distribuito all'app per le funzioni in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni di Azure vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento dati. La funzione deve elaborare tale trigger e qualsiasi altro input per generare uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Il codice di esempio con le annotazioni è disponibile nei [documenti di riferimento Java](/java/api/com.microsoft.azure.functions.annotation) per ciascuna annotazione e nella documentazione di riferimento per l'associazione di Funzioni di Azure, ad esempio quella per i [trigger HTTP](/azure/azure-functions/functions-bindings-http-webhook).

I trigger di input e output possono essere definiti anche nel file [function.json](/azure/azure-functions/functions-reference#function-code) per la funzione, anziché tramite le annotazioni. Non è consigliabile usare `function.json` anziché le annotazioni in questo modo.

> [!IMPORTANT] 
> È necessario configurare un account di Archiviazione di Azure nel file [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire i trigger di BLOB del servizio di archiviazione di Azure o di code e tabelle di Azure.

Esempio di uso di annotazioni:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="jdk-runtime-availability-and-support"></a>Disponibilità del runtime JDK e supporto 

Eseguire il download e usare i pacchetti JDK [Azul Zulu per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) di [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) per lo sviluppo locale di app per le funzioni Java. I pacchetti JDK sono disponibili per Windows, Linux e macOS e il [supporto di Azure](https://support.microsoft.com/en-us/help/4026305/sql-contact-microsoft-azure-support) è disponibile per risolvere i problemi riscontrati durante lo sviluppo con un [piano di supporto completo](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte le dipendenze specificate nel file `pom.xml` del progetto verranno raggruppate automaticamente durante l'obiettivo `mvn package`. Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Le dipendenze inserite nella directory `lib` verranno aggiunte al caricatore di classe di sistema in fase di esecuzione.

La dipendenza `com.microsoft.azure.functions:azure-functions-java-library` è specificata in classpath per impostazione predefinita e non deve essere inclusa nella directory `lib`.

## <a name="data-type-support"></a>Supporto dei tipi di dati

Come dati di input e output è possibile usare tutti i tipi di dati in Java, inclusi i tipi nativi, i tipi Java personalizzati e i tipi specializzati di Azure definiti nel pacchetto `azure-functions-java-library`. Il runtime di Funzioni di Azure tenta di convertire l'input ricevuto nel tipo richiesto dal codice.

### <a name="strings"></a>Stringhe

Per i valori passati nei metodi di funzione sarà possibile eseguire il cast in stringhe se il tipo di parametro di input corrispondente per la funzione è `String`. 

### <a name="plain-old-java-objects-pojos"></a>Oggetti Java precedenti normali (POJO, Plain Old Java Object)

Per le stringhe in formato JSON verrà eseguito il cast in tipi Java se la firma di input della funzione prevede tale tipo. Questa conversione consente di passare al formato JSON e usare tipi Java.

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

I valori di input vuoti potrebbero essere `null` come l'argomento delle funzioni, ma per gestire gli eventuali valori vuoti è consigliabile usare `Optional<T>`.


## <a name="function-method-overloading"></a>Overload di metodi di funzioni

È consentito eseguire l'overload di metodi di funzione con lo stesso nome, ma con tipi diversi. È ad esempio possibile che in una classe siano presenti sia `String echo(String s)` sia `String echo(MyType s)`. Funzioni di Azure decide quale metodo richiamare in base al tipo di input (per un input HTTP il tipo MIME `text/plain` determina `String`, mentre `application/json` rappresenta `MyType`).

## <a name="inputs"></a>Input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono diversi in `function.json`, l'uso nel codice Java è diverso. Si consideri il frammento di codice di esempio seguente:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Quando viene attivata questa funzione, la richiesta HTTP viene passata alla funzione da `String in`. Una voce verrà recuperata dall'archiviazione tabelle di Azure in base all'ID nell'URL di route e verrà resa disponibile come `obj` nel corpo della funzione.

## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Il valore restituito è la forma più semplice di output, perché è possibile restituire solo un valore di qualsiasi tipo e il runtime di Funzioni di Azure runtime tenterà di eseguire il marshalling nel tipo effettivo, ad esempio una risposta HTTP.  Per definire l'output del valore restituito, è possibile applicare qualsiasi annotazione di output al metodo di funzione. La proprietà del nome dell'annotazione deve essere $return.

Per generare più valori di output, usare il tipo `OutputBinding<T>` definito nel pacchetto `azure-functions-java-library`. Se è necessario restituire una risposta HTTP ed eseguire anche il push di un messaggio a una coda, è possibile scrivere un codice simile al seguente:

La funzione di copia di un contenuto BLOB può essere ad esempio definita come il codice seguente. L'annotazione `@StorageAccount` è qui usata per evitare la duplicazione della proprietà di connessione per `@BlobTrigger` e `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Usare `OutputBinding<byte[]`> per creare un valore di output binario (per i parametri). Per i valori restituiti usare `byte[]`.

## <a name="specialized-types"></a>Tipi specializzati

Talvolta una funzione deve disporre di controllo dettagliato sui dati di input e output. I tipi specializzati nel pacchetto `azure-functions-java-core` sono disponibili per modificare le informazioni sulla richiesta e per personalizzare lo stato restituito di un trigger HTTP:

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Metodo Get, intestazioni o query |
| `HttpResponseMessage<T>` | Associazione di output HTTP | Stato restituito diverso da 200   |

> [!NOTE] 
> È anche possibile usare l'annotazione `@BindingName` per ottenere le intestazioni e le query HTTP. `@BindingName("name") String query` consente ad esempio di scorrere le intestazioni delle richieste e le query HTTP e di passare tale valore al metodo. `query` sarà `"test"`, ad esempio, se l'URL richiesto è `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

I metadati provengono da origini diverse, ad esempio le intestazioni HTTP, le query HTTP e i [metadati di trigger](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Usare l'annotazione `@BindingName` insieme al nome dei metadati per ottenere il valore.

Ad esempio, il `queryValue` nel frammento di codice seguente è `"test"` se l'URL richiesto è `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>Contesto di esecuzione

Per interagire con l'ambiente di esecuzione di Funzioni di Azure, usare l'oggetto `ExecutionContext` definito nel pacchetto `azure-functions-java-library`. L'oggetto `ExecutionContext` consente di usare le informazioni di chiamata e quelle del runtime delle funzioni nel codice.

### <a name="custom-logging"></a>Registrazione personalizzata

Per accedere al logger di runtime di Funzioni di Azure, è possibile usare l'oggetto `ExecutionContext`. Il logger è associato a Monitoraggio di Azure e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione delle funzioni.

L'esempio di codice seguente registra un messaggio di avviso quando il corpo della richiesta ricevuto è vuoto.

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visualizzazione dei log e della traccia

È possibile usare l'interfaccia della riga di comando di Azure per eseguire lo streaming dello standard Java e della registrazione degli errori, nonché la registrazione di altre applicazioni. Prima di tutto configurare l'applicazione di funzione per scrivere la registrazione delle applicazioni tramite l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Per eseguire lo streaming dell'output dell'applicazione di funzione usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione terminal o Bash o terminal e immettere il comando seguente:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Il comando [az webapp log tail](/cli/azure/webapp/log) ha opzioni per filtrare l'output usando l'opzione `--provider`. 

Per scaricare i file di log come un singolo file ZIP usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione terminal o Bash e immettere il comando seguente:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Prima di eseguire questo comando, è necessario aver abilitato la registrazione del file system nel portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="environment-variables"></a>Variabili di ambiente

Per motivi di sicurezza, è consigliabile evitare di includere nel codice sorgente informazioni segrete come chiavi o token. Usare le chiavi e i token nel codice della funzione leggendoli dalle variabili di ambiente.

Per impostare le variabili di ambiente quando si esegue Funzioni di Azure in locale, è possibile aggiungerle al file local.settings.json. Se una di esse non è presente nella directory radice del progetto funzione, è possibile crearla. Il file sarà simile al seguente:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Ogni chiave/valore di mapping nel mapping `values` verrà reso disponibile in fase di esecuzione come variabile di ambiente, accessibile tramite una chiamata `System.getenv("<keyname>")`, ad esempio `System.getenv("AzureWebJobsStorage")`. L'aggiunta di coppie di chiavi/valori aggiuntive non solo è permessa, ma è anche auspicabile.

> [!NOTE]
> Se si adotta questo approccio, assicurarsi di aggiungere il file local.settings.json al comando Ignora file del repository, in modo che non ne venga eseguito il commit.

Con il codice ora basato su queste variabili di ambiente è possibile accedere al portale di Azure per impostare le stesse coppie di chiave/valore nelle impostazioni dell'app per le funzioni, in modo che il codice funzioni allo stesso modo nel test in locale e nella distribuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Java delle funzioni di Azure, vedere le risorse seguenti:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
- Sviluppo locale ed esecuzione del debug con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) ed [Eclipse](functions-create-maven-eclipse.md). 
* [Eseguire il debug remoto di funzioni di Azure in Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per Funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md): semplifica la creazione della funzione tramite l'obiettivo `azure-functions:add` e prepara una directory di gestione [per la distribuzione tramite file ZIP](deployment-zip-push.md).
