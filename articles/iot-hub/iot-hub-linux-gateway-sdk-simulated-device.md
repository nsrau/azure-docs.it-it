<properties
	pageTitle="Simulazione di un dispositivo con SDK per gateway | Microsoft Azure "
	description="Procedura dettagliata dell'Azure IoT SDK per gateway con Linux che illustra l'invio di dati di telemetria da un dispositivo simulato con Azure IoT SDK per gateway."
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


# IoT SDK per gateway (beta): invio di messaggi da dispositivo a cloud con un dispositivo simulato usando Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Compilare ed eseguire l'esempio

Prima di iniziare:

- [configurare l'ambiente di sviluppo][lnk-setupdevbox] per usare l'SDK in Linux.
- [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha ancora una sottoscrizione di Azure, è possibile creare un [account gratuito][lnk-free-trial].
- Aggiungere due dispositivi all'hub IoT e annotare i relativi ID e le chiavi di dispositivo. È possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per aggiungere i dispositivi all'hub IoT creato nel passaggio precedente e recuperarne le chiavi relative.

Per compilare l'esempio:

1. Aprire una shell.
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk**.
3. Eseguire lo script **tools/build.sh**. Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella directory principale della copia locale del repository **azure-iot-gateway-sdk** e generare un makefile. Lo script quindi compila la soluzione ed esegue i test.

> [AZURE.NOTE]  Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella directory principale della copia locale del repository **azure-iot-gateway-sdk**.

Per eseguire l'esempio:

In un editor di testo aprire il file **samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_lin.json** nella copia locale del repository **azure-iot-gateway-sdk**. Questo file consente di configurare i moduli nel gateway di esempio:

- Il modulo **IoTHub** si connette all'hub IoT. È necessario configurarlo per l'invio di dati all'hub IoT. In particolare, impostare il valore di **IoTHubName** sul nome dell'hub IoT e impostare il valore di **IoTHubSuffix** su **azure-devices.net**.
- Il modulo **mapping** esegue il mapping degli indirizzi MAC dei dispositivi simulati sugli ID dispositivo dell'hub IoT. Assicurarsi che i valori di **deviceId** corrispondano agli ID dei due dispositivi aggiunti all'hub IoT e che i valori di **deviceKey** contengano le chiavi dei due dispositivi.
- I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi MAC corrispondono a quelli nel modulo **mapping**.
- Il modulo **Logger** registra l'attività del gateway in un file.
- I valori di **module path** illustrati di seguito presuppongono che l'esempio venga eseguito dalla radice di una copia locale del repository **azure-iot-gateway-sdk**.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothub_http/libiothub_http_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/mac_iothub_id_map/libmac_iothub_id_map_hl.so",
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
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}

```

Salvare le modifiche apportate al file di configurazione.

Per eseguire l'esempio:

1. Dalla shell accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk**.
2. Eseguire il comando seguente:

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
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

<!---HONumber=AcomDC_0504_2016-->