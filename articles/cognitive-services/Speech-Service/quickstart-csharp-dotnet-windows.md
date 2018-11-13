---
title: 'Guida introduttiva: Riconoscere e trascrivere contenuti vocali, .NET Framework (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 2dd001ebebd5cdf90d7d0b8163a85a5f83c855f6
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281295"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>Guida introduttiva: Riconoscere e trascrivere contenuti vocali usando Speech SDK e .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.

Per questa guida introduttiva è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con Microsoft Voce abilitato. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione per il servizio Voce
* Accesso al microfono del computer

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Apri `Program.cs` e sostituire il codice generato automaticamente con questo codice di esempio:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Individuare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione del Servizio Voce.

1. Individuare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilazione completata")

1. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Verrà visualizzata una finestra della console in cui si chiede all'utente di parlare. Pronunciare qualche parola in italiano. Il contenuto vocale viene trasmesso servizio Voce e trascritto in tempo reale in formato testo. Il risultato viene stampato sulla console.

    ![Risultato sulla console dopo un riconoscimento riuscito](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Risultato sulla console dopo un riconoscimento riuscito")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Il codice è disponibile nella cartella `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscere le finalità dai contenuti vocali con Speech SDK per C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
