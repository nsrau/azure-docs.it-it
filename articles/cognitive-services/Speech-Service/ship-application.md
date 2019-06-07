---
title: Sviluppare app con Speech SDK - Servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare app con Speech SDK.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: d8330ff663a7adc2f1cae3ed6bdf1cec2fa972c5
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808447"
---
# <a name="ship-an-application"></a>Spedire un'applicazione

Osservare la [licenza Speech SDK](https://aka.ms/csspeech/license201809), nonché gli [avvisi software di terze parti](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) durante la distribuzione di Speech SDK su servizi cognitivi. Leggere inoltre l'[Informativa sulla privacy di Microsoft](https://aka.ms/csspeech/privacy).

A seconda della piattaforma, esistono diverse dipendenze per eseguire l'applicazione.

## <a name="windows"></a>Windows

Speech SDK do Servizi cognitivi è stato testato su Windows 10 e su Windows Server 2016.

il SDK di riconoscimento vocale di servizi cognitivi richiede la [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) nel sistema. È possibile scaricare i programmi di installazione per l'ultima versione di `Microsoft Visual C++ Redistributable for Visual Studio 2019` qui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se l'applicazione utilizza codice gestito, è necessario `.NET Framework 4.6.1` o successivo sul computer di destinazione.

Per l'input del microfono, è necessario installare le raccolte Media Foundation. Queste raccolte sono parte di Windows 10 e Windows Server 2016. È possibile utilizzare Speech SDK senza queste raccolte finché non viene utilizzato il microfono come dispositivo di input audio.

I file Speech SDK richiesti possono essere distribuiti nella stessa directory dell'applicazione. In questo modo l'applicazione può accedere direttamente alle raccolte. Assicurarsi di selezionare la versione corretta (Win32/x64) corrispondente all'applicazione.

| Name | Funzione
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | SDK di base, necessario per la distribuzione nativa e gestita
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Obbligatorio per le distribuzioni gestite

>[!NOTE]
> Iniziando con la versione 1.3.0 file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (forniti nelle versioni precedenti) non è più necessaria. La funzionalità è ora integrata nel nucleo SDK.

## <a name="linux"></a>Linux

Speech SDK attualmente supporta le distribuzioni di Ubuntu 16.04, 18.04 Ubuntu e Debian 9.
Per un'applicazione nativa è necessario fornire la raccolta Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Assicurarsi di selezionare la versione (x86/x64) corrispondente all'applicazione. A seconda della versione di Linux, potrebbe essere necessario anche includere le seguenti dipendenze:

* Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
* La libreria OpenSSL (`libssl.so.1.0.0` o `libssl.so.1.0.2`)
* La libreria condivisa per le applicazioni ALSA (`libasound.so.2`)

In Ubuntu le librerie GNU C dovrebbero essere già installate per impostazione predefinita. È possibile installare gli ultimi tre utilizzando i comandi seguenti:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

In Debian 9 installare questi pacchetti:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
