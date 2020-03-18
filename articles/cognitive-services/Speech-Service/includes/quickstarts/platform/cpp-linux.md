---
title: 'Avvio rapido: Configurazione della piattaforma per C++ (Linux) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per C++ in Linux con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383949"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisiti di sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, sono necessari gli elementi seguenti:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Le piattaforme Linux supportate richiedono l'installazione di alcune librerie (`libssl` per il supporto di Secure Sockets Layer e `libasound2` per il supporto audio). Per i comandi necessari per installare le versioni corrette di queste librerie, identificare la propria distribuzione di seguito.

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

   * In RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

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

Ora è possibile procedere ai [Passaggi successivi](#next-steps) qui sotto.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
