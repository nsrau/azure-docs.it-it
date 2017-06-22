---
title: Connettere un dispositivo tramite C in Windows | Documentazione Microsoft
description: "Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3536777690a9b00ded7c7fdf4d5f39638dad71b0
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Creare una soluzione di esempio C in Windows
La procedura seguente illustra come creare un'applicazione client che comunica con la soluzione di monitoraggio remoto preconfigurata. Questa applicazione è scritta in C e compilata ed eseguita in Windows.

Creare un progetto iniziale in Visual Studio 2015 o Visual Studio 2017 e aggiungere i pacchetti NuGet del client dispositivo hub IoT:

1. In Visual Studio creare un'applicazione console C usando il modello **Applicazione console Win32** di Visual C++ . Assegnare al progetto il nome **RMDevice**.
2. Nella pagina **Impostazioni applicazione** della **Creazione guidata applicazione Win32** verificare che l'opzione **Applicazione console** sia selezionata e deselezionare **Intestazione precompilata** e **Controlli Security Development Lifecycle (SDL)**.
3. In **Esplora soluzioni**eliminare i file stdafx.h, targetver.h e stdafx.cpp.
4. In **Esplora soluzioni**rinominare il file RMDevice.cpp in RMDevice.c.
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice** e quindi scegliere **Gestisci pacchetti NuGet**. Fare clic su **Sfoglia**, quindi cercare e installare i seguenti pacchetti NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. In **Esplora soluzioni** fare clic sul progetto **RMDevice** e quindi scegliere **Proprietà** per aprire la finestra di dialogo **Pagine** delle proprietà del progetto. Per informazioni dettagliate, vedere [Setting Visual C++ Project Properties][lnk-c-project-properties] (Impostazione delle proprietà dei progetti Visual C++). 
7. Fare clic sulla cartella **Linker**, quindi fare clic sulla pagina delle proprietà **Input**.
8. Aggiungere **crypt32.lib** alla proprietà **Dipendenze aggiuntive**. Fare clic su **OK** e quindi scegliere nuovamente **OK** per salvare i valori delle proprietà del progetto.

Aggiungere la libreria Parson JSON al progetto **RMDevice** e aggiungere le istruzioni `#include` richieste:

1. In una cartella appropriata nel computer, clonare il repository Parson GitHub usando il comando seguente:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Copiare i file parson.h e parson.c dalla copia locale del repository Parson alla cartella del progetto **RMDevice**.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **RMDevice**, scegliere **Aggiungi** e quindi **Elemento esistente**.

1. Nella finestra di dialogo **Aggiungi elemento esistente**, selezionare i file parson.h e parson.c file nella cartella di progetto **RMDevice**. Quindi fare clic su **Aggiungi** per aggiungere i due file al progetto.

1. In Visual Studio aprire il file RMDevice.c. Sostituire le istruzioni `#include` esistenti con il codice seguente:
   
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

    > [!NOTE]
    > Compilando il progetto è possibile verificare che siano state impostate le dipendenze corrette.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Aggiungere codice per richiamare la funzione **remote\_monitoring\_run** e quindi creare ed eseguire l'applicazione del dispositivo.

1. Sostituire la funzione **main** con il codice seguente per richiamare la funzione **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Fare clic su **Compila** e quindi su **Compila soluzione** per compilare l'applicazione del dispositivo.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice**, scegliere **Debug** e quindi fare clic su **Avvia nuova istanza** per eseguire l'esempio. Nella console vengono visualizzati i messaggi mano a mano che l'applicazione invia i dati di telemetria di esempio alla soluzione preconfigurata, riceve i valori delle proprietà desiderate impostate nel dashboard della soluzione e risponde ai metodi richiamati dal dashboard della soluzione.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

