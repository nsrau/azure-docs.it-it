---
title: 'Guida introduttiva: Libreria client di Content Moderator per Java | Microsoft Docs'
description: Introduzione alla libreria client di Content Moderator per Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: pafarley
ms.openlocfilehash: 5d1575818ac35c45af8a7df59c0853389d01031c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698542"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Guida introduttiva: Libreria client di Content Moderator per Java

Introduzione alla libreria client di Content Moderator per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Content Moderator è un servizio cognitivo che verifica la presenza di materiale potenzialmente offensivo, rischioso o altrimenti indesiderato in contenuti di testo, immagini e video. Quando tale materiale viene trovato, il servizio applica al contenuto apposite etichette (flag). L'app può quindi gestire il contenuto contrassegnato per garantire la conformità alle normative o gestire un ambiente con le caratteristiche previste per gli utenti.

Usare la libreria client di Content Moderator per Java per:

* Moderare le immagini per individuare contenuti per adulti e spinti, testo o visi umani.

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Artefatto (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=content+moderator&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* La versione più recente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-content-moderator-azure-resource"></a>Creare una risorsa di Azure per Content Moderator

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Content Moderator usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```
Eseguire `gradle init`. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione. Eseguire questo comando dalla directory di lavoro:

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio DSL di script di compilazione, selezionare **Kotlin**.

Individuare *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare la configurazione di compilazione seguente. Questa configurazione definisce il progetto come applicazione Java il cui punto di ingresso è la classe **ContentModeratorQuickstart**. Importa Content Moderator SDK e Gson SDK per la serializzazione JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Nella directory di lavoro eseguire il comando seguente per creare una cartella di origine del progetto.

```console
mkdir -p src/main/java
```

Quindi, creare un file denominato *ContentModeratorQuickstart.java* nella nuova cartella. Aprire il file in un editor o un IDE a scelta e importare le librerie seguenti nella parte superiore:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Content Moderator Java SDK.

|NOME|DESCRIZIONE|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Questa classe è necessaria per tutte le funzionalità di Content Moderator. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Questa classe fornisce la funzionalità per analizzare le immagini e individuare contenuti per adulti, informazioni personali o visi umani.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Questa classe fornisce la funzionalità per analizzare il testo e individuare lingua, volgarità, errori e informazioni personali.|
|[Revisioni](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Questa classe fornisce la funzionalità delle API di revisione, inclusi i metodi per la creazione di processi, flussi di lavoro personalizzati e revisioni umane.|


## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Content Moderator per Java:

* [Autenticare il client](#authenticate-the-client)
* [Moderare le immagini](#moderate-images)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo passaggio presuppone che sia stata [creata una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave di Content Moderator, denominata `AZURE_CONTENTMODERATOR_KEY`.

Nel metodo `main` dell'applicazione creare un oggetto [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) usando il valore dell'endpoint sottoscrizione e la variabile di ambiente della chiave di sottoscrizione. 

> [!NOTE]
> Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Moderare le immagini

### <a name="get-images"></a>Ottenere le immagini

Nella cartella **src/main/** del progetto creare una cartella **resources** e aprirla. Quindi, creare un nuovo file di testo, *ImageFiles.txt*. In questo file aggiungere gli URL delle immagini da analizzare, inserendo un URL in ogni riga. È possibile usare i contenuti di esempio seguenti:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="use-helper-class"></a>Usare la classe helper

Quindi, nel file *ContentModeratorQuickstart.Java* aggiungere la definizione di classe seguente all'interno della classe **ContentModeratorQuickstart**. Questa classe interna verrà usata in seguito nel processo di moderazione delle immagini.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Eseguire l'iterazione tra le immagini

Aggiungere quindi il codice seguente alla fine del metodo `main`. In alternativa, è possibile aggiungerlo a un metodo separato chiamato da `main`. Questo codice esamina il file _ImageFiles.txt_ una riga alla volta.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificare la presenza di contenuti per adulti o spinti
Questa riga di codice verifica la presenza di contenuti per adulti o spinti nell'immagine in corrispondenza dell'URL specificato. Per informazioni su questi termini, vedere la guida concettuale alla moderazione di immagini.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verificare la presenza di testo
Questa riga di codice verifica la presenza di testo visibile nell'immagine.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificare la presenza di visi
Questa riga di codice verifica la presenza di visi umani nell'immagine.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Infine, archiviare le informazioni restituite nell'elenco `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Stampare i risultati

Dopo il ciclo `while`, aggiungere il codice seguente, che stampa i risultati nella console e in un file di output, *src/main/Resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Chiudere l'istruzione `try` e aggiungere un'istruzione `catch` per completare il metodo.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

```console
gradle build
```

Eseguire l'applicazione con il comando `gradle run`:

```console
gradle run
```

Passare quindi al file *src/main/Resources/ModerationOutput.json* e visualizzare i risultati della moderazione del contenuto.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria Java di Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

> [!div class="nextstepaction"]
>[Concetti di moderazione delle immagini](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Che cos'è Azure Content Moderator?](./overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).