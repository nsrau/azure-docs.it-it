---
title: Guida introduttiva di Speech SDK per C++ e Linux | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare Speech SDK con Linux e C++ in Servizi cognitivi.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753603"
---
# <a name="quickstart-for-c-and-linux"></a>Guida introduttiva per C++ e Linux

La versione corrente di Speech SDK di Servizi cognitivi è `0.4.0`.

Speech SDK di Servizi cognitivi per Linux è disponibile per la compilazione di applicazioni a 64 bit e a 32 bit. I file necessari possono essere scaricati come un file con estensione tar da https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Se si sta cercando una guida introduttiva a C++ e Windows, andare [qui](quickstart-cpp-windows.md).
> Se si sta cercando una guida introduttiva a C# e Windows, andare [qui](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Queste istruzioni presuppongono che Ubuntu 16.04 sia in esecuzione in un computer (x86 o x64).
> In una versione diversa di Ubuntu, o una distribuzione Linux diversa, è necessario adattare i passaggi necessari.

## <a name="prerequisites"></a>prerequisiti

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Ottenere il pacchetto binario

[!include[License Notice](includes/license-notice.md)]

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

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Codice di esempio

Il codice seguente riconosce la lingua inglese dal microfono.
Posizionarlo in un file denominato `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Sostituire la chiave di sottoscrizione nel codice con quella ottenuta.

## <a name="building"></a>Compilazione

> [!NOTE]
> Assicurarsi di copiare e incollare i comandi di compilazione qui di seguito come una _singola riga_.

* Eseguire il comando seguente per compilare l'applicazione su un computer x64:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Eseguire il comando seguente per compilare l'applicazione su un computer x86:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>In esecuzione

Per eseguire l'applicazione, è necessario configurare il percorso di libreria del caricatore in modo da puntare alla libreria Speech SDK.

* Su un computer x64 eseguire:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Su un computer x86 eseguire:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Eseguire l'applicazione come indicato di seguito:

```sh
./quickstart-linux
```

Se non ci sono errori, verrà visualizzato un output simile al seguente:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Download dell'esempio

Per l'insieme di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

* Visitare la [pagina degli esempi](samples.md) per esempi aggiuntivi.
