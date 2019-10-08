---
title: 'Guida introduttiva: Libreria client di Ricerca visiva Bing per Java | Microsoft Docs'
description: Caricare un'immagine usando l'SDK di Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695725"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Guida introduttiva: Libreria client di Ricerca visiva Bing per Java

Questo argomento costituisce un'introduzione alla libreria client di Ricerca visiva Bing per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. 

Usare la libreria client di Ricerca visiva Bing per Java per:

* Caricare un'immagine per inviare una richiesta di ricerca visiva.
* Ottenere il token delle informazioni cognitive dettagliate e i tag di ricerca visiva dell'immagine.

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefatto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze

## <a name="setting-up"></a>Configurazione

### <a name="create-a-bing-visual-search-azure-resource"></a>Creare una risorsa di Azure per Ricerca visiva Bing

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Ricerca visiva Bing usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. quindi copiare questa configurazione della build:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Creare una cartella per l'app di esempio. Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir -p src/main/java
```

Creare una cartella per l'immagine da caricare nell'API. Inserire l'immagine nella cartella **resources**.

```console
mkdir -p src/main/resources
``` 

Passare alla nuova cartella e creare un file denominato *BingVisualSearchSample.java*. Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Creare quindi una nuova classe.

```java
public class BingVisualSearchSample {
}
```

Nel metodo `main` dell'applicazione creare variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione. Creare quindi un tipo di dati `byte[]` per l'immagine da caricare. Creare un blocco `try` per i metodi che verranno definiti in un secondo momento, quindi caricare l'immagine e convertirla in byte usando `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installare la libreria client

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. La libreria client e le informazioni per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Nel file *build.gradle.kts* del progetto assicurarsi di includere la libreria client come istruzione `implementation`. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Ricerca visiva Bing e Java:

* [Autenticare il client](#authenticate-the-client)
* [Inviare una richiesta di ricerca visiva](#send-a-visual-search-request)
* [Stampare il token delle informazioni cognitive dettagliate e i tag di ricerca visiva dell'immagine](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che sia stata [creata una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave di Ricerca visiva Bing, denominata `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Nel metodo Main assicurarsi di usare la chiave di sottoscrizione per creare un'istanza di un oggetto [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable).

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Inviare una richiesta di ricerca visiva

In un nuovo metodo inviare la matrice di byte immagine (creata nel metodo `main()`) usando il metodo [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) del client. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Stampare il token delle informazioni cognitive dettagliate e i tag di ricerca visiva dell'immagine

Controllare se l'oggetto [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) è Null. Se non lo è, stampare il token delle informazioni cognitive dettagliate dell'immagine, il numero di tag, il numero di azioni e il primo tipo di azione.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

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
> [Creare un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app.md)
