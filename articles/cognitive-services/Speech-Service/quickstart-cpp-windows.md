---
title: 'Guida introduttiva: Riconoscimento vocale, C++ (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in C++ in Windows Desktop con Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c795f1581ae36f100065c39cd47bc4efc564b9fe
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607895"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in C++ su Windows Desktop con Speech SDK

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-cpp-windows.md) e la [traduzione vocale](quickstart-translate-speech-cpp-windows.md).

Se si vuole, è possibile scegliere un linguaggio di programmazione e/o un ambiente diverso:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, viene creata un'applicazione console C++ per Windows. Utilizzare i Servizi cognitivi [Speech SDK](speech-sdk.md) per trascrivere il riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 o versione successiva (qualsiasi edizione).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessaria una chiave di sottoscrizione di servizi Voce. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente servizi Voce](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine *helloworld.cpp*. Sostituire tutto il codice seguente sotto l'istruzione iniziale di inclusione (`#include "stdafx.h"` o `#include "pch.h"`) con il codice seguente:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

   ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione selezionata](media/sdk/qs-cpp-windows-06-build.png)

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

   ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug selezionata](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Si apre una finestra della console che chiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto in formato testo, che viene visualizzato nella stessa finestra.

   ![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C++ su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
