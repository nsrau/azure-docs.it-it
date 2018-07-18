---
title: Guida introduttiva di Speech SDK per C++ e Windows | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare Speech SDK con Windows e C++ in Servizi cognitivi.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049231"
---
# <a name="quickstart-for-c-and-windows"></a>Guida introduttiva per C++ e Windows

La versione corrente di Speech SDK di Servizi cognitivi è `0.4.0`.

Viene illustrato come creare un'applicazione console basata su C++ per Windows Desktop che usi Speech SDK.
L'applicazione si basa sul [pacchetto NuGet Microsoft Cognitive Services SDK](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) e su Microsoft Visual Studio 2017.

> [!NOTE]
> Per la guida introduttiva a C++ e Linux, vedere [qui](quickstart-cpp-linux.md).<br>
> Per la guida introduttiva a C# e Windows, vedere [qui](quickstart-csharp-windows.md).

> [!NOTE]
> Per questa guida introduttiva è necessario un PC con un microfono funzionante.<br>
> Per il riconoscimento vocale da un file di input audio specifico, vedere l'[esempio](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Assicurarsi che l'installazione di Visual Studio includa il carico di lavoro **Sviluppo di applicazioni desktop con C++**.
> In caso di dubbi, seguire questa procedura per verificarlo e risolvere l'eventuale problema: in Visual Studio 2017 selezionare **Strumenti** \> **Get Tools and Features** (Scarica strumenti e funzionalità) e confermare il prompt di Controllo dell'account utente scegliendo **Sì**.
> Nella scheda **Carichi di lavoro**, se la casella di controllo **Sviluppo di applicazioni desktop con C++** non è selezionata, impostarla e fare clic su **Modifica** per salvare le modifiche.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Creazione di un progetto di applicazione console vuoto

In Visual Studio 2017 creare una nuova applicazione console Windows in Visual C++ per Windows Desktop con il nome "CppHelloSpeech":

![Creare un'applicazione console Windows in Visual C++ per Windows Desktop](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Se si esegue un'installazione di Windows a 64 bit, passare facoltativamente dalla piattaforma build in uso a `x64`:

![Passare dalla piattaforma build a x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installare e fare riferimento al pacchetto NuGet Speech SDK

> [!NOTE]
> Assicurarsi che Gestione pacchetti NuGet sia abilitata per l'installazione di Visual Studio 2017.
> In Visual Studio 2017 selezionare **Strumenti** \> **Get Tools and Features** (Scarica strumenti e funzionalità) e confermare il prompt di Controllo dell'account utente scegliendo **Sì**. Selezionare quindi **Singoli componenti** e cercare **Gestione pacchetti NuGet** in **Strumenti per il codice**.
> Se la casella di controllo a sinistra non è selezionata, selezionarla e fare clic su **Modifica** per salvare le modifiche.
>
> ![Abilitare Gestione pacchetti NuGet in Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

![Scegliere Gestisci pacchetti NuGet per la soluzione](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

Nel campo **Origine pacchetto** nell'angolo superiore destro scegliere "Nuget.org".
Nella scheda **Esplora** cercare il pacchetto "Microsoft.CognitiveServices.Speech", selezionarlo, selezionare le caselle **Progetto** e **CppHelloSpeech** a destra e selezionare **Installa** per installarlo nel progetto CppHelloSpeech.

![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Nella schermata di licenza visualizzata accettare la licenza:

![Accettare la licenza](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

Sostituire il codice di avvio predefinito con il seguente:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Sostituire la chiave della sottoscrizione con quella ottenuta. <br>
> Sostituire l'area con la propria area dell'[API REST del servizio di riconoscimento vocale](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis), ad esempio con "westus".

![Aggiungere la chiave della sottoscrizione](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Il codice verrà ora compilato senza errori:

![Compilazione completata](media/sdk/speechsdk-16-vs-cpp-build.png)

Avviare il programma nel debugger con il pulsante di avvio o con la scelta rapida da tastiera F5:

![Avviare l'app nel debug](media/sdk/speechsdk-17-vs-cpp-f5.png)

Verrà aperta una finestra della console che chiede di dire qualcosa (in inglese).
Il risultato del riconoscimento verrà visualizzato sullo schermo.

![Output della console dopo il riconoscimento corretto](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Download dell'esempio

Per l'insieme di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

* Visitare la [pagina degli esempi](samples.md) per esempi aggiuntivi.
