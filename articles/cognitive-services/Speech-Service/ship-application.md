---
title: Servizi cognitivi di Azure - Speech SDK su Servizi cognitivi - Esercitazioni e informazioni di riferimento sulle API | Microsoft Docs
description: Di seguito viene descritto come creare e sviluppare le app con Speech SDK di Servizi cognitivi
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: c2b06ed4c31dc585111000266e57888f490938ec
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258975"
---
# <a name="shipping-an-application"></a>Esecuzione dello shipping di un’applicazione

Osservare la [licenza Speech SDK](license.md), nonché gli [avvisi software di terze parti](third-party-notices.md) quando si distribuisce Speech SDK di Servizi cognitivi. Leggere inoltre l'[Informativa sulla privacy di Microsoft](https://aka.ms/csspeech/privacy).

A seconda della piattaforma, esistono diverse dipendenze per eseguire l'applicazione.

## <a name="windows"></a>Windows

Speech SDK do Servizi cognitivi è stato testato su Windows 10 e su Windows Server 2016.

Speech SDK di Servizi cognitivi richiede il [Microsoft Visual C++ Redistributable per Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) sul sistema. È possibile scaricare i programmi di installazione per l'ultima versione di `Microsoft Visual C++ Redistributable for Visual Studio 2017` qui:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Se l'applicazione utilizza codice gestito, è necessario `.Net Framework 4.6.1` o successivo sul computer di destinazione.

Per l'input del microfono, è necessario installare le raccolte Media Foundation. Queste raccolte sono parte di Windows 10 e Windows Server 2016. È possibile utilizzare Speech SDK senza queste raccolte finché non viene utilizzato il microfono come dispositivo di input audio.

I file Speech SDK richiesti possono essere distribuiti nella stessa directory dell'applicazione. In questo modo l'applicazione può accedere direttamente alle raccolte. Assicurarsi di selezionare la versione corretta (Win32/x64) corrispondente all'applicazione.

| NOME | Funzione
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | SDK di base, necessario per la distribuzione nativa e gestita
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | obbligatorio per le distribuzioni gestite
| `Microsoft.CognitiveServices.Speech.csharp.dll` | obbligatorio per le distribuzioni gestite

## <a name="linux"></a>Linux

Per un'applicazione nativa è necessario fornire la raccolta Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Assicurarsi di selezionare la versione (x86/x64) corrispondente all'applicazione. A seconda della versione di Linux, potrebbe essere necessario anche includere le seguenti dipendenze:

* Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
* La raccolta OpenSSL (`libssl.so.1.0.0`)
* La raccolta cURL (`libcurl.so.4`)
* La libreria condivisa per le applicazioni ALSA (`libasound.so.2`)

Su Ubuntu 16.04, ad esempio, le librerie GNU C dovrebbero essere già installate per impostazione predefinita. È possibile installare gli ultimi tre utilizzando i comandi seguenti:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
