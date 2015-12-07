<properties
   pageTitle="Connettere un dispositivo con Node.js | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in Node.js."
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilare ed eseguire la soluzione di esempio node.js

1. Per clonare l’archivio GitHub *SDK di Microsoft Azure IoT* e installare l’*SDK per dispositivi Microsoft Azure IoT per Node.js* nel proprio ambiente desktop di Windows, seguire le istruzioni di [Preparare l’ambiente di sviluppo Windows][lnk-github-prepare].

2. Da una copia locale dell’archivio [azure-iot-sdks][lnk-github-repo], copiare i seguenti due file dalla cartella node/device/samples in una cartella sul dispositivo:

  - packages.json
  - remote\_monitoring.js

3. Aprire il file remote-monitoring.js e cercare le variabili seguenti:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Sostituire **[stringa di connessione dispositivo Hub IoT]** con la stringa di connessione del dispositivo. È possibile trovare i valori per nome host dell’Hub IoT, id dispositivo e chiave del dispositivo nel dashboard delle soluzioni di monitoraggio remoto. Una stringa di connessione del dispositivo ha il formato seguente:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

5. Se il nome host dell'Hub IoT è **contoso** e l'id dispositivo è **mydevice**, la stringa di connessione sarà simile alla seguente:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

6. Salvare il file. Eseguire i comandi seguenti nella cartella che contiene i file:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md

<!---HONumber=AcomDC_1125_2015-->