---
title: Effettuare il provisioning di dispositivi Linux nella soluzione di monitoraggio remoto in C - Azure | Microsoft Docs
description: "Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e4ed662c4dbe6da05582426f56e087020c05c6b8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico alla soluzione preconfigurata di monitoraggio remoto.

## <a name="create-a-c-client-project-on-linux"></a>Creare un progetto client C in Linux

Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione del dispositivo è scritto in C. In questa esercitazione si compilerà l'applicazione in un computer che esegue Ubuntu (Linux).

Per completare questi passaggi, è necessario un dispositivo che esegue Ubuntu 15.04 o versioni successive. Prima di continuare, installare i pacchetti dei prerequisiti nel dispositivo Ubuntu usando il comando seguente:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Installare le librerie client sul dispositivo

Le librerie client dell'hub IoT di Azure sono disponibili in forma di pacchetto da installare sul dispositivo Ubuntu tramite il comando **apt-get** . Completare la procedura seguente per installare il pacchetto che contiene la libreria del client dell'hub IoT e i file d'intestazione nel computer Ubuntu:

1. In una shell, aggiungere il repository AzureIoT al computer:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Installare il pacchetto azure-iot-sdk-c-dev

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Installare il parser JSON Parson

Le librerie client di hub IoT usano il parser JSON per analizzare il payload dei messaggi. In una cartella appropriata nel computer, clonare il repository Parson GitHub usando il comando seguente:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Preparare il progetto

Nel computer Ubuntu creare una cartella denominata `remote_monitoring`. Nella cartella `remote_monitoring`:

- Creare i quattro file `main.c`, `remote_monitoring.c`, `remote_monitoring.h` e `CMakeLists.txt`.
- Creare una cartella denominata `parson`.

Copiare i file `parson.c` e `parson.h` dalla copia locale del repository Parson alla cartella `remote_monitoring/parson`.

Aprire il file `remote_monitoring.c` in un editor di testo. Aggiungere le istruzioni `#include` seguenti:

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
