---
title: 'Avvio rapido: Riconoscimento vocale da un file audio, C++ (macOS) - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: 622087a914b77712df850d4200ac381439f3bd85
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446675"
---
## <a name="prerequisites"></a>Prerequisites

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Creare un file di origine C++ denominato `helloworld.cpp`e incollare il codice seguente al suo interno.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Nel nuovo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione del Servizio di riconoscimento vocale.

1. Sostituire la stringa `YourServiceRegion` con l'**identificatore di area** corrispondente all'[area](https://aka.ms/speech/sdkregion) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di prova gratuita.

1. Sostituire la stringa `whatstheweatherlike.wav` con il proprio nome file.

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="build-the-app"></a>Compilare l'app

> [!NOTE]
> Assicurarsi di immettere i comandi seguenti come una_riga di comando singola_. Il modo più semplice per farlo consiste nel copiare il comando usando il pulsante **Copia** accanto a ogni comando e quindi incollarlo nel prompt di shell.

* Eseguire il comando seguente per compilare l'applicazione.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Eseguire l'app

1. Configurare il percorso di libreria del caricatore in modo da puntare alla libreria Speech SDK.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Eseguire l'applicazione.

   ```sh
   ./helloworld
   ```

1. Il file audio viene trasmesso al servizio Voce e la prima espressione nel file viene trascritta in testo, che viene visualizzato nella stessa finestra.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
