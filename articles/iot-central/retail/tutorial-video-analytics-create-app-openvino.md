---
title: Esercitazione - Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO)
description: Questa esercitazione illustra come creare un'applicazione di analisi video in IoT Central. È possibile crearla, personalizzarla e connetterla ad altri servizi di Azure. Questa esercitazione usa il toolkit Intel OpenVINO&trade; per il rilevamento oggetti in tempo reale.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: e16342b4b46a189a5a5c4c0554fb2d870c9cdcac
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876657"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO&trade;)

Questa esercitazione illustra agli sviluppatori di soluzioni come creare un'applicazione di analisi video con il modello di applicazione *Analisi video - rilevamento movimento e oggetti* di IoT Central, dispositivi Azure IoT Edge, Servizi multimediali di Azure e il sistema di rilevamento movimento e oggetti OpenVINO&trade; ottimizzato per l'hardware Intel. La soluzione usa un punto vendita al dettaglio per mostrare come soddisfare l'esigenza aziendale comune di monitorare le telecamere di sicurezza. La soluzione usa il rilevamento automatico degli oggetti in un feed video per identificare e individuare rapidamente gli eventi interessanti.

> [!TIP]
> Per usare YOLO v3 invece di OpenVINO&trade; per il rilevamento movimento e oggetti, vedere [Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Modificare il manifesto della distribuzione

La distribuzione di un modulo IoT Edge viene eseguita usando un manifesto della distribuzione. In IoT Central è possibile importare il manifesto come modello di dispositivo e quindi lasciare che IoT Central gestisca la distribuzione del modulo.

Per preparare il manifesto della distribuzione:

1. Aprire il file *deployment.openvino.amd64.json* salvato nella cartella *lva-configuration* usando un editor di testo.

1. Individuare le impostazioni `LvaEdgeGatewayModule` e modificare il nome dell'immagine come illustrato nel frammento di codice seguente:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Aggiungere il nome dell'account di Servizi multimediali nel nodo `env` nella sezione `LvaEdgeGatewayModule`. Il nome dell'account è stato annotato nel file *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Il modello non espone queste proprietà in IoT Central, quindi è necessario aggiungere i valori di configurazione di Servizi multimediali nel manifesto della distribuzione. Individuare il modulo `lvaEdge` e sostituire i segnaposto con i valori annotati nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    `azureMediaServicesArmId` è il valore di **ID risorsa** annotato nel file *scratchpad.txt* quando è stato creato l'account di Servizi multimediali.

    I valori `aadTenantId`, `aadServicePrincipalAppId` e `aadServicePrincipalSecret` sono stati annotati nel file *scratchpad.txt* quando è stata creata l'entità servizio per l'account di Servizi multimediali:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Salvare le modifiche.

Questa esercitazione configura la soluzione per l'uso del modulo OpenVINO&trade; per il rilevamento movimento e oggetti. Il frammento di codice seguente mostra la configurazione del modulo:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Sostituire il manifesto

Nella pagina **LVA Edge Gateway** selezionare **+ Sostituisci manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Sostituire il manifesto":::

Passare alla cartella *lva-configuration* e selezionare il file manifesto *deployment.openvino.amd64.json* modificato in precedenza. Selezionare **Carica**. Dopo aver completato la convalida, selezionare **Sostituisci**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
