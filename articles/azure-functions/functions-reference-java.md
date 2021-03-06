---
title: Guida di riferimento per gli sviluppatori Java per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326455"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

Questa guida contiene informazioni dettagliate che consentono di sviluppare funzioni di Azure con Java.

Gli sviluppatori Java, se non si ha familiarità con funzioni di Azure, si consiglia di leggere prima di tutto uno degli articoli seguenti:

| Guida introduttiva | Concetti| 
| -- | -- |  
| <ul><li>[Funzione Java con Visual Studio Code](./create-first-function-vs-code-java.md)</li><li>[Funzione Java/Maven con terminale/prompt dei comandi](./create-first-function-cli-java.md)</li><li>[Funzione Java con Gradle](functions-create-first-java-gradle.md)</li><li>[Funzione Java con Eclipse](functions-create-maven-eclipse.md)</li><li>[Funzione Java con IntelliJ IDEA](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Guida per sviluppatori](functions-reference.md)</li><li>[Opzioni di hosting](functions-scale.md)</li><li>[Considerazioni sulle prestazioni &nbsp;](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Nozioni fondamentali sulle funzioni Java

Una funzione Java è un metodo `public`, accompagnato dall'annotazione `@FunctionName`. Questo metodo definisce la voce per una funzione Java e deve essere unico in un particolare pacchetto. Il pacchetto può avere più classi con più metodi pubblici annotati con `@FunctionName` . Un singolo pacchetto viene distribuito in un'app per le funzioni in Azure. Quando è in esecuzione in Azure, l'app per le funzioni fornisce il contesto di distribuzione, esecuzione e gestione per le singole funzioni Java.

## <a name="programming-model"></a>Modello di programmazione 

I concetti di [trigger e binding](functions-triggers-bindings.md) sono fondamentali in Funzioni di Azure. I trigger avviano l'esecuzione del codice, mentre i binding consentono la trasmissione dei dati e la restituzione dei dati da una funzione, senza dover scrivere codice personalizzato per l'accesso ai dati.

## <a name="create-java-functions"></a>Creare funzioni Java

Per semplificare la creazione di funzioni Java, sono disponibili strumenti e archetipi basati su Maven che usano modelli Java predefiniti che consentono di creare progetti con un trigger di funzione specifico.    

### <a name="maven-based-tooling"></a>Strumenti basati su Maven

Negli ambienti di sviluppo seguenti sono disponibili strumenti di Funzioni di Azure che consentono di creare progetti di funzioni Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

I collegamenti agli articoli precedenti illustrano come creare le prime funzioni usando l'IDE preferito. 

### <a name="project-scaffolding"></a>Eseguire lo scaffold di un progetto

Se si preferisce eseguire lo sviluppo dalla riga di comando dal terminale, il modo più semplice per eseguire lo scaffold dei progetti di funzione basati su Java consiste nell'usare archetipi `Apache Maven`. L'archetipo Java Maven per Funzioni di Azure è pubblicato con il seguente _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Il comando seguente genera un nuovo progetto di funzione Java usando questo archetipo:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

Per iniziare a usare questo archetipo, vedere [Avvio rapido Java](./create-first-function-cli-java.md).

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

È possibile usare un file [host.json](functions-host-json.md) per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. L'elemento `FunctionApp` nella directory di destinazione viene distribuito all'app per le funzioni in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento dati. La funzione deve elaborare tale trigger e qualsiasi altro input per generare uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Per altre informazioni, vedere la [documentazione di riferimento per Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> È necessario configurare un account di Archiviazione di Azure nel file [local.settings.json](./functions-run-local.md#local-settings-file) per eseguire i trigger di Archivio BLOB di Azure, archiviazione code di Azure o archiviazione tabelle di Azure.

Esempio:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

ecco il file `function.json` corrispondente generato da [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="java-versions"></a>Versioni Java

La versione di Java usata quando si crea l'app per le funzioni in cui vengono eseguite le funzioni in Azure è specificata nel file di pom.xml. L'archetipo Maven genera attualmente una pom.xml per Java 8, che è possibile modificare prima di pubblicare. La versione di Java in pom.xml deve corrispondere alla versione in cui è stata sviluppata e testata localmente l'app. 

### <a name="supported-versions"></a>Versioni supportate

La tabella seguente mostra le versioni Java supportate correnti per ogni versione principale del runtime di funzioni, per sistema operativo:

| Versione di Funzioni | Versioni Java (Windows) | Versioni Java (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | n/d |

A meno che non si specifichi una versione Java per la distribuzione, l'archetipo di Maven viene impostato su Java 8 durante la distribuzione in Azure.

### <a name="specify-the-deployment-version"></a>Specificare la versione della distribuzione

È possibile controllare la versione di Java a cui è destinato l'archetipo Maven usando il `-DjavaVersion` parametro. Il valore di questo parametro può essere `8` o `11` . 

L'archetipo Maven genera un pom.xml destinato alla versione Java specificata. Gli elementi seguenti in pom.xml indicano la versione di Java da usare:

| Elemento |  Valore Java 8 | Valore Java 11 | Descrizione |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Versione di Java usata dal plug-in del compilatore Maven. |
| **`JavaVersion`** | 8 | 11 | Versione Java ospitata dall'app per le funzioni in Azure. |

Gli esempi seguenti illustrano le impostazioni per Java 8 nelle sezioni pertinenti del file pom.xml:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> È necessario impostare correttamente la variabile di ambiente JAVA_HOME sulla directory JDK utilizzata durante la compilazione del codice con Maven. Verificare che la versione del JDK sia almeno uguale alla massima dell' `Java.version` impostazione. 

### <a name="specify-the-deployment-os"></a>Specificare il sistema operativo di distribuzione

Maven consente inoltre di specificare il sistema operativo in cui viene eseguita l'app per le funzioni in Azure. Usare l' `os` elemento per scegliere il sistema operativo. 

| Elemento |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | Linux | docker |

Nell'esempio seguente viene illustrata l'impostazione del sistema operativo nella `runtime` sezione del file di pom.xml:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Disponibilità del runtime JDK e supporto 

Per lo sviluppo locale di app per le funzioni Java, scaricare e usare il JDK di [Azul Zulu Enterprise per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java da [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Funzioni di Azure usa un runtime di Azul Java JDK quando si distribuisce l'app per le funzioni nel cloud.

[Supporto tecnico di Azure](https://azure.microsoft.com/support/) per i problemi con i JDK e le app per le funzioni è disponibile con un [piano di supporto completo](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personalizzare JVM

Funzioni di Azure consente di personalizzare la JVM (macchina virtuale Java) usata per eseguire le funzioni Java. Per impostazione predefinita, vengono usate le [opzioni JVM seguenti](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7):

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

È possibile fornire argomenti aggiuntivi in un'impostazione dell'app denominata `JAVA_OPTS`. È possibile aggiungere impostazioni all'app per le funzioni distribuita in Azure nel portale di Azure o nell'interfaccia della riga di comando di Azure.

> [!IMPORTANT]  
> Nel piano a consumo, perché la personalizzazione funzioni è necessario aggiungere anche l'impostazione WEBSITE_USE_PLACEHOLDER con un valore pari a 0. Questa impostazione aumenta i tempi di avvio a freddo per le funzioni Java.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com), usare la [scheda Impostazioni applicazione](functions-how-to-use-azure-function-app-settings.md#settings) per aggiungere l'impostazione `JAVA_OPTS`.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile usare il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) per impostare `JAVA_OPTS`, come nell'esempio seguente:

# <a name="consumption-plan"></a>[Piano a consumo](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Piano a consumo](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Piano dedicato/piano Premium](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Piano dedicato/piano Premium](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Questo esempio abilita la modalità headless. Sostituire anche `<APP_NAME>` con il nome dell'app per le funzioni e `<RESOURCE_GROUP>` con il gruppo di risorse. 

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte le dipendenze specificate nel file `pom.xml` del progetto vengono raggruppate automaticamente durante l'obiettivo [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Le dipendenze inserite nella directory `lib` vengono aggiunte al caricatore di classe di sistema in fase di runtime.

La dipendenza `com.microsoft.azure.functions:azure-functions-java-library` è specificata in classpath per impostazione predefinita e non deve essere inclusa nella directory `lib`. Le dipendenze elencate [qui](https://github.com/Azure/azure-functions-java-worker), inoltre, vengono aggiunte al classpath da [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies).

## <a name="data-type-support"></a>Supporto dei tipi di dati

Per stabilire un'associazione con binding di input/output, è possibile usare oggetti Java precedenti normali (POJO, Plain Old Java Object), tipi definiti in `azure-functions-java-library` o tipi di dati primitivi, ad esempio stringa o numero intero.

### <a name="pojos"></a>Oggetti POJO

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

Se si prevedono valori null, usare `Optional<T>`.

## <a name="bindings"></a>Associazioni

I binding di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Una funzione può avere più binding di input e output.

### <a name="input-binding-example"></a>Esempio di associazione di input

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
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
- Il payload della richiesta HTTP viene passato come `String` per l'argomento `inputReq`.
- Viene recuperata una voce dall'archiviazione tabelle e passata come `TestInputData` all'argomento `inputData`.

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

Questa funzione viene attivata ogni volta che sono disponibili nuovi dati nell'hub eventi configurato. Poiché l'oggetto `cardinality` è impostato su `MANY`, la funzione riceve un batch di messaggi dall'hub eventi. L'oggetto `TestEventData` dell'hub eventi viene convertito in `EventData` per l'esecuzione della funzione.

### <a name="output-binding-example"></a>Esempio di associazione di output

È possibile associare un binding di output al valore restituito usando `$return`. 

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

Questa funzione viene richiamata con un comando HttpRequest. La funzione scrive più valori nell'archiviazione code.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Questi sono definiti in `azure-functions-java-library`. Si tratta di tipi di helper che funzionano con le funzioni HttpTrigger.

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Metodo Get, intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Restituisce uno stato diverso da 200   |

## <a name="metadata"></a>Metadati

Alcuni trigger inviano [metadati dei trigger](./functions-triggers-bindings.md) insieme ai dati di input. È possibile usare l'annotazione `@BindingName` per stabilire un'associazione con i metadati dei trigger.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Nell'esempio precedente, `queryValue` è associato al parametro della stringa di query `name` nell'URL della richiesta HTTP `http://{example.host}/api/metadata?name=test`. Di seguito è riportato un altro esempio di binding a `Id` dai metadati del trigger della coda.

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
> Il nome specificato nell'annotazione deve corrispondere alla proprietà dei metadati.

## <a name="execution-context"></a>Contesto di esecuzione

L'oggetto `ExecutionContext` definito in `azure-functions-java-library` contiene metodi helper per comunicare con il runtime di Funzioni. Per ulteriori informazioni, vedere l' [articolo di riferimento ExecutionContext](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Logger

Usare l'oggetto `getLogger` definito in `ExecutionContext` per scrivere i log dal codice della funzione.

Esempio:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visualizzazione dei log e della traccia

È possibile usare l'interfaccia della riga di comando di Azure per trasmettere in streaming la registrazione dei canali stdout e stderr di Java, oltre alla registrazione di altre applicazioni. 

Ecco come configurare l'app per le funzioni per scrivere la registrazione delle applicazioni tramite l'interfaccia della riga di comando di Azure:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Per eseguire lo streaming dell'output dell'app per le funzioni usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione terminal o Bash e immettere il comando seguente:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

Il comando [az webapp log tail](/cli/azure/webapp/log) offre opzioni per filtrare l'output usando l'opzione `--provider`. 

Per scaricare i file di log come un singolo file ZIP usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione terminal o Bash e immettere il comando seguente:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Prima di eseguire questo comando, è necessario aver abilitato la registrazione del file system nel portale di Azure o nell'interfaccia della riga di comando di Azure.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando `System.getenv("AzureWebJobsStorage")`.

Nell'esempio seguente viene ottenuta l'[impostazione applicazione](functions-how-to-use-azure-function-app-settings.md#settings) con la chiave denominata `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Il valore di AppSetting FUNCTIONS_EXTENSION_VERSION deve essere ~2 o ~3 per un'esperienza di avvio a freddo ottimizzata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di Funzioni di Azure in Java, vedere le risorse seguenti:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* Sviluppo locale ed esecuzione del debug con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) ed [Eclipse](functions-create-maven-eclipse.md)
* [Debug remoto di funzioni Java usando Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per Funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Semplificare la creazione della funzione tramite l'obiettivo `azure-functions:add` e preparare una directory di gestione per la [distribuzione tramite file ZIP](deployment-zip-push.md).
