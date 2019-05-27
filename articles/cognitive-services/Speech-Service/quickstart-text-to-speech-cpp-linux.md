---
title: 'Guida introduttiva: Eseguire la sintesi vocale, C++ (Linux) - Servizi Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in C++ su Linux con Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: b5b768e52a40652840e9e3d3069df8a9c895f77b
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001612"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Guida introduttiva: Eseguire la sintesi vocale in C++ su Linux con Speech SDK

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-cpp-linux.md).

In questo articolo viene creata un'applicazione console C++ per Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). È possibile usare [Speech SDK](speech-sdk.md) di Servizi cognitivi per eseguire la sintesi vocale in tempo reale e riprodurre la voce dall'altoparlante del PC. L'applicazione viene compilata con il [Speech SDK per Linux](https://aka.ms/csspeech/linuxbinary) e il compilatore C++ della distribuzione di Linux (ad esempio, `g++`).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessaria una chiave di sottoscrizione di servizi Voce. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente il servizio Voce](get-started.md).

## <a name="install-speech-sdk"></a>Installare Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.5.1`.

Speech SDK per Linux è utilizzabile per compilare applicazioni sia a 64 bit che a 32 bit. Le librerie necessarie e i file di intestazione possono essere scaricati come file tar da https://aka.ms/csspeech/linuxbinary.

Scaricare e installare SDK come indicato di seguito:

1. Assicurarsi che le dipendenze SDK siano installate.

   * In Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * In Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Scegliere una directory in cui estrarre i file di Speech SDK e impostare la `SPEECHSDK_ROOT` variabile di ambiente in modo che punti a tale directory. La variabile semplifica il riferimento alla directory nei comandi futuri. Ad esempio, se si desidera usare la directory `speechsdk` nella home directory, usare un comando simile al seguente:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Creare la directory se non esiste.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Scaricare ed estrarre l'`.tar.gz`archivio con i file binari di Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Convalidare il contenuto della directory di primo livello del pacchetto estratto:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   L'elenco di directory deve contenere la notifica di terzi e i file di licenza, nonché una `include` directory contenente i file (`.h`) di intestazione e una `lib` directory contenente le librerie.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Aggiungere codice di esempio

1. Creare un file di origine C++ denominato `helloworld.cpp`e incollare il codice seguente al suo interno.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. Nel nuovo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei Servizi di riconoscimento vocale.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

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

## <a name="run-the-app"></a>Esecuzione dell'app

1. Configurare il percorso di libreria del caricatore in modo da puntare alla libreria Speech SDK.

   * In un sistema**x64** (64 bit), immettere il comando seguente.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * In un sistema **x86** (32 bit), immettere questo comando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Eseguire l'applicazione.

   ```sh
   ./helloworld
   ```

1. Nella finestra della console viene visualizzato un prompt che chiede di digitare un testo. Digitare alcune parole o una frase. Il testo digitato viene trasmesso ai servizi Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C++ su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
