---
title: 'Avvio rapido: Riconoscimento vocale da un file audio, C++ (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: cfe649f56ff57548f6fa711abf67f2925d74a2ea
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446597"
---
## <a name="prerequisites"></a>Prerequisites

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine **helloworld.cpp**.

1. Sostituire tutto il codice con il frammento di codice seguente:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con l'**identificatore di area** corrispondente all'[area](https://aka.ms/speech/sdkregion) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di prova gratuita.

1. Sostituire la stringa `whatstheweatherlike.wav` con il proprio nome file.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione **helloworld**.

1. Il file audio viene trasmesso al servizio Voce e la prima espressione nel file viene trascritta in testo, che viene visualizzato nella stessa finestra.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]