---
title: 'Avvio rapido: Configurazione della piattaforma per Java (Windows, Linux, macOS) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per Java (Windows, Linux, macOS) con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 5f6b994bce1d38872cffb1e6e389136742be6d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468416"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per Java 8 JRE a 64 bit.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Il pacchetto Speech SDK per Java è disponibile per i sistemi operativi seguenti:
  - Windows: solo 64 bit
  - Mac: macOS X versione 10.13 o successiva.
  - Linux: solo 64 bit su Ubuntu 16.04, Ubuntu 18.04 o Debian 9

## <a name="prerequisites"></a>Prerequisites

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (richiede Java già installato)
- Le piattaforme Linux supportate richiedono l'installazione di alcune librerie (`libssl` per il supporto di Secure Sockets Layer e `libasound2` per il supporto audio). Per i comandi necessari per installare le versioni corrette di queste librerie, identificare la propria distribuzione di seguito.

  - In Ubuntu eseguire i comandi seguenti per installare i pacchetti necessari:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - In Debian 9 eseguire i comandi seguenti per installare i pacchetti necessari:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare Windows prima di continuare con questa guida.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Creare un progetto di Eclipse e installare Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
