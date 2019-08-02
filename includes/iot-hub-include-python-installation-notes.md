---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640438"
---
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* **Windows**

    * [Python 2. x o 3. x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python](https://pip.pypa.io/en/stable/installing/).

    * Se si usa il sistema operativo Windows, assicurarsi di avere la versione corretta di [ C++ Visual Redistributable Package](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) per consentire l'uso di DLL native da Python. Si consiglia di usare la versione più recente.

    * Se necessario, installare il pacchetto [Azure-iothub-Device-client](https://pypi.org/project/azure-iothub-device-client/) usando il comando`pip install azure-iothub-device-client`

    * Se necessario, installare il pacchetto [Azure-iothub-Service-client](https://pypi.org/project/azure-iothub-service-client/) usando il comando`pip install azure-iothub-service-client`

* **Mac OS**

    Per Mac OS sono necessari Python 3.7.0 (o 2,7) + libboost-1,67 + CURL 7.61.1 (tutti installati tramite Homebrew). Eventuali altre distribuzioni/sistemi operativi probabilmente incorporano versioni diverse di Boost & dipendenze che non funzioneranno e comporteranno un ImportError in fase di esecuzione.

    I pacchetti *pip* per `azure-iothub-service-client` e `azure-iothub-device-client` sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .

> [!NOTE]
> Sono stati rilevati diversi report di errori durante l'importazione di iothub_client in un esempio. Per ulteriori informazioni sulla gestione dei problemi di **ImportError** , vedere la pagina relativa [alla gestione dei problemi di ImportError](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
