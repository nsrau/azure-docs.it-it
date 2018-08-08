---
title: 'Guida introduttiva: Riconoscimento vocale in C++ su Linux con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in C++ su Linux con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 8f794f1d29b5fde9acd43fa4a018ccb39a8ccd25
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324779"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C++ su Linux con Speech SDK

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione console C++ su Linux (Ubuntu 16.04) usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC Ubuntu 16.04 con un microfono funzionante.
* Per installare i pacchetti necessari per compilare ed eseguire questo esempio eseguire il comando seguente:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Ottenere Speech SDK

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `0.5.0`.

Speech SDK di Servizi cognitivi per Linux è disponibile per la compilazione di applicazioni a 64 bit e a 32 bit.
I file necessari possono essere scaricati come un file con estensione tar da https://aka.ms/csspeech/linuxbinary.
Scaricare e installare SDK come indicato di seguito:

1. Selezionare una directory (percorso assoluto) in cui si desidera posizionare le intestazioni e i file binari di Speech SDK.
   Ad esempio, selezionare il percorso `speechsdk` sotto la home directory:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Creare la directory se non esiste:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Scaricare ed estrarre l'archivio `.tar.gz` con i file binari di Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Convalidare il contenuto della directory di primo livello del pacchetto estratto:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Dovrebbero essere presenti le comunicazioni relative a terze parti e i file di licenza, nonché una directory `include` per le intestazioni e una directory `lib` per le librerie.

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aggiungere il codice seguente in un file denominato `helloworld.cpp`:

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="building"></a>Compilazione

> [!NOTE]
> Assicurarsi di copiare e incollare i comandi di compilazione qui di seguito come una _singola riga_.

* Eseguire il comando seguente per compilare l'applicazione su un computer **x64**:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Eseguire il comando seguente per compilare l'applicazione su un computer **x86**:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Configurare il percorso di libreria del caricatore in modo da puntare alla libreria Speech SDK.

   * Su un computer **x64** eseguire:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Su un computer **x86** eseguire:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Eseguire l'applicazione come indicato di seguito:

   ```sh
   ./helloworld
   ```

1. Verrà visualizzato un output simile al seguente:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/cpp-linux`.

## <a name="next-steps"></a>Passaggi successivi

* [Scaricare gli esempi](speech-sdk.md#get-the-samples)
