---
title: 'Avvio rapido: Riconoscimento vocale in C# per .NET Core su Windows con Speech SDK di Servizi cognitivi | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in C# per .NET Core su Windows con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 50cf1bbbe529b30da6bfe39281d11eee1c788dd8
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259161"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C# per .NET Core su Windows con Speech SDK | Microsoft Docs

Questo articolo spiega come creare un'applicazione console C# per .NET Core in Windows usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale.
L'applicazione si basa sul [pacchetto NuGet Speech SDK di Servizi cognitivi Microsoft](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

> [!NOTE]
> .NET core è una multipiattaforma open source di .NET che implementa la specifica [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC Windows con un microfono funzionante.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition o versione successiva.
* Carico di lavoro **Sviluppo per multipiattaforma .NET Core** in Visual Studio. È possibile abilitarlo in **Strumenti** \> **Scarica strumenti e funzionalità**.

  ![Abilitare lo sviluppo multipiattaforma .NET Core](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Creare una nuova applicazione console Visual C# .NET Core in Visual Studio 2017. Nella finestra di dialogo **Nuovo progetto**, nel riquadro a sinistra, espandere **Installato** \> **Visual C#** \> **.NET Core** e selezionare **App console (.NET Core)**. Per il nome del progetto, immettere *helloworld*.

    ![Creare App console Visual C# (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Creare App console Visual C# (.NET Core)")

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Fare clic con il pulsante destro del mouse su Gestisci pacchetti NuGet per la soluzione](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **Nuget.org**. Cercare e installare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Installare pacchetto NuGet")

1. Accettare la licenza nella finestra di dialogo visualizzata.

    ![Accettare le condizioni di licenza](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accettare le condizioni di licenza")

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `Program.cs` nel progetto Visual Studio e sostituire tutto il codice in questo file come indicato di seguito.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Compilazione riuscita](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilazione riuscita")

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

    ![Avviare l'app durante il debug](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Avviare l'app durante il debug")

1. Si apre una finestra della console che chiede di dire qualcosa (in inglese). Il testo riconosciuto viene quindi visualizzato nella stessa finestra.

    ![Risultato sulla console dopo il riconoscimento efficace](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Risultato sulla console dopo il riconoscimento efficace")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Passaggi successivi

- [Traduzione vocale](how-to-translate-speech.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
