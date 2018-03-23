---
title: Effettuare il provisioning di dispositivi Windows nella soluzione di monitoraggio remoto in C - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Windows.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 83d0427a3ba8c634699608c38ab22efb1f275e52
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico alla soluzione preconfigurata di monitoraggio remoto.

## <a name="create-a-c-client-solution-on-windows"></a>Creare una soluzione client C in Windows

Come per la maggior parte delle applicazioni incorporate in esecuzione su dispositivi vincolati, il codice client per l'applicazione del dispositivo è scritto in C. In questa esercitazione si compilerà l'applicazione in un computer che esegue Windows.

### <a name="create-the-starter-project"></a>Creare il progetto iniziale

Creare un progetto iniziale in Visual Studio 2017 e aggiungere i pacchetti NuGet del client dispositivo dell'hub IoT:

1. In Visual Studio creare un'applicazione console C usando il modello **Applicazione console Windows** di Visual C++. Assegnare al progetto il nome **RMDevice**.

    ![Creare un'applicazione console Windows in Visual C++](media/iot-suite-connecting-devices/visualstudio01.png)

1. In **Esplora soluzioni**eliminare i file `stdafx.h`, `targetver.h` e `stdafx.cpp`.

1. In **Esplora soluzioni**rinominare il file `RMDevice.cpp` in `RMDevice.c`.

    ![Esplora soluzioni mostra il file RMDevice.c rinominato](media/iot-suite-connecting-devices/visualstudio02.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice** e quindi scegliere **Gestisci pacchetti NuGet**. Scegliere **Sfoglia**, quindi cercare e installare i pacchetti NuGet seguenti:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![Gestione pacchetti NuGet mostra i pacchetti Microsoft.Azure.IoTHub installati](media/iot-suite-connecting-devices/visualstudio03.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice** e quindi scegliere **Proprietà** per aprire la finestra di dialogo **Pagine delle proprietà** del progetto. Per informazioni dettagliate, vedere [Setting Visual C++ Project Properties](https://docs.microsoft.com/cpp/ide/working-with-project-properties) (Impostazione delle proprietà dei progetti Visual C++).

1. Scegliere la cartella **C/C++** e quindi la pagina delle proprietà **Intestazioni precompilate**.

1. Impostare **Intestazione precompilata** su **Senza intestazioni precompilate**. Scegliere quindi **Applica**.

    ![Proprietà del progetto che mostrano l'opzione Senza intestazioni precompilate](media/iot-suite-connecting-devices/visualstudio04.png)

1. Scegliere la cartella **Linker** e quindi fare clic sulla pagina delle proprietà **Input**.

1. Aggiungere `crypt32.lib` alla proprietà **Dipendenze aggiuntive**. Per salvare i valori delle proprietà del progetto, scegliere **OK** e quindi di nuovo **OK**.

    ![Proprietà del progetto che mostrano la cartella Linker che include crypt32.lib](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Installare la raccolta JSON Parson

Aggiungere la libreria Parson JSON al progetto **RMDevice** e aggiungere le istruzioni `#include` richieste:

1. In una cartella appropriata nel computer, clonare il repository Parson GitHub usando il comando seguente:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Copiare i file `parson.h` e `parson.c` dalla copia locale del repository Parson alla cartella del progetto **RMDevice**.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **RMDevice**, scegliere **Aggiungi** e quindi **Elemento esistente**.

1. Nella finestra di dialogo **Aggiungi elemento esistente** selezionare i file `parson.h` e `parson.c` nella cartella del progetto **RMDevice**. Per aggiungere questi due file al progetto, scegliere **Aggiungi**.

    ![Esplora soluzioni mostra i file parson.h e parson.c](media/iot-suite-connecting-devices/visualstudio06.png)

1. In Visual Studio aprire il file `RMDevice.c`. Sostituire le istruzioni `#include` esistenti con il codice seguente:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > È ora possibile verificare che per il progetto siano configurate le dipendenze corrette compilando la soluzione.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Aggiungere il codice per richiamare la funzione **remote\_monitoring\_run** e quindi compilare ed eseguire l'applicazione del dispositivo:

1. Per richiamare la funzione **remote\_monitoring\_run**, sostituire la funzione **main** con il codice seguente:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Scegliere **Compila** e quindi **Compila soluzione** per compilare l'applicazione del dispositivo.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice**, scegliere **Debug** e quindi fare clic su **Avvia nuova istanza** per eseguire l'esempio. La console visualizza messaggi relativi alle operazioni seguenti:

    * L'applicazione invia dati di telemetria di esempio alla soluzione preconfigurata.
    * Riceve i valori di proprietà desiderati impostati nel dashboard della soluzione.
    * Risponde ai metodi richiamati dal dashboard della soluzione.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
