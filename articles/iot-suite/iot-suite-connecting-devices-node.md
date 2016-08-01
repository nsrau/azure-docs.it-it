<properties
   pageTitle="Connettere un dispositivo con Node.js | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Node.js)

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

    Se il nome host dell'Hub IoT è **contoso** e l'id dispositivo è **mydevice**, la stringa di connessione sarà simile alla seguente:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Salvare il file. Eseguire i comandi seguenti al prompt dei comandi nella cartella che contiene i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

<!---HONumber=AcomDC_0720_2016-->