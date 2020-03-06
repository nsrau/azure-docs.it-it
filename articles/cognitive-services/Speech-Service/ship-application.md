---
title: Sviluppare app con il servizio riconoscimento vocale (Speech SDK)
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire un'applicazione che usa l'SDK di riconoscimento vocale nelle piattaforme supportate.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330805"
---
# <a name="ship-an-application"></a>Spedire un'applicazione

Osservare la [licenza Speech SDK](https://aka.ms/csspeech/license201809), nonché gli [avvisi software di terze parti](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) durante la distribuzione di Speech SDK su servizi cognitivi. Leggere inoltre l'[Informativa sulla privacy di Microsoft](https://aka.ms/csspeech/privacy).

A seconda della piattaforma, esistono diverse dipendenze per eseguire l'applicazione.

## <a name="windows"></a>WINDOWS

Speech SDK do Servizi cognitivi è stato testato su Windows 10 e su Windows Server 2016.

L'SDK per la sintesi vocale di servizi cognitivi richiede [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) nel sistema. È possibile scaricare i programmi di installazione per l'ultima versione di `Microsoft Visual C++ Redistributable for Visual Studio 2019` qui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se l'applicazione utilizza codice gestito, è necessario `.NET Framework 4.6.1` o successivo sul computer di destinazione.

Per l'input del microfono, è necessario installare le raccolte Media Foundation. Queste raccolte sono parte di Windows 10 e Windows Server 2016. È possibile utilizzare Speech SDK senza queste raccolte finché non viene utilizzato il microfono come dispositivo di input audio.

I file Speech SDK richiesti possono essere distribuiti nella stessa directory dell'applicazione. In questo modo l'applicazione può accedere direttamente alle raccolte. Assicurarsi di selezionare la versione corretta (Win32/x64) corrispondente all'applicazione.

| Name | Funzione |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK di base, necessario per la distribuzione nativa e gestita |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Obbligatorio per le distribuzioni gestite                      |

> [!NOTE]
> A partire dalla versione 1.3.0, il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata in Core SDK.

> [!NOTE]
> Per il progetto app Windows Forms (.NET Framework C# ), verificare che le librerie siano incluse nelle impostazioni di distribuzione del progetto. È possibile selezionarlo in `Properties -> Publish Section`. Fare clic sul pulsante `Application Files` e trovare le librerie corrispondenti dall'elenco Scorri verso il basso. Verificare che il valore sia impostato su `Included`. In Visual Studio il file verrà incluso quando il progetto viene pubblicato/distribuito.

## <a name="linux"></a>Linux

Speech SDK supporta attualmente le distribuzioni Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8.
Per un'applicazione nativa è necessario fornire la raccolta Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Assicurarsi di selezionare la versione (x86/x64) corrispondente all'applicazione. A seconda della versione di Linux, potrebbe essere necessario anche includere le seguenti dipendenze:

- Le raccolte condivise della Libreria GNU C (inclusa la libreria di programmazione thread POSIX, `libpthreads`)
- Libreria OpenSSL (`libssl.so.1.0.0` o `libssl.so.1.0.2`)
- La libreria condivisa per le applicazioni ALSA (`libasound.so.2`)

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

In RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> In RHEL/CentOS 8 seguire le istruzioni su [come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Informazioni sul riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
