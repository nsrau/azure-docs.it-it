---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656721"
---
:::row:::
    :::column span="3":::
        Java SDK per Android è incluso in <a href="https://developer.android.com/studio/projects/android-library" target="_blank">un <span class="docon docon-navigate-external x-hidden-focus"> </span>AAR (libreria Android) </a>, che include le librerie necessarie e le autorizzazioni Android necessarie. È ospitato in un repository Maven in `https://csspeechstorage.blob.core.windows.net/maven/` come pacchetto `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Per usare il pacchetto dal progetto Android Studio apportare le modifiche seguenti:

1. Nel file *build.gradle* a livello di progetto, aggiungere quanto segue alla `repository` sezione:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Nel file *build.gradle* a livello di modulo, aggiungere quanto segue alla `dependencies` sezione:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK fa inoltre parte di [Speech Devices SDK](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Risorse aggiuntive

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Codice sorgente dell'avvio rapido Java specifico per Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Codice sorgente dell'avvio rapido Java Runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>