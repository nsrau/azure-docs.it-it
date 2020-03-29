---
title: Informazioni su Speech SDK - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Speech Software Development Kit (SDK) fornisce alle applicazioni l'accesso nativo alle funzioni del servizio Voce, rendendo più semplice lo sviluppo di software. Questo articolo fornisce dettagli aggiuntivi sull'SDK per Windows, Linux e Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331094"
---
# <a name="about-the-speech-sdk"></a>Informazioni su Speech SDK

Speech Software Development Kit (SDK) fornisce alle applicazioni l'accesso alle funzioni del servizio Voce, rendendo più semplice lo sviluppo di software con funzionalità vocali. Attualmente, gli SDK forniscono l'accesso alla sintesi **vocale, alla** **sintesi vocale,** alla **traduzione vocale,** al **riconoscimento delle finalità**e al canale Direct Line Speech di **Bot Framework.**

Puoi facilmente acquisire l'audio da un microfono, leggere da un flusso o accedere ai file audio dall'archiviazione con Speech SDK. Speech SDK supporta l'audio a canale singolo WAV/PCM a 16 bit, 16 kHz/8 kHz per il riconoscimento vocale. Ulteriori formati audio sono supportati utilizzando [l'endpoint REST di sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) o il servizio di [trascrizione batch.](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)

Una panoramica generale sulle funzionalità e le piattaforme supportate è disponibile nella pagina di [immissione](https://aka.ms/csspeech)della documentazione.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Ottenere l'SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Speech SDK supporta Windows 10 o versioni successive. Le versioni precedenti di Windows non sono **supportate.**

Per Windows sono supportati le lingue seguenti:

* C#, (piattaforma UWP e .NET), C++: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet. Il pacchetto include librerie client a 32 e 64 bit e librerie gestite (.NET). L'SDK può essere installato in Visual Studio utilizzando NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: è possibile fare riferimento e usare la versione più recente del pacchetto Maven di Speech SDK, che supporta solo Windows x64. Nel progetto Maven aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e fare riferimento a `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` come dipendenza.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Attualmente, supportiamo solo Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 e CentOS 8 sulle seguenti architetture di destinazione:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) per lo sviluppo in C
> - x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) per Java
> - x64 per .NET Core e Python

Assicurarsi di avere installato le librerie necessarie eseguendo i seguenti comandi della shell:

In Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

In Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Su RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Su RHEL/CentOS 8, seguire le istruzioni su [come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet. Per fare riferimento all'SDK, aggiungere al progetto il riferimento al pacchetto seguente:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK Maven. Nel progetto Maven aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e fare riferimento a `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` come dipendenza.

* C++: scaricare l'SDK come [pacchetto con estensione tar](https://aka.ms/csspeech/linuxbinary) e decomprimere i file nella directory desiderata. La tabella seguente illustra la struttura di cartelle dell'SDK:

  |Path|Descrizione|
  |-|-|
  |`license.md`|Licenza|
  |`ThirdPartyNotices.md`|Comunicazioni di terze parti|
  |`include`|File di intestazione per C e C++|
  |`lib/x64`|Libreria x64 nativa per il collegamento all'applicazione|
  |`lib/x86`|Libreria x86 nativa per il collegamento all'applicazione|

  Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

# <a name="android"></a>[Android](#tab/android)

Java SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste. È ospitato in un repository Maven in `https://csspeechstorage.blob.core.windows.net/maven/` come pacchetto `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Per usare il pacchetto dal progetto Android Studio apportare le modifiche seguenti:

* Nel file build.gradle a livello di progetto aggiungere quanto segue nella sezione `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Nel file build.gradle a livello di modulo aggiungere quanto segue nella sezione `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK fa inoltre parte di [Speech Devices SDK](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
