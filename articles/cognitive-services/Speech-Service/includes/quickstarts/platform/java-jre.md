---
title: 'Guida introduttiva: Configurazione della piattaforma per Java (Windows, Linux, macOS) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per Java (Windows, Linux, macOS) con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 142d4504ab12e7df5cc1e009038554a5b90dff0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188279"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per Java 8 JRE a 64 bit. Se si vuole solo il nome del pacchetto per iniziare autonomamente, Java SDK non è disponibile nel repository centrale di Maven. Se si usa Gradle o un file di dipendenza `pom.xml`, è necessario aggiungere un repository personalizzato che punta a `https://csspeechstorage.blob.core.windows.net/maven/` (vedere di seguito per il nome del pacchetto).

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Il pacchetto Speech SDK per Java è disponibile per i sistemi operativi seguenti:
  - Windows: solo 64 bit
  - Mac: macOS X versione 10.13 o successiva.
  - Linux: vedere l'elenco di [distribuzioni Linux e architetture di destinazione supportate](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (richiede Java già installato)
- Le piattaforme Linux supportate richiedono l'installazione di alcune librerie (`libssl` per il supporto di Secure Sockets Layer e `libasound2` per il supporto audio). Per i comandi necessari per installare le versioni corrette di queste librerie, identificare la propria distribuzione di seguito.

  - In Ubuntu/Debian eseguire i comandi seguenti per installare i pacchetti necessari:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Se libssl1.0.0 non è disponibile, installare libssl1.0.x (dove x è maggiore di 0) o libssl1.1.

  - In RHEL/CentOS eseguire i comandi seguenti per installare i pacchetti necessari:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - In RHEL/CentOS 7 seguire le istruzioni riportate in [Come configurare RHEL/CentOS 7 per Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare Windows prima di continuare con questa guida.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Creare un progetto di Eclipse e installare Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
