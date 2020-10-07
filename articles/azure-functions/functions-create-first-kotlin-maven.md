---
title: Creare la prima funzione in Azure con Kotlin e Maven
description: Creare e pubblicare una funzione attivata da HTTP in Azure con Kotlin e Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 526287feb93052d10428971dfd8f660d4265c831
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89071262"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Guida di avvio rapido: Creare la prima funzione con Kotlin e Maven

Questo articolo illustra come usare lo strumento da riga di comando Maven per compilare e pubblicare un progetto di funzione Kotlin in Funzioni di Azure. Al termine, il codice della funzione viene eseguito nel [piano a consumo](functions-scale.md#consumption-plan) in Azure e può essere attivato usando una richiesta HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per sviluppare funzioni con Kotlin, è necessario che siano installati gli elementi seguenti:

- [Java Developer Kit](https://aka.ms/azure-jdks), versione 8
- [Apache Maven](https://maven.apache.org), versione 3.0 o successive
- [Interfaccia della riga di comando di Azure](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successive

> [!IMPORTANT]
> Per completare questa guida di avvio rapido, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

## <a name="generate-a-new-functions-project"></a>Generare un nuovo progetto di Funzioni

In una cartella vuota eseguire il comando seguente per generare il progetto di Funzioni da un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Se si verificano problemi durante l'esecuzione del comando, esaminare quale versione di `maven-archetype-plugin` è in uso. Poiché il comando viene eseguito in una directory vuota senza file `.pom`, potrebbe provare a usare un plug-in della versione meno recente di `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, se Maven è stato aggiornato da una versione precedente. In questo caso, provare a eliminare la directory `maven-archetype-plugin` e a eseguire di nuovo il comando.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven richiede l'immissione dei valori necessari per completare la generazione del progetto. Per i valori _groupId_, _artifactId_ e _version_, vedere le informazioni di riferimento relative alle [convenzioni di denominazione Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Il valore _appName_ deve essere univoco in Azure. Maven genera pertanto un nome di app in base al valore _artifactId_ precedentemente immesso come nome predefinito. Il valore _packageName_ determina il pacchetto Kotlin per il codice della funzione generato.

Gli identificatori `com.fabrikam.functions` e `fabrikam-functions` riportati più avanti vengono usati come esempio e per semplificare la lettura dei passaggi successivi di questa guida di avvio rapido. È consigliabile specificare valori personalizzati a Maven in questo passaggio.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven crea i file di progetto in una nuova cartella denominata _artifactId_, in questo esempio `fabrikam-functions`. Il codice pronto per l'esecuzione generato nel progetto è una funzione [attivata da HTTP](./functions-bindings-http-webhook.md) semplice che restituisce il corpo della richiesta:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Passare alla cartella in cui si trova il nuovo progetto creato e quindi generare ed eseguire la funzione con Maven:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Se si verifica questa eccezione: `javax.xml.bind.JAXBException` con Java 9, vedere la soluzione alternativa in [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Questo output viene visualizzato quando la funzione è in esecuzione in locale nel sistema ed è pronta per la risposta alle richieste HTTP:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Attivare la funzione dalla riga di comando usando curl in una nuova finestra del terminale:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Usare `Ctrl-C` nel terminal per interrompere il codice funzione.

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

Il processo di distribuzione in Funzioni di Azure usa le credenziali dell'account dell'interfaccia della riga di comando di Azure. [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) prima di continuare.

```azurecli
az login
```

Distribuire il codice in una nuova app per le funzioni usando la destinazione Maven `azure-functions:deploy`.

> [!NOTE]
> Quando si usa Visual Studio Code per distribuire l'app per le funzioni, scegliere una sottoscrizione non gratuita, altrimenti si verificherà un errore. È possibile individuare la sottoscrizione sul lato sinistro dell'IDE.

```
mvn azure-functions:deploy
```

Quando la distribuzione è stata completata, viene visualizzato l'URL da usare per accedere all'app per le funzioni di Azure:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Eseguire il test dell'app per le funzioni in esecuzione in Azure usando `cURL`. Sarà necessario modificare l'URL dall'esempio seguente in modo che corrisponda all'URL distribuito per l'app per le funzioni specifica dal passaggio precedente.

> [!NOTE]
> Assicurarsi di impostare i **diritti di accesso** su `Anonymous`. Quando si sceglie il livello predefinito di `Function`, occorre presentare il [function key](functions-bindings-http-webhook-trigger.md#authorization-keys) nelle richieste di accesso all'endpoint della funzione.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Apportare modifiche e ripetere la distribuzione

Modificare il file di origine `src/main.../Function.java` nel progetto generato per cambiare il testo restituito dall'app per le funzioni. Sostituire questa riga:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Nel codice seguente:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Salvare le modifiche e ripetere la distribuzione eseguendo `azure-functions:deploy` dal terminale, come indicato in precedenza. Verranno aggiornate l'app per le funzioni e questa richiesta:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Viene visualizzato l'output aggiornato:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Associazioni di riferimento

Per usare [trigger e associazioni di Funzioni](functions-triggers-bindings.md) diversi dal trigger HTTP e dal trigger Timer, è necessario installare le estensioni di binding. Anche se non è richiesto per questo articolo, è necessario saper abilitare le estensioni quando si usano altri tipi di binding.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata un'app per le funzioni Kotlin con un trigger HTTP semplice ed è stata distribuita in Funzioni di Azure.

- Vedere [Guida per sviluppatori Java per Funzioni di Azure](functions-reference-java.md) per altre informazioni sullo sviluppo di funzioni Java e Kotlin.
- Al progetto aggiungere altre funzioni con trigger diversi usando la destinazione Maven `azure-functions:add`.
- Scrivere ed eseguire il debug delle funzioni in locale con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) ed [Eclipse](functions-create-maven-eclipse.md). 
- Eseguire il debug delle funzioni distribuite in Azure con Visual Studio Code. Per istruzioni, vedere la documentazione di Visual Studio Code relativa alle [applicazioni Java senza server](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
