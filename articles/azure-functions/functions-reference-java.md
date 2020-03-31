---
title: Java developer reference for Azure Functions
description: Informazioni sullo sviluppo di funzioni con Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276751"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

Il runtime di Funzioni di Azure supporta [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Questa guida contiene informazioni sulla complessità della scrittura di Funzioni di Azure con Java.This guide contains information about the retricacies of writing Azure Functions with Java.

Come accade ad altri linguaggi, un App per le funzioni può avere una o più funzioni. Una funzione Java `public` è un metodo, decorato con l'annotazione `@FunctionName`. Questo metodo definisce la voce per una funzione Java e deve essere univoco in un determinato pacchetto. Un'app per le funzioni scritta in Java può `@FunctionName`avere più classi con più metodi pubblici annotati con .

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È inoltre necessario completare la guida introduttiva di Funzioni per creare la prima funzione, utilizzando [Visual Studio Code](functions-create-first-function-vs-code.md) o [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modello di programmazione 

I concetti di [trigger e binding](functions-triggers-bindings.md) sono fondamentali in Funzioni di Azure. I trigger avviano l'esecuzione del codice, mentre i binding consentono la trasmissione dei dati e la restituzione dei dati da una funzione, senza dover scrivere codice personalizzato per l'accesso ai dati.

## <a name="create-java-functions"></a>Creare funzioni Java

Per semplificare la creazione di funzioni Java, sono disponibili oggetti basati su Maven e archetipi che utilizzano modelli Java predefiniti per facilitare la creazione di progetti con un trigger di funzione specifico.    

### <a name="maven-based-tooling"></a>Utensili basati su Maven

Gli ambienti di sviluppo seguenti dispongono di strumenti di Funzioni di Azure che consentono di creare progetti di funzioni Java:The following developer environments have Azure Functions tooling that lets you create Java function projects: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

I collegamenti dell'articolo precedente mostrano come creare le prime funzioni utilizzando l'IDE di scelta. 

### <a name="project-scaffolding"></a>Scaffolding di progetto

Se si preferisce lo sviluppo da riga di comando dal terminale, `Apache Maven` il modo più semplice per eseguire lo scaffolding di progetti di funzione basati su Java consiste nell'utilizzare gli archetipi. Attualmente ci sono due archetipi di Funzioni per Maven:

+ **Java Archetype**: pubblicato negli elementi groupId e artifactId [seguenti.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Per iniziare a utilizzare questo archetipo, vedere la [guida introduttiva](functions-create-first-java-maven.md)di Java . 

+ **Kotlin Archetype (anteprima)** pubblicato negli argomenti groupId e artifactId [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

Il codice sorgente di questi archetipi è disponibile nel repository GitHub di [Azure Maven Archetypes](https://github.com/microsoft/azure-maven-archetypes).


## <a name="folder-structure"></a>Struttura di cartelle

Di seguito è riportata la struttura di cartelle di un progetto Java di Funzioni di Azure:Here is the folder structure of an Azure Functions Java project:

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

_Il progetto Kotlin sembra molto simile in quanto è ancora Maven_

È possibile usare un file [host.json](functions-host-json.md) condiviso per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. Nella `FunctionApp` directory di destinazione è ciò che viene distribuito all'app per le funzioni in Azure.The in the target directory is what is deployed to your function app in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento dei dati. La funzione deve elaborare tale trigger e qualsiasi altro input per produrre uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Per ulteriori informazioni, consultate la documentazione di [riferimento su Java.](/java/api/com.microsoft.azure.functions.annotation)

> [!IMPORTANT] 
> È necessario configurare un account di archiviazione di Azure in [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire l'archiviazione BLOB di Azure, l'archiviazione delle code di Azure o i trigger di archiviazione tabelle di Azure in locale.

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

Ecco la corrispondente `function.json` generata dal [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

Per lo sviluppo locale di app per le funzioni Java, scaricare e usare i JDK [di Azul di Windows Enterprise per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 di [Azul Systems.](https://www.azul.com/downloads/azure-only/zulu/) Funzioni di Azure usa il runtime di Java 8 JDK di Azul quando l'app per le funzioni viene distribuita nel cloud.

[Il supporto di Azure](https://azure.microsoft.com/support/) per i problemi relativi a JDK e alle app per le funzioni è disponibile con un piano di [supporto qualificato.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Personalizza JVM

Funzioni consente di personalizzare la macchina virtuale Java (JVM) utilizzata per eseguire le funzioni Java. Per impostazione predefinita, vengono utilizzate le [seguenti opzioni JVM:](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

È possibile fornire argomenti aggiuntivi `JAVA_OPTS`in un'impostazione dell'app denominata . È possibile aggiungere impostazioni dell'app all'app per le funzioni distribuita in Azure nel portale di Azure o nell'interfaccia della riga di comando di Azure.You can add app settings to your function app deployed to Azure in the Azure portal or the Azure CLI.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com)usare la scheda `JAVA_OPTS` Impostazioni [applicazione](functions-how-to-use-azure-function-app-settings.md#settings) per aggiungere l'impostazione.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile utilizzare il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) per impostare `JAVA_OPTS`, come nell'esempio seguente:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
In questo esempio viene abilitata la modalità headless. Sostituire `<APP_NAME>` con il nome dell'app per le funzioni e `<RESOURCE_GROUP>` con il gruppo di risorse.

> [!WARNING]  
> Nel [piano Consumo](functions-scale.md#consumption-plan)è `WEBSITE_USE_PLACEHOLDER` necessario aggiungere l'impostazione con un valore di `0`.  
Questa impostazione aumenta i tempi di avvio a freddo per le funzioni Java.

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte `pom.xml` le dipendenze specificate [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) nel file di progetto vengono automaticamente raggruppate durante l'obiettivo. Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Le dipendenze `lib` inserite nella directory vengono aggiunte al caricatore della classe di sistema in fase di esecuzione.

La `com.microsoft.azure.functions:azure-functions-java-library` dipendenza viene fornita nel percorso di classe per impostazione predefinita `lib` e non deve essere inclusa nella directory. Inoltre, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) aggiunge le dipendenze elencate [di seguito](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) al percorso di classe.

## <a name="data-type-support"></a>Supporto dei tipi di dati

È possibile utilizzare oggetti Java semplici vecchi (POJO), tipi definiti in `azure-functions-java-library`tipi di dati primitivi, ad esempio String e Integer, per eseguire l'associazione alle associazioni di input o di output.

### <a name="pojos"></a>POJE

Per convertire i dati di input in POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) usa la libreria [gson.](https://github.com/google/gson) I tipi POJO usati come input delle funzioni devono essere `public`.

### <a name="binary-data"></a>Dati binari

Associare input o output `byte[]`binari a `dataType` , impostando il `binary`campo in function.json su :

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

Se si prevedono `Optional<T>`valori Null, utilizzare .

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

Richiamare questa funzione con una richiesta HTTP. 
- Il payload della richiesta `String` HTTP `inputReq`viene passato come oggetto per l'argomento .
- Una voce viene recuperata dall'archiviazione Table `inputData`e viene passata all'argomento `TestInputData` .

Per ricevere un batch di input, `String[]` `POJO[]`è `List<String>`possibile `List<POJO>`eseguire l'associazione a , , o .

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

Questa funzione viene attivata ogni volta che sono disponibili nuovi dati nell'hub eventi configurato. Poiché `cardinality` l'oggetto è impostato su `MANY`, la funzione riceve un batch di messaggi dall'hub eventi. `EventData`from event hub `TestEventData` viene convertito in per l'esecuzione della funzione.

### <a name="output-binding-example"></a>Esempio di associazione di output

È possibile associare un'associazione `$return`di output al valore restituito utilizzando . 

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

Richiamare questa funzione su un HttpRequest.You invoke this function on an HttpRequest. Scrive più valori nell'archivio della coda.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Questi sono `azure-functions-java-library`definiti in . Sono tipi di supporto da usare con le funzioni HttpTrigger.They are helper types to work with HttpTrigger functions.

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Ottiene metodo, intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Restituisce lo stato diverso da 200   |

## <a name="metadata"></a>Metadati

Alcuni trigger inviano [metadati dei trigger](/azure/azure-functions/functions-triggers-bindings) insieme ai dati di input. È possibile `@BindingName` utilizzare l'annotazione per eseguire l'associazione per attivare i metadati.


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
Nell'esempio precedente, `queryValue` l'oggetto viene associato `name` al parametro `http://{example.host}/api/metadata?name=test`della stringa di query nell'URL della richiesta HTTP, . Ecco un altro esempio che mostra `Id` come eseguire l'associazione ai metadati del trigger della coda.

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

`ExecutionContext`, definito `azure-functions-java-library`in , contiene metodi di supporto per comunicare con il runtime delle funzioni.

### <a name="logger"></a>Logger

Utilizzare `getLogger`, `ExecutionContext`definito in , per scrivere i log dal codice della funzione.

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

È possibile usare l'interfaccia della riga di comando di Azure per trasmettere la registrazione stdout e stderr Java, nonché altre registrazioni dell'applicazione. 

Ecco come configurare l'app per le funzioni per scrivere la registrazione dell'applicazione usando l'interfaccia della riga di comando di Azure:Here's how to configure your function app to write application logging by using the Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Per trasmettere l'output di registrazione per l'app per le funzioni usando l'interfaccia della riga di comando di Azure, aprire una nuova sessione di prompt dei comandi, Bash o Terminal e immettere il comando seguente:To stream logging output for your function app by using the Azure CLI, open a new command prompt, Bash, or Terminal session, and enter the following command:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Il comando [az webapp log tail](/cli/azure/webapp/log) include `--provider` opzioni per filtrare l'output utilizzando l'opzione . 

Per scaricare i file di log come singolo file zip usando l'interfaccia della riga di comando di Azure, aprire una nuova sessione di prompt dei comandi, Bash o Terminal e immettere il comando seguente:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

È necessario aver abilitato la registrazione del file system nel portale di Azure o nell'interfaccia della riga di comando di Azure prima di eseguire questo comando.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a `System.getenv("AzureWebJobsStorage")`queste impostazioni utilizzando , .

Nell'esempio seguente viene ottenuta [l'impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings), con la chiave denominata `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Java di Funzioni di Azure, vedere le risorse seguenti:For more information about Azure Functions Java development, see the following resources:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di AzureAzure Functions triggers and bindings](functions-triggers-bindings.md)
* Sviluppo locale ed eseguire il debug con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)ed [Eclipse](functions-create-maven-eclipse.md)
* [Eseguire il debug remoto di funzioni di Azure in Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per Funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Semplificare la `azure-functions:add` creazione di funzioni attraverso l'obiettivo e preparare una directory di gestione temporanea per la distribuzione di [file zip](deployment-zip-push.md).
