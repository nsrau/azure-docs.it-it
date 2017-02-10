---
title: Connettere un dispositivo con Node.js | Documentazione Microsoft
description: "Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in Node.js."
services: 
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
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Compilare ed eseguire la soluzione di esempio node.js
1. Per clonare il repository GitHub *Microsoft Azure IoT SDK per Node.js* e installarlo nell'ambiente desktop, seguire le istruzioni riportate in [Preparare l'ambiente di sviluppo][lnk-github-prepare].
2. Dalla copia locale del repository [azure-iot-sdk-node][lnk-github-repo] copiare i due file seguenti dalla cartella device/samples in una cartella nel dispositivo:
   
   * package.json
   * remote_monitoring.js
3. Aprire il file remote-monitoring.js e cercare le variabili seguenti:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. Sostituire **[stringa di connessione dispositivo Hub IoT]** con la stringa di connessione del dispositivo. È possibile trovare i valori per nome host dell’Hub IoT, id dispositivo e chiave del dispositivo nel dashboard delle soluzioni di monitoraggio remoto. Una stringa di connessione del dispositivo ha il formato seguente:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Se il nome host dell'hub IoT è **contoso** e l'ID dispositivo è **mydevice**, la stringa di connessione sarà simile alla seguente:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. Salvare il file. Eseguire i comandi seguenti al prompt dei comandi nella cartella che contiene i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio:
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


