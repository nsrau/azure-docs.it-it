---
title: 'Guida introduttiva: Sintesi vocale, C++ (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni su come seguire la sintesi vocale in C++ su Windows Desktop con Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383094"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Guida introduttiva: Eseguire la sintesi vocale in C++ su Windows con Speech SDK

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-cpp-windows.md) e la [traduzione vocale](quickstart-translate-speech-cpp-windows.md).

In questo articolo, viene creata un'applicazione console C++ per Windows. È possibile usare [Speech SDK](speech-sdk.md) di Servizi cognitivi per eseguire la sintesi vocale in tempo reale e riprodurre la voce dall'altoparlante del PC. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019 (qualsiasi edizione).

Per un elenco completo di lingue/voci disponibili per la sintesi vocale, vedere [Supporto per le lingue](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessaria una chiave di sottoscrizione di servizi Voce. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente servizi Voce](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine **helloworld.cpp**.

1. Sostituire tutto il codice con il frammento di codice seguente:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione **helloworld**.

1. Digitare una frase o un'espressione in inglese. L'applicazione trasmette il testo ai servizi Voce, che inviano la sintesi vocale all'applicazione per la riproduzione con l'altoparlante.

   ![Output della console dopo la sintesi vocale](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per salvare il parlato in un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C++ su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](record-custom-voice-samples.md)
