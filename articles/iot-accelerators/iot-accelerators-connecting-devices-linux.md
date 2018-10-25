---
title: Effettuare il provisioning di dispositivi Linux nella soluzione di monitoraggio remoto in C - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo all'acceleratore di soluzioni di monitoraggio remoto con un'applicazione scritta in C in esecuzione in Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 5faa91f054e62e2b3d9d317efe57f2d3f659cee6
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829835"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Connettere il dispositivo all'acceleratore di soluzioni di monitoraggio remoto (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico all'acceleratore di soluzioni di monitoraggio remoto.

Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione del dispositivo è scritto in C. In questa esercitazione si compilerà l'applicazione in un computer che esegue Ubuntu (Linux).

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi in questa guida pratica, è necessario un dispositivo che esegue Ubuntu 15.04 o versioni successive. Prima di continuare, [configurare l'ambiente di sviluppo di Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
