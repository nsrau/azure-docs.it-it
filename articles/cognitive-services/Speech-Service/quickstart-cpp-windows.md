---
title: 'Avvio rapido: Riconoscimento vocale in C++ su Windows Desktop con Speech SDK di Servizi cognitivi | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in C++ su Windows Desktop con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 4a8c5f7053c1976233bf9de6a0c142885b73c8aa
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071199"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C# su Windows Desktop con Speech SDK

Viene illustrato come creare un'applicazione console basata su C++ per Windows Desktop che usi Speech SDK.
L'applicazione si basa sul [pacchetto NuGet Speech SDK di Servizi cognitivi Microsoft](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC Windows con un microfono funzionante.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition o versione successiva.
* Il carico di lavoro **Sviluppo di applicazioni Desktop con C++** in Visual Studio e il componente **Gestione pacchetti NuGet** in Visual Studio.
  È possibile abilitare **Strumenti** \> **Scaricare gli strumenti e le funzioni** nelle schede **Carichi di lavoro** e **Singoli componenti**, rispettivamente:

  ![Consentire lo sviluppo Desktop con il carico di lavoro C++](media/sdk/vs-enable-cpp-workload.png)

  ![Abilitare Gestione pacchetti NuGet in Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

In Visual Studio 2017 creare una nuova applicazione console Windows in Visual C++ per Windows Desktop. Nella finestra di dialogo **Nuovo progetto** nel riquadro a sinistra, espandere **Installato** \> **Visual C++** \> **Windows Desktop** e quindi selezionare **Applicazione Console Windows**. Per il nome del progetto, immettere *helloworld*.

![Creare un'applicazione console Windows in Visual C++ per Windows Desktop](media/sdk/qs-cpp-windows-01-new-console-app.png)

Se si esegue un'installazione di Windows a 64 bit, passare facoltativamente dalla piattaforma build in uso a `x64`:

![Passare dalla piattaforma build a x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installare e fare riferimento al pacchetto NuGet Speech SDK

In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

![Scegliere Gestisci pacchetti NuGet per la soluzione](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

Nel campo **Origine pacchetto** nell'angolo superiore destro scegliere "Nuget.org".
Nella scheda **Esplora** cercare il pacchetto "Microsoft.CognitiveServices.Speech", selezionarlo, selezionare le caselle **Progetto** e **helloworld** a destra e selezionare **Installa** per installarlo nel progetto helloworld.

> [!NOTE]
> La versione corrente di Speech SDK di Servizi cognitivi è `0.5.0`.

![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Nella schermata di licenza visualizzata accettare la licenza:

![Accettare la licenza](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Sostituire il codice di avvio predefinito con il seguente:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori:

   ![Compilazione completata](media/sdk/qs-cpp-windows-06-build.png)

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

   ![Avviare l'app nel debug](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Si apre una finestra della console che chiede di dire qualcosa (in inglese).
   Il risultato del riconoscimento verrà visualizzato sullo schermo.

   ![Output della console dopo il riconoscimento corretto](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/cpp-windows`.

## <a name="next-steps"></a>Passaggi successivi

* Visitare la [pagina degli esempi](samples.md) per esempi aggiuntivi.
