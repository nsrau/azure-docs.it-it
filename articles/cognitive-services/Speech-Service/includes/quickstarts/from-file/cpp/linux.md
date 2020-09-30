---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 18e3f4a7564eb59052cd7deff32fe022dc89a017
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377357"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Creare un file di origine C++ denominato `helloworld.cpp`e incollare il codice seguente al suo interno.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Nel nuovo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione del Servizio di riconoscimento vocale.

1. Sostituire la stringa `YourServiceRegion` con l'**identificatore di area** corrispondente all'[area](https://aka.ms/speech/sdkregion) associata alla sottoscrizione.

1. Sostituire la stringa `whatstheweatherlike.wav` con il proprio nome file.

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="build-the-app"></a>Compilare l'app

> [!NOTE]
> Assicurarsi di immettere i comandi seguenti come una_riga di comando singola_. Il modo più semplice per farlo consiste nel copiare il comando usando il pulsante **Copia** accanto a ogni comando e quindi incollarlo nel prompt di shell.

* In un sistema **x64**  (64 bit), eseguire il comando seguente per compilare l'applicazione.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* In un sistema **x86** (32 bit), eseguire il comando seguente per compilare l'applicazione.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* In un sistema **ARM64** (64 bit), eseguire il comando seguente per compilare l'applicazione.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Eseguire l'app

1. Configurare il percorso di libreria del caricatore in modo da puntare alla libreria Speech SDK.

   * In un sistema**x64** (64 bit), immettere il comando seguente.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * In un sistema **x86** (32 bit), immettere questo comando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * In un sistema**ARM64** (64 bit), immettere il comando seguente.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
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

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
