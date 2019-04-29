---
title: Effettuare il provisioning di un dispositivo Raspberry Pi nella soluzione di monitoraggio remoto con C - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo Raspberry Pi all'acceleratore di soluzioni di monitoraggio remoto con un'applicazione scritta in C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61454501"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Connettere il dispositivo Raspberry Pi all'acceleratore di soluzioni di monitoraggio remoto (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo reale all'acceleratore di soluzioni di monitoraggio remoto. Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione del dispositivo Raspberry Pi è scritto in C. In questa esercitazione si compilerà l'applicazione in un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian.

Se si preferisce simulare un dispositivo, vedere [Creare e testare un nuovo dispositivo simulato](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Requisiti hardware

Un computer desktop per potersi connettere in remoto alla riga di comando in Raspberry Pi.

[Starter kit di Microsoft Azure IoT per Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) o componenti equivalenti. Questa esercitazione usa gli elementi seguenti del kit:

- Raspberry Pi 3
- Scheda microSD (con NOOBS)
- Un cavo USB mini
- Un cavo Ethernet

### <a name="required-desktop-software"></a>Software desktop necessario

È necessario un client SSH nel computer desktop per poter accedere in remoto alla riga di comando in Raspberry Pi.

- Windows non include un client SSH. È consigliabile usare [PuTTY](https://www.putty.org/).
- La maggior parte delle distribuzioni Linux e Mac OS includono l'utilità SSH della riga di comando. Per altre informazioni, vedere [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH con Linux o Mac OS).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessario

In questo articolo si presuppone che sia stata installata la versione più recente del [sistema operativo Raspbian OS nel dispositivo Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

I passaggi seguenti mostrano come preparare il dispositivo Raspberry Pi per compilare un'applicazione C da connettere all'acceleratore di soluzioni:

1. Connettersi al dispositivo Raspberry Pi con **ssh**. Per altre informazioni, vedere [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) nel [sito Web Raspberry Pi](https://www.raspberrypi.org/).

1. Usare il comando seguente per aggiornare Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Per completare la procedura in questa guida pratica, seguire i passaggi per [configurare l'ambiente di sviluppo Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) e aggiungere le librerie e gli strumenti di sviluppo necessari a Raspberry Pi.

## <a name="view-the-code"></a>Visualizzare il codice

Il [codice di esempio](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) usato in questa guida è disponibile nel repository GitHub degli SDK di Azure IoT per C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Eseguire il download del codice sorgente e preparare il progetto

Per preparare il progetto, clonare o scaricare il [repository degli SDK di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) da GitHub.

L'esempio è disponibile nella cartella **samples/solutions/remote_monitoring_client**.

In un editor di testo aprire il file **remote_monitoring.c** nella cartella **samples/solutions/remote_monitoring_client**.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

La procedura seguente descrive i metodi d'uso di *CMake* per creare l'applicazione client. L'applicazione client di monitoraggio remoto viene creata nell'ambito del processo di compilazione per l'SDK.

1. Modificare il file **remote_monitoring.c** per sostituire `<connectionstring>` con la stringa di connessione del dispositivo annotata all'inizio di questa guida pratica, al momento dell'aggiunta di un dispositivo all'acceleratore della soluzione.

1. Passare alla radice della copia clonata del [repository degli SDK di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) ed eseguire i comandi seguenti per creare l'applicazione client:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Eseguire l'applicazione client e inviare dati di telemetria all'hub IoT:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    La console visualizza messaggi relativi alle operazioni seguenti:

    - L'applicazione invia dati di telemetria di esempio all'acceleratore della soluzione.
    - Risponde ai metodi richiamati dal dashboard della soluzione.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
