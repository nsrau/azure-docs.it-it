<properties
	pageTitle="Simulazione di un dispositivo con SDK per gateway | Microsoft Azure "
	description="Procedura dettagliata di Azure IoT SDK per gateway con Windows che illustra l'invio di dati di telemetria da un dispositivo simulato con Azure IoT SDK per gateway."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# IoT SDK per gateway (beta): invio di messaggi da dispositivo a cloud con un dispositivo simulato usando Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Compilare ed eseguire l'esempio

Prima di iniziare:

- [Configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Windows.
- [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha ancora una sottoscrizione di Azure, è possibile creare un [account gratuito][lnk-free-trial].
- Aggiungere due dispositivi all'hub IoT e annotare i relativi ID e le chiavi di dispositivo. È possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per aggiungere i dispositivi all'hub IoT creato nel passaggio precedente e recuperarne le chiavi relative.

Per compilare l'esempio:

1. Aprire un **prompt dei comandi per gli sviluppatori per VS2015**.
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk**.
3. Eseguire lo script **tools\\build.cmd**. Questo script crea un file di soluzione Visual Studio, compila la soluzione ed esegue i test. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**.

Per eseguire l'esempio:

In un editor di testo aprire il file **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** nella copia locale del repository **azure-iot-gateway-sdk**. Questo file consente di configurare i moduli nel gateway di esempio:

- Il modulo **IoTHub** si connette all'hub IoT. È necessario configurarlo per l'invio di dati all'hub IoT. In particolare, impostare il valore di **IoTHubName** sul nome dell'hub IoT e impostare il valore di **IoTHubSuffix** su **azure-devices.net**.
- Il modulo **mapping** esegue il mapping degli indirizzi MAC dei dispositivi simulati sugli ID dispositivo dell'hub IoT. Assicurarsi che i valori di **deviceId** corrispondano agli ID dei due dispositivi aggiunti all'hub IoT e che i valori di **deviceKey** contengano le chiavi dei due dispositivi.
- I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi MAC corrispondono a quelli nel modulo **mapping**.
- Il modulo **Logger** registra l'attività del gateway in un file.
- I valori di **module path** illustrati di seguito presuppongono che sia stato clonato il repository SDK per gateway nella directory principale dell'unità **C:**. Se il repository è stato scaricato in un altro percorso, è necessario rettificare di conseguenza i valori di **module path**.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\iothubhttp\\Debug\\iothubhttp_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\identitymap\\Debug\\identitymap_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\ble_fake\\Debug\\ble_fake_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\ble_fake\\Debug\\ble_fake_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}
```

Salvare le modifiche apportate al file di configurazione.

Per eseguire l'esempio:

1. Al prompt dei comandi, accedere alla directory principale della copia locale del repository **azure-iot-gateway-sdk**.
2. Eseguire il comando seguente:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. È possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che l'hub IoT riceve dal gateway.


## Passaggi successivi

Per informazioni su come usare l'SDK per gateway, vedere la pagina sugli [Azure IoT SDK per gateway][lnk-gateway-sdk] in GitHub.

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

<!---HONumber=AcomDC_0608_2016-->