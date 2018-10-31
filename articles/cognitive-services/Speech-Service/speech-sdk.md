---
title: Informazioni su Speech Service SDK
titleSuffix: Azure Cognitive Services
description: Panoramica degli SDK disponibili per il servizio Voce.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: wolfma
ms.openlocfilehash: f071c15ea586f7b146b82a6201adf16dadc65add
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464812"
---
# <a name="about-the-speech-service-sdk"></a>Informazioni su Speech Service SDK

Speech Service Software Development Kit (SDK) fornisce alle applicazioni l'accesso nativo alle funzioni del servizio Voce, rendendo più semplice lo sviluppo di software. Attualmente, l'SDK consente l'accesso alle funzioni di **riconoscimento vocale**, **traduzione vocale** e **riconoscimento finalità**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Ottenere l'SDK

### <a name="windows"></a>Windows

Per Windows sono supportati le lingue seguenti:

* C#, (piattaforma UWP e .NET), C++: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet. Il pacchetto include librerie client a 32 e 64 bit e librerie gestite (.NET). Per installare l'SDK in Visual Studio, è possibile usare NuGet. Cercare **Microsoft.CognitiveServices.Speech**.

* Java: è possibile fare riferimento e usare la versione più recente del pacchetto Maven di Speech SDK, che supporta solo Windows x64. Nel progetto Maven aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e fare riferimento a `com.microsoft.cognitiveservices.speech:client-sdk:1.0.1` come dipendenza. 

### <a name="linux"></a>Linux

> [!NOTE]
> Attualmente è supportato solo Ubuntu 16.04 in un PC (x86 o x64 per lo sviluppo in C++ e x64 per .NET Core e Java).

Verificare di avere installato il compilatore e le librerie necessarie eseguendo i comandi della shell seguenti:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet. Per fare riferimento all'SDK, aggiungere al progetto il riferimento al pacchetto seguente:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.0.1" />
  ```

* Java: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK Maven. Nel progetto Maven aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e fare riferimento a `com.microsoft.cognitiveservices.speech:client-sdk:1.0.1` come dipendenza. 

* C++: scaricare l'SDK come [pacchetto con estensione tar](https://aka.ms/csspeech/linuxbinary) e decomprimere i file nella directory desiderata. La tabella seguente illustra la struttura di cartelle dell'SDK:

  |Percorso|Descrizione|
  |-|-|
  |`license.md`|Licenza|
  |`ThirdPartyNotices.md`|Comunicazioni di terze parti|
  |`include`|File di intestazione per C e C++|
  |`lib/x64`|Libreria x64 nativa per il collegamento all'applicazione|
  |`lib/x86`|Libreria x86 nativa per il collegamento all'applicazione|

  Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

### <a name="android"></a>Android

Java SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste per usarlo. È ospitato in un repository Maven in `https://csspeechstorage.blob.core.windows.net/maven/` come pacchetto `com.microsoft.cognitiveservices.speech:client-sdk:1.0.1`.

Per usare il pacchetto dal progetto Android Studio apportare le modifiche seguenti:

* Nel file build.gradle a livello di progetto aggiungere quanto segue nella sezione `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Nel file build.gradle a livello di modulo aggiungere quanto segue nella sezione `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.0.1'
  ```

Java SDK fa inoltre parte di [Speech Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
