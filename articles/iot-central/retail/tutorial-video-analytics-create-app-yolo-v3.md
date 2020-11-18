---
title: Esercitazione - Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (YOLO v3)
description: Questa esercitazione illustra come creare un'applicazione di analisi video in IoT Central. È possibile crearla, personalizzarla e connetterla ad altri servizi di Azure. Questa esercitazione usa il sistema di rilevamento oggetti in tempo reale YOLO V3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 3994b05f613cbebcf6daa05cf8db3ef429b52407
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428063"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (YOLO v3)

Questa esercitazione illustra agli sviluppatori di soluzioni come creare un'applicazione di analisi video con il modello di applicazione *Analisi video - rilevamento movimento e oggetti* di IoT Central, dispositivi Azure IoT Edge, Servizi multimediali di Azure e il sistema di rilevamento movimento e oggetti in tempo reale YOLO v3. La soluzione usa un punto vendita al dettaglio per mostrare come soddisfare l'esigenza aziendale comune di monitorare le telecamere di sicurezza. La soluzione usa il rilevamento automatico degli oggetti in un feed video per identificare e individuare rapidamente gli eventi interessanti.

> [!TIP]
> Per usare OpenVINO&trade; invece di YOLO v3 per il rilevamento movimento e oggetti, vedere [Esercitazione: Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Il repository GitHub include anche il codice sorgente per i moduli IoT Edge **LvaEdgeGatewayModule** e **lvaYolov3**. Per altre informazioni sull'uso del codice sorgente, vedere [Creare i moduli del gateway Analisi video live](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Modificare il manifesto della distribuzione

La distribuzione di un modulo IoT Edge viene eseguita usando un manifesto della distribuzione. In IoT Central è possibile importare il manifesto come modello di dispositivo e quindi lasciare che IoT Central gestisca la distribuzione del modulo.

Per preparare il manifesto della distribuzione:

1. Aprire il file *deployment.amd64.json* salvato nella cartella *lva-configuration* usando un editor di testo.

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

Questa esercitazione configura la soluzione per l'uso del modulo YOLO V3 per il rilevamento movimento e oggetti. Il frammento di codice seguente mostra la configurazione del modulo:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Sostituire il manifesto

Nella pagina **LVA Edge Gateway v2** selezionare **+ Sostituisci manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Sostituire il manifesto":::

Passare alla cartella *lva-configuration* e selezionare il file manifesto *deployment.amd64.json* modificato in precedenza. Selezionare **Carica**. Dopo aver completato la convalida, selezionare **Sostituisci**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
