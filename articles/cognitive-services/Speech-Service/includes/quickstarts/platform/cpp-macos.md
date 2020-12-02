---
title: 'Guida introduttiva: Configurazione della piattaforma per C++ (macOS) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per C++ in macOS con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188249"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per C++ su macOS 10.13 e versioni successive.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisiti di sistema

macOS 10.13 e versioni successive

## <a name="install-speech-sdk"></a>Installare Speech SDK

1. Scegliere una directory in cui estrarre i file di Speech SDK e impostare la `SPEECHSDK_ROOT` variabile di ambiente in modo che punti a tale directory. La variabile semplifica il riferimento alla directory nei comandi futuri. Ad esempio, se si desidera usare la directory `speechsdk` nella home directory, usare un comando simile al seguente:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Creare la directory se non esiste.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Scaricare ed estrarre l'archivio `.zip` contenente il framework di Speech SDK:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Convalidare il contenuto della directory di primo livello del pacchetto estratto:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   La visualizzazione directory dovrebbe contenere l'avviso di terze parti e i file della licenza, oltre a una directory `MicrosoftCognitiveServicesSpeech.framework`.

Ora è possibile procedere ai [Passaggi successivi](#next-steps) qui sotto.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
