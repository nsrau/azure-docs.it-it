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
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163710"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

Il runtime di funzioni di Azure supporta [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Questa guida contiene informazioni preoccupare delle complessità della scrittura di funzioni di Azure con Java.

Una funzione Java è un `public` metodo decorato con l'annotazione `@FunctionName`. Questo metodo definisce la voce per una funzione Java e deve essere univoco in un pacchetto specifico. 

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È necessario completare anche l'avvio rapido di funzioni per creare la prima funzione usando [Visual Studio Code](functions-create-first-function-vs-code.md) oppure [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modello di programmazione 

I concetti di [trigger e binding](functions-triggers-bindings.md) sono fondamentali in Funzioni di Azure. I trigger avviano l'esecuzione del codice, mentre i binding consentono la trasmissione dei dati e la restituzione dei dati da una funzione, senza dover scrivere codice personalizzato per l'accesso ai dati.

## <a name="folder-structure"></a>Struttura di cartelle

Ecco la struttura di cartelle di un progetto Java di funzioni di Azure:

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

È possibile usare un oggetto condiviso [host. JSON](functions-host-json.md) file per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. Il `FunctionApp` nella destinazione di directory è ciò che viene distribuito all'app di funzione in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento dati. La funzione deve elaborare tale trigger e altri input, per produrre uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Per altre informazioni, vedere la [documentazione di riferimento Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> È necessario configurare un account di archiviazione di Azure nel [Local](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire localmente l'archiviazione Blob di Azure, archiviazione code di Azure o i trigger di archiviazione tabelle di Azure.

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

Ecco il corrispondente generata `function.json` per il [azure-funzioni-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

Per lo sviluppo locale di App per le funzioni Java, scaricare e usare la [Azul Zulu Enterprise per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK dal [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Funzioni di Azure usa il runtime di Java 8 JDK di Azul quando l'app per le funzioni viene distribuita nel cloud.

[Supporto di Azure](https://azure.microsoft.com/support/) per i problemi con il JDK e la funzione è disponibile con le app una [piano di supporto completo](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personalizzare JVM

Funzioni consente di personalizzare la macchina virtuale Java (JVM) usata per eseguire le funzioni Java. Il [le opzioni di JVM seguenti](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) vengono utilizzati per impostazione predefinita:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

È possibile fornire argomenti aggiuntivi in un'impostazione dell'app denominata `JAVA_OPTS`. È possibile aggiungere le impostazioni dell'app per le app per le funzioni distribuite in Azure nel portale di Azure o il comando di Azure.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com), utilizzare il [scheda delle impostazioni dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings) per aggiungere il `JAVA_OPTS` impostazione.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile usare la [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) comando per impostare `JAVA_OPTS`, come illustrato nell'esempio seguente:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Questo esempio abilita modalità headless. Sostituire `<APP_NAME>` con il nome dell'app per le funzioni e `<RESOURCE_GROUP>` con il gruppo di risorse.

> [!WARNING]  
> Nel [piano a consumo](functions-scale.md#consumption-plan), è necessario aggiungere il `WEBSITE_USE_PLACEHOLDER` impostazione con un valore di `0`.  
Questa impostazione di aumentare i tempi di avvio a freddo per funzioni Java.

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte le dipendenze specificate nel progetto `pom.xml` file sono inclusi automaticamente durante il [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) obiettivo. Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Dipendenze inserite nel `lib` directory vengono aggiunti al caricatore di classe di sistema in fase di esecuzione.

Il `com.microsoft.azure.functions:azure-functions-java-library` dipendenza sono fornita sul classpath per impostazione predefinita e non deve essere incluso nel `lib` directory. È inoltre [azure-funzioni-java-worker](https://github.com/Azure/azure-functions-java-worker) aggiunge le dipendenze elencate [qui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) al classpath.

## <a name="data-type-support"></a>Supporto dei tipi di dati

È possibile usare oggetti Java precedenti normali (Pojo), tipi definiti in `azure-functions-java-library`, o tipi di dati primitivi, ad esempio String e Integer per l'associazione di input o le associazioni di output.

### <a name="pojos"></a>Pojo

Per la conversione dei dati di input in POJO, [azure-funzioni-java-worker](https://github.com/Azure/azure-functions-java-worker) Usa le [gson](https://github.com/google/gson) libreria. I tipi POJO usati come input delle funzioni devono essere `public`.

### <a name="binary-data"></a>Dati binari

Eseguire l'associazione di input / output al binari `byte[]`, impostando il `dataType` campo il file Function. JSON a `binary`:

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

Se si prevede che i valori null, usare `Optional<T>`.

## <a name="bindings"></a>Associazioni

I binding di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Una funzione può avere più binding di input e output.

### <a name="input-binding-example"></a>Esempio di associazione di input

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

Si richiama questa funzione con una richiesta HTTP. 
- Payload della richiesta HTTP viene passato come un `String` dell'argomento `inputReq`.
- Una voce viene recuperata dall'archiviazione tabelle e viene passata come `TestInputData` nell'argomento `inputData`.

Per ricevere un batch di input, è possibile associare a `String[]`, `POJO[]`, `List<String>`, o `List<POJO>`.

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

Questa funzione viene attivata ogni volta che sono disponibili nuovi dati nell'hub eventi configurato. Poiché il `cardinality` è impostata su `MANY`, la funzione riceve un batch di messaggi dall'hub eventi. `EventData` evento dell'hub viene convertito in un `TestEventData` per l'esecuzione della funzione.

### <a name="output-binding-example"></a>Esempio di associazione di output

È possibile associare un'associazione di output per il valore restituito usando `$return`. 

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

Si richiama questa funzione in una HttpRequest. Scrive più valori in archiviazione di Accodamento.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Questi vengono definiti `azure-functions-java-library`. Si tratta di tipi di helper per lavorare con le funzioni HttpTrigger.

| Tipo specializzato      |       Destinazione        | Utilizzo tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Ottiene (metodo), le intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Restituisce lo stato diverso da 200   |

## <a name="metadata"></a>Metadata

Alcuni trigger inviano [metadati dei trigger](/azure/azure-functions/functions-triggers-bindings) insieme ai dati di input. È possibile utilizzare l'annotazione `@BindingName` da associare a metadati dei trigger.


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
Nell'esempio precedente, il `queryValue` è associato al parametro di stringa di query `name` nell'URL della richiesta http, `http://{example.host}/api/metadata?name=test`. Ecco un altro esempio, che mostra come associare a `Id` dai metadati del trigger della coda.

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
> Il nome fornito nell'annotazione deve corrispondere alla proprietà dei metadati.

## <a name="execution-context"></a>Contesto di esecuzione

`ExecutionContext`, definito nel `azure-functions-java-library`, contiene i metodi helper per comunicare con il runtime di funzioni.

### <a name="logger"></a>Logger

Uso `getLogger`, definita in `ExecutionContext`, scrivere i log dal codice della funzione.

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

È possibile usare il comando di Azure per la registrazione di stderr e stdout Java flusso, nonché altri la registrazione delle applicazioni. 

Di seguito viene illustrato come configurare l'app di funzione per scrivere la registrazione delle applicazioni tramite la CLI di Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Per trasmettere l'output di registrazione per le app per le funzioni usando il comando di Azure, aprire un nuovo prompt dei comandi, Bash o del terminale e immettere il comando seguente:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Il [della parte finale del log di az webapp](/cli/azure/webapp/log) comando ha opzioni da applicare un filtro output utilizzando il `--provider` opzione. 

Per scaricare i file di log come un unico file ZIP con la CLI di Azure, aprire un nuovo prompt dei comandi, Bash o del terminale e immettere il comando seguente:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

È necessario aver abilitato la registrazione nel portale di Azure o il comando di Azure prima di eseguire questo comando del sistema di file.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando, `System.getenv("AzureWebJobsStorage")`.

Ad esempio, è possibile aggiungere [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), con il nome `testAppSetting` e il valore `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Java di funzioni di Azure, vedere le risorse seguenti:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* Lo sviluppo locale e debug con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), e [Eclipse](functions-create-maven-eclipse.md)
* [Eseguire il debug remoto di funzioni di Azure in Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per le funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Semplificare la creazione della funzione tramite il `azure-functions:add` obiettivo e la preparazione di una directory di gestione temporanea [distribuzione tramite file ZIP](deployment-zip-push.md).
