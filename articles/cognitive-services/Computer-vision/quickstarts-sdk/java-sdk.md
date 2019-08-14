---
title: 'Guida introduttiva: Libreria client di Visione artificiale per Java'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client di Visione artificiale per Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 15baf5ee2418581056d571340ba6e8009c33e4ca
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829266"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Guida introduttiva: Libreria client di Visione artificiale per Java

Introduzione alla libreria client di Visione artificiale per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Visione artificiale consente di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.

Usare la libreria client di Visione artificiale per Java per:

* Analizzare un'immagine per trovare tag, descrizioni di testo, visi, contenuto per adulti e altro ancora.

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Artefatto (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-computer-vision-azure-resource"></a>Creare una risorsa di Visione artificiale in Azure

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Visione artificiale usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `COMPUTER_VISION_SUBSCRIPTION_KEY`.

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

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare la configurazione di compilazione seguente. Questa configurazione definisce il progetto come applicazione Java il cui punto di ingresso è la classe **ComputerVisionQuickstarts**. Importa la libreria di Visione artificiale.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Dalla directory di lavoro eseguire il comando seguente per creare una cartella di origine del progetto:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *ComputerVisionQuickstarts.Java.* Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Aggiungere quindi una definizione di classe per **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Installare la libreria client

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. La libreria client e le informazioni per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Nel file *build.gradle.kts* del progetto includere la libreria client di Visione artificiale come dipendenza.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK Visione artificiale per Java.

|NOME|DESCRIZIONE|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Questa classe deriva dall'oggetto client e gestisce direttamente tutte le operazioni per le immagini, come l'analisi, il rilevamento del testo e la generazione di anteprime.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per Java:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che sia stata [creata una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave di Visione artificiale, denominata `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Il codice seguente aggiunge un metodo `main` alla classe e crea le variabili per l'endpoint e per la chiave di Azure della risorsa. È necessario immettere la propria stringa di endpoint, che è possibile trovare consultando la sezione **Panoramica** del portale di Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Aggiungere quindi il codice seguente per creare un oggetto [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) e passarlo ad altri metodi, che verranno definiti in un secondo momento.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente definisce un metodo, `AnalyzeLocalImage`, che usa l'oggetto client per analizzare un'immagine locale e stampare i risultati. Il metodo restituisce una descrizione di testo, una categorizzazione, un elenco di tag, i visi rilevati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Prima di tutto, creare una cartella **resources/** nella cartella **src/main/** del progetto e aggiungere un'immagine da analizzare. Aggiungere quindi la definizione di metodo seguente alla classe **ComputerVisionQuickstarts**. Se necessario, cambiare il valore di `pathToLocalImage` in modo che corrisponda al file di immagine. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Quindi, specificare le caratteristiche visive da estrarre nell'analisi. Per un elenco completo, vedere l'enumerazione [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analizzare
Questo metodo stampa i risultati dettagliati nella console per ogni ambito dell'analisi di immagini. È consigliabile racchiudere questa chiamata al metodo in un blocco try/catch

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>Visualizzare i risultati

La chiamata al metodo precedente restituirà un oggetto ImageAnalysis che contiene tutte le informazioni estratte. Per stampare i dettagli di una determinata caratteristica, è possibile usare un blocco di codice simile al seguente.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

Per un set completo di opzioni di visualizzazione, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

```console
gradle build
```

Eseguire l'applicazione con il comando `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come usare la libreria di Visione artificiale per Java per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
>[Informazioni di riferimento su Visione artificiale (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Informazioni su Visione artificiale](../Home.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).