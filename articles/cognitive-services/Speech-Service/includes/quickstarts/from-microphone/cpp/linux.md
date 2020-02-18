---
title: 'Avvio rapido: Riconoscimento vocale da un microfono, C++ (Linux) - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: f63105e97f447bd3d536b5de2908fbc830f903d4
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156034"
---
## <a name="prerequisites"></a>Prerequisites

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=linux)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Creare un file di origine C++ denominato `helloworld.cpp`e incollare il codice seguente al suo interno.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. Nel nuovo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione del Servizio di riconoscimento vocale.

1. Sostituire la stringa `YourServiceRegion` con il "Parametro Speech SDK" dell'[area](https://aka.ms/speech/sdkregion) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione della versione di valutazione gratuita.

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

1. Nella finestra della console, viene visualizzato un prompt che richiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nella stessa finestra.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
