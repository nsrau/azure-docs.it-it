---
title: Effettuare il provisioning di dispositivi Windows nella soluzione di monitoraggio remoto in C - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo all'acceleratore di soluzioni di monitoraggio remoto con un'applicazione scritta in C in esecuzione in Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 55c8ff799ba3ff7fe9691d46dc90a00d5182d390
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829411"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Connettere il dispositivo all'acceleratore di soluzioni di monitoraggio remoto (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico all'acceleratore di soluzioni di monitoraggio remoto.

Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione per dispositivi è scritto in C. In questa esercitazione si compilerà l'applicazione client per dispositivi in un computer che esegue Windows.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura in questa guida pratica seguire i passaggi descritti in [Configurare l'ambiente di sviluppo Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) per aggiungere le librerie e gli strumenti di sviluppo necessari per il computer Windows in uso.

## <a name="view-the-code"></a>Visualizzare il codice

Il [codice di esempio](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) usato in questa guida è disponibile nel repository GitHub degli SDK di Azure IoT per C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Eseguire il download del codice sorgente e preparare il progetto

Per preparare il progetto, clonare o scaricare il [repository degli SDK di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) da GitHub.

L'esempio è disponibile nella cartella **samples/solutions/remote_monitoring_client**.

In un editor di testo aprire il file **remote_monitoring.c** nella cartella **samples/solutions/remote_monitoring_client**.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Modificare il file **remote_monitoring.c** per sostituire `<connectionstring>` con la stringa di connessione del dispositivo annotata all'inizio di questa guida pratica, al momento dell'aggiunta di un dispositivo all'acceleratore della soluzione.

1. Seguire i passaggi descritti nell'argomento relativo alla [compilazione dell'SDK C in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) per compilare l'SDK e l'applicazione client di monitoraggio remoto.

1. Al prompt dei comandi usato per compilare la soluzione, eseguire:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    La console visualizza messaggi relativi alle operazioni seguenti:

    - L'applicazione invia dati di telemetria di esempio all'acceleratore della soluzione.
    - Risponde ai metodi richiamati dal dashboard della soluzione.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
