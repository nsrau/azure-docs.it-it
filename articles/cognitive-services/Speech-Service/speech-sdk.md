---
title: Informazioni su Speech SDK di Servizi cognitivi
description: Panoramica degli SDK disponibili per il servizio di riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6b5796bf4d049579dbdede2251f2ca67cc9c4bfd
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "41937527"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informazioni su Speech SDK di Servizi cognitivi

Speech Software Development Kit (SDK) di Servizi cognitivi consente alle applicazioni accesso nativo alle funzioni del servizio di riconoscimento vocale, rendendo più semplice lo sviluppo di software. Attualmente, l'SDK consente l'accesso alle funzioni di **riconoscimento vocale**, **traduzione vocale** e **riconoscimento finalità**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Ottenere l'SDK

### <a name="windows"></a>Windows

Per Windows sono supportati le lingue seguenti:

* C#, (piattaforma UWP e .NET), C++: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet.
  Il pacchetto include librerie client a 32 e 64 bit, nonché librerie gestite (.NET).
  Per installare l'SDK in Visual Studio è possibile usare NuGet. È sufficiente cercare `Microsoft.CognitiveServices.Speech`.

* Java: è possibile fare riferimento e usare la versione più recente del nostro pacchetto riconoscimento vocale SDK Maven, che supporta solo Windows x64.
  Nel progetto Maven, aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e il riferimento`com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` come dipendenza. 

### <a name="linux"></a>Linux

> [!NOTE]
> Attualmente è supportato solo Ubuntu 16.04 in un computer (x86 o x64 per lo sviluppo in C++, x64 per .NET Core e Java).

Verificare di avere installato il compilatore e le librerie necessarie eseguendo i comandi della shell seguenti:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK NuGet.
  Per fare riferimento all’SDK, aggiungere il seguente riferimento al pacchetto nel progetto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: è possibile fare riferimento e usare la versione più recente del pacchetto riconoscimento vocale SDK Maven.
  Nel progetto Maven, aggiungere `https://csspeechstorage.blob.core.windows.net/maven/` come repository aggiuntivo e il riferimento`com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` come dipendenza. 

* C++: scaricare l'SDK come [pacchetto con estensione tar](https://aka.ms/csspeech/linuxbinary) e decomprimere i file nella directory desiderata. La tabella seguente illustra la struttura di cartelle dell'SDK.

  |path|DESCRIZIONE|
  |-|-|
  |`license.md`|Licenza|
  |`ThirdPartyNotices.md`|Comunicazioni di terze parti|
  |`include`|File di intestazione per C e C++|
  |`lib/x64`|Libreria x64 nativa per il collegamento all'applicazione|
  |`lib/x86`|Libreria x86 nativa per il collegamento all'applicazione|

  Per creare un'applicazione, copiare o spostare i file binari e le librerie necessari nell'ambiente di sviluppo e includerli nel processo di compilazione come richiesto.

### <a name="android"></a>Android

Java SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste per usarlo.
È ospitato in un repository Maven in `https://csspeechstorage.blob.core.windows.net/maven/` come pacchetto `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Per usare il pacchetto dal progetto Android Studio apportare le modifiche seguenti:

* Nel file al livello di progetto `build.gradle` aggiungere quanto segue nella sezione `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Nel modulo al livello di progetto `build.gradle` aggiungere quanto segue nella sezione `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

Java SDK fa inoltre parte di [Speech Devices SDK](speech-devices-sdk.md).

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
