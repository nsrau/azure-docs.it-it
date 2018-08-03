---
title: Informazioni su Speech SDK di Servizi cognitivi
description: Panoramica degli SDK disponibili per il servizio di riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: c7eaa2aa37b05bd0e125e1841357979af4f6763a
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326060"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informazioni su Speech SDK di Servizi cognitivi

Speech Software Development Kit (SDK) di Servizi cognitivi consente alle applicazioni accesso nativo alle funzioni del servizio di riconoscimento vocale, rendendo più semplice lo sviluppo di software. Attualmente, l'SDK consente l'accesso alle funzioni di **riconoscimento vocale**, **traduzione vocale** e **riconoscimento finalità**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Ottenere l'SDK

### <a name="get-the-windows-sdk"></a>Ottenere Windows SDK

La versione Windows di Speech SDK include librerie client di C/C++ a 32 e a 64 bit, nonché librerie (.NET) gestite per l'uso con C#. Per installare l'SDK in Visual Studio è possibile usare NuGet. È sufficiente cercare `Microsoft.CognitiveServices.Speech`.

### <a name="get-the-linux-sdk"></a>Ottenere Linux SDK

Assicurarsi di avere il compilatore e le librerie necessarie eseguendo i comandi della shell seguenti:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Queste istruzioni presuppongono che sia in esecuzione Ubuntu 16.04 in un PC (x86 o x64). In una versione diversa di Ubuntu o in un'altra distribuzione Linux diversa, adattare questa procedura all'ambiente in uso.

Quindi [scaricare l'SDK](https://aka.ms/csspeech/linuxbinary) e decomprimere i file nella directory desiderata. Questa tabella illustra la struttura di cartelle dell'SDK.

|path|DESCRIZIONE|
|-|-|
|`license.md`|Licenza|
|`third-party-notices.md`|Comunicazioni di terze parti|
|`include`|File di intestazione per C e C++|
|`lib/x64`|Libreria x64 nativa per il collegamento all'applicazione|
|`lib/x86`|Libreria x86 nativa per il collegamento all'applicazione|

Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

### <a name="get-the-java-sdk"></a>Ottenere Java SDK

Java SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste per usarlo.
È ospitato in un repository Maven in `https://csspeechstorage.blob.core.windows.net/maven/` come pacchetto `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
Per usare il pacchetto dal progetto Android Studio apportare le modifiche seguenti:

* Nel file al livello di progetto `build.gradle` aggiungere quanto segue nella sezione `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Nel modulo al livello di progetto `build.gradle` aggiungere quanto segue nella sezione `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Java SDK fa inoltre parte di [Speech Devices SDK](speech-devices-sdk.md).

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
