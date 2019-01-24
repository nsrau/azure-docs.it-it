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
ms.openlocfilehash: 429c7c266357b4808ab3ebbb7f346cf22d9f479c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855391"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Modello di programmazione 

I concetti di [trigger e binding](functions-triggers-bindings.md) sono fondamentali in Funzioni di Azure. I trigger avviano l'esecuzione del codice, mentre i binding consentono la trasmissione dei dati e la restituzione dei dati da una funzione, senza dover scrivere codice personalizzato per l'accesso ai dati.

Una funzione deve essere un metodo senza stato per l'elaborazione di un input e la produzione di un output. La funzione non deve dipendere dai campi di istanza della classe. Tutti i metodi di funzione devono essere `public` e il metodo con annotazione @FunctionName deve essere univoco poiché il nome del metodo definisce la voce di una funzione.

## <a name="folder-structure"></a>Struttura di cartelle

Di seguito è illustrata la struttura di cartelle di un progetto Java di Funzioni di Azure:

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

È presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. L'elemento FunctionApp nella directory di destinazione viene distribuito all'app per le funzioni in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni di Azure vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento dati. La funzione deve elaborare tale trigger e qualsiasi altro input per generare uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Per altre informazioni, vedere [Documentazione di riferimento per Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> È necessario configurare un account di Archiviazione di Azure nel file [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire i trigger di BLOB del servizio di archiviazione di Azure o di code e tabelle di Azure.

Esempio:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

e il file `function.json` corrispondente generato da [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
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

Eseguire il download e usare i pacchetti Java 8 JDK [Azul Zulu Enterprise per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) di [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) per lo sviluppo locale di app per le funzioni Java. Funzioni di Azure usa il runtime di Java 8 JDK di Azul quando l'app per le funzioni viene distribuita nel cloud.

[Supporto tecnico di Azure](https://azure.microsoft.com/en-us/support/) per i problemi con i JDK e le app per le funzioni è disponibile con un [piano di supporto completo](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte le dipendenze specificate nel file `pom.xml` del progetto verranno raggruppate automaticamente durante l'obiettivo [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Le dipendenze inserite nella directory `lib` verranno aggiunte al caricatore di classe di sistema in fase di esecuzione.

La dipendenza `com.microsoft.azure.functions:azure-functions-java-library` è specificata in classpath per impostazione predefinita e non deve essere inclusa nella directory `lib`. Le dipendenze elencate [qui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies), inoltre, vengono aggiunte al classpath da [azure-funzioni-java-worker](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Supporto dei tipi di dati

Per stabilire un'associazione con binding di input/output, è possibile usare Oggetti Java precedenti normali (POJO, Plain Old Java Object), tipi definiti in `azure-functions-java-library` o tipi di dati primitivi, ad esempio stringa o numero intero.

### <a name="plain-old-java-objects-pojos"></a>Oggetti Java precedenti normali (POJO, Plain Old Java Object)

Per la conversione dei dati di input in oggetti POJO, [azure-funzioni-java-worker](https://github.com/Azure/azure-functions-java-worker) usa la libreria [gson](https://github.com/google/gson). I tipi POJO usati come input delle funzioni devono essere `public`.

### <a name="binary-data"></a>Dati binari

Per associare dati di input/output binari a `byte[]`, impostare il campo `dataType` nel file function.json su `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Usare `Optional<T>` se sono previsti valori Null

## <a name="bindings"></a>Associazioni

I binding di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Una funzione può avere più binding di input e output.

### <a name="example-input-binding"></a>Esempio di binding di input

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Questa funzione viene richiamata con una richiesta HTTP. 
- Il payload della richiesta HTTP viene passato come `String` per l'argomento `inputReq`
- Viene recuperata una voce da Archiviazione tabelle di Azure e passata come `TestInputData` all'argomento `inputData`.

Per ricevere un batch di input, è possibile stabilire un'associazione a `String[]`, `POJO[]`, `List<String>` o `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Questa funzione viene attivata ogni volta che sono disponibili nuovi dati nell'hub eventi configurato. Poiché l'oggetto `cardinality` è impostato su `MANY`, la funzione riceve un batch di messaggi dall'hub eventi. L'oggetto EventData dell'hub eventi viene convertito in `TestEventData` per l'esecuzione della funzione.

### <a name="example-output-binding"></a>Esempio di binding di output

È possibile associare un binding di output al valore restituito usando `$return` 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Per inviare più valori di output, usare il tipo `OutputBinding<T>` definito nel pacchetto `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

La funzione precedente viene richiamata su una HttpRequest e scrive più valori nella coda di Azure

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 I tipi HttpRequestMessage e HttpResponseMessage definiti in `azure-functions-java-library` sono tipi di helper per l'interazione con le funzioni HttpTrigger

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Metodo Get, intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Stato restituito diverso da 200   |

## <a name="metadata"></a>Metadata

Alcuni trigger inviano [metadati dei trigger](/azure/azure-functions/functions-triggers-bindings) insieme ai dati di input. È possibile usare l'annotazione `@BindingName` per stabilire un'associazione con i metadati dei trigger


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Nell'esempio precedente, `queryValue` è associato al parametro della stringa di query `name` nell'URL della richiesta `http://{example.host}/api/metadata?name=test`. Di seguito è riportato un altro esempio di binding a `Id` dai metadati del trigger della coda

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Il nome specificato nell'annotazione deve corrispondere alla proprietà dei metadati

## <a name="execution-context"></a>Contesto di esecuzione

L'oggetto `ExecutionContext` definito in `azure-functions-java-library` contiene metodi helper per comunicare con il runtime di Funzioni.

### <a name="logger"></a>Logger

Usare `getLogger` definito in `ExecutionContext` per scrivere i log dal codice della funzione.

Esempio:

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

È possibile usare l'interfaccia della riga di comando di Azure per trasmettere in streaming la registrazione dei canali stdout e stderr di Java, oltre alla registrazione di altre applicazioni. 

Configurare l'applicazione Funzioni per scrivere la registrazione delle applicazioni tramite l'interfaccia della riga di comando di Azure:

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

In Funzioni, le [impostazioni dell'app](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando `System.getenv("AzureWebJobsStorage")`

Esempio:

Aggiungere [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) con il nome testAppSetting e il valore testAppSettingValue

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Java delle funzioni di Azure, vedere le risorse seguenti:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
- Sviluppo locale ed esecuzione del debug con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) ed [Eclipse](functions-create-maven-eclipse.md). 
* [Eseguire il debug remoto di funzioni di Azure in Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per Funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md): semplifica la creazione della funzione tramite l'obiettivo `azure-functions:add` e prepara una directory di gestione [per la distribuzione tramite file ZIP](deployment-zip-push.md).
