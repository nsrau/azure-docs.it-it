---
title: 'Guida introduttiva: Libreria client di Ricerca personalizzata Bing per Java | Microsoft Docs'
description: Iniziare a usare la libreria client di Ricerca personalizzata Bing per Java richiedendo i risultati della ricerca dall'istanza di Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 7d1a7b2cdba082c78a1753ea0dcce6ead02ab036
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148321"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Guida introduttiva: Libreria client di Ricerca personalizzata Bing per Java

Introduzione alla libreria client di Ricerca personalizzata Bing per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'API Ricerca personalizzata Bing permette di creare esperienze di ricerca personalizzate senza annunci per specifici argomenti di interesse.

Usare la libreria client di Ricerca personalizzata Bing per Java per:

* Trovare i risultati della ricerca sul Web dall'istanza di Ricerca personalizzata Bing. 

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefatto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md) per altre informazioni.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-bing-custom-search-azure-resource"></a>Creare una risorsa di Azure per Ricerca personalizzata Bing

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Ricerca personalizzata Bing usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#decision), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

> [!TIP]
> Se non si usa Gradle, i dettagli della libreria client per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando crea i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

## <a name="install-the-client-library"></a>Installare la libreria client 

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare questa configurazione della build. Assicurarsi di includere la libreria client in `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Creare una cartella per l'app di esempio. Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir src/main/java
```

Passare alla nuova cartella e creare un file denominato *BingCustomSearchSample.java*. Aprirlo e aggiungere le istruzioni `import` seguenti:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Creare una classe denominata `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Nella classe creare un metodo `main` e le variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione. Gli altri metodi verranno definiti successivamente.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modello a oggetti

Il client di Ricerca personalizzata Bing è un oggetto [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) creato dal metodo [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) dell'oggetto [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable). È possibile inviare una richiesta di ricerca usando il metodo [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) del client.

La risposta dell'API è un oggetto [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) contenente le informazioni sulla query di ricerca e i risultati della ricerca.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Ricerca personalizzata Bing per Java:

* [Autenticare il client](#authenticate-the-client)
* [Ottenere i risultati della ricerca dall'istanza di ricerca personalizzata](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autenticare il client

Il metodo main deve includere un oggetto [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) che accetta la chiave e chiama il relativo metodo `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Ottenere i risultati della ricerca dall'istanza di ricerca personalizzata

Usare la funzione [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) del client per inviare una query di ricerca all'istanza personalizzata. Impostare `withCustomConfig` sull'ID di configurazione personalizzata oppure usare il valore predefinito `1`. Dopo aver ricevuto una risposta dall'API, controllare se sono stati trovati risultati della ricerca. In tal caso, ottenere il primo risultato della ricerca chiamando la funzione `webPages().value().get()` della risposta e stampare il nome del risultato e l'URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Compilare l'app con il comando seguente dalla directory principale del progetto:

```console
gradle build
```

Eseguire l'applicazione con il comando `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
