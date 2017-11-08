---
title: Effettuare il provisioning di un dispositivo Raspberry Pi nella soluzione di monitoraggio remoto con C - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo Raspberry Pi alla soluzione preconfigurata di monitoraggio remoto Azure IoT Suite usando un'applicazione scritta in C.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: dobett
ms.openlocfilehash: 2f5915093a0d7984f0350af21aa438cdd588bbf2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Connettere il dispositivo Raspberry Pi alla soluzione preconfigurata di monitoraggio remoto (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico alla soluzione preconfigurata di monitoraggio remoto. Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione del dispositivo Raspberry Pi è scritto in C. In questa esercitazione si compilerà l'applicazione in un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian.

### <a name="required-hardware"></a>Requisiti hardware

Un computer desktop per potersi connettere in remoto alla riga di comando in Raspberry Pi.

[Starter kit di Microsoft Azure IoT per Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) o componenti equivalenti. Questa esercitazione usa gli elementi seguenti del kit:

- Raspberry Pi 3
- Scheda microSD (con NOOBS)
- Un cavo USB mini
- Un cavo Ethernet

### <a name="required-desktop-software"></a>Software desktop necessario

È necessario un client SSH nel computer desktop per poter accedere in remoto alla riga di comando in Raspberry Pi.

- Windows non include un client SSH. È consigliabile usare [PuTTY](http://www.putty.org/).
- La maggior parte delle distribuzioni Linux e Mac OS includono l'utilità SSH della riga di comando. Per altre informazioni, vedere [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH con Linux o Mac OS).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessario

I passaggi seguenti mostrano come preparare il dispositivo Raspberry Pi per compilare un'applicazione C da connettere alla soluzione preconfigurata:

1. Connettersi al dispositivo Raspberry Pi con `ssh`. Per altre informazioni, vedere [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) nel [sito Web Raspberry Pi](https://www.raspberrypi.org/).

1. Usare il comando seguente per aggiornare Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Usare il comando seguente per aggiungere le librerie e gli strumenti di sviluppo necessari al dispositivo Raspberry Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. Usare i comandi seguenti per installare le librerie client dell'hub IoT:

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. Clonare il parser Parson JSON nel dispositivo Raspberry Pi usando i comandi seguenti:

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>Creare un progetto

Completare i passaggi seguenti usando la connessione `ssh` al dispositivo Raspberry Pi:

1. Creare una cartella denominata `remote_monitoring` nella cartella principale nel dispositivo Raspberry Pi. Passare a questa cartella nella riga di comando:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Creare i quattro file `main.c`, `remote_monitoring.c`, `remote_monitoring.h` e `CMakeLists.txt` nella cartella `remote_monitoring`.

1. Creare una cartella denominata `parson` nella cartella `remote_monitoring`.

1. Copiare i file `parson.c` e `parson.h` dalla copia locale del repository Parson alla cartella `remote_monitoring/parson`.

1. Aprire il file `remote_monitoring.c` in un editor di testo. Nel dispositivo Raspberry Pi è possibile usare l'editor di testo `nano` O `vi`. Aggiungere le istruzioni `#include` seguenti:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Aggiungere il codice per eseguire l'app

Aprire il file `remote_monitoring.h` in un editor di testo. Aggiungere il codice seguente:

```c
void remote_monitoring_run(void);
```

Aprire il file `main.c` in un editor di testo. Aggiungere il codice seguente:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

La procedura seguente descrive i metodi d'uso di *CMake* per compilare l'applicazione client.

1. Aprire il file **CMakeLists.txt** nella cartella `remote_monitoring` in un editor di testo.

1. Aggiungere le istruzioni seguenti per definire la modalità di compilazione dell'applicazione client:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. Nella cartella `remote_monitoring` creare una cartella per archiviare i file *make* generati da CMake. Eseguire quindi i comandi **cmake** e **make** in questo modo:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Eseguire l'applicazione client e inviare dati di telemetria all'hub IoT:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
