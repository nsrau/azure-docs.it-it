---
title: 'Avvio rapido: Libreria client di gestione di Azure per Java'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: a7fc96127d4449cfe0333ba81532d3c5c4ec19fe
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097018"
---
[Documentazione di riferimento](/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18) | [Pacchetto (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Installare la libreria client

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. La libreria client e le informazioni per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Nel file *build.gradle.kts* del progetto includere la libreria client come istruzione `implementation`, unitamente ai plug-in e alle impostazioni necessari.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Importare le librerie

Passare alla nuova cartella **src/main/Java** e creare un file denominato *Management.Java*. Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere una classe in *Management.Java*, quindi aggiungere i campi seguenti e i relativi valori all'interno. Popolare i valori usando l'entità servizio creata e le altre informazioni sull'account Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Quindi, nel metodo **main** usare questi valori per creare un oggetto **CognitiveServicesManager**. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Chiamare i metodi di gestione

Aggiungere il codice seguente alla fine del metodo **Main** per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Questi metodi verranno definiti nei passaggi seguenti.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. È possibile trovare un elenco di "tipologie" di servizi cognitivi disponibili chiamando il metodo seguente:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Per creare e sottoscrivere una nuova risorsa Servizi cognitivi, usare il metodo **create**. Questo metodo aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio che si vuole usare, oltre al piano tariffario (o SKU) e a una località di Azure. Il metodo seguente accetta tutti questi argomenti e crea una risorsa.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare il metodo seguente:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

Il metodo seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento Azure Management SDK](/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)
* [Informazioni su Servizi cognitivi di Azure](../../what-are-cognitive-services.md)
* [Autenticare le richieste a Servizi cognitivi di Azure](../../authentication.md)
* [Creare una nuova risorsa con il portale di Azure](../../cognitive-services-apis-create-account.md)