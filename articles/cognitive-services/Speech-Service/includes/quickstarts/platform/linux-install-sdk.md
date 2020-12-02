---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188309"
---
## <a name="install-speech-sdk"></a>Installare Speech SDK

Speech SDK per Linux è utilizzabile per compilare applicazioni sia a 64 bit che a 32 bit. Le librerie necessarie e i file di intestazione possono essere scaricati come file tar da https://aka.ms/csspeech/linuxbinary.

Scaricare e installare SDK come indicato di seguito:

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

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]