---
title: 'Guida introduttiva: Riconoscimento vocale, .NET Framework (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327059"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Guida introduttiva: Riconoscimento vocale con Speech SDK per .NET Framework (Windows)

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-dotnet-windows.md) e la [traduzione vocale](quickstart-translate-speech-dotnetframework-windows.md).

Se si vuole, è possibile scegliere un linguaggio di programmazione e/o un ambiente diverso:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Usare questa guida per creare un'applicazione console di riconoscimento vocale con .NET framework per Windows e Speech SDK. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.

Per una dimostrazione rapida (senza compilare il progetto di Visual Studio, come descritto in questo articolo), ottenere la versione più recente degli [esempi di Speech SDK di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-speech-sdk) da GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Accesso al microfono del computer

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire **Program.cs** e sostituire il codice generato automaticamente con questo codice di esempio:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Trovare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione dei servizi Voce.

1. Trovare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, per la sottoscrizione di valutazione gratuita l'area è `westus`.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o selezionare **F5** per avviare l'applicazione **helloworld**.

1. Pronunciare una frase o un'espressione nel microfono del dispositivo. L'applicazione trasmette il parlato ai servizi Voce che inviano il testo trascritto all'applicazione per la visualizzazione.

   ![Interfaccia utente di riconoscimento vocale](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Eseguire il training di un modello per Riconoscimento vocale personalizzato](how-to-custom-speech-train-model.md)
