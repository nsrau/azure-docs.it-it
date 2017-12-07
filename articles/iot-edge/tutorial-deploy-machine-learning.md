---
title: Distribuire Azure Machine Learning con Azure IoT Edge | Microsoft Docs
description: Distribuire Azure Machine Learning come modulo in un dispositivo periferico
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuire Azure Machine Learning come modulo di IoT Edge - anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra in modo dettagliato la distribuzione di un modulo di Azure Machine Learning che prevede quando un dispositivo restituisce un errore in base ai dati dei sentori nel dispositivo di IoT Edge simulato creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato su [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. Si apprenderà come: 

> [!div class="checklist"]
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

Quando si desidera usare il modello di [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) nella soluzione, si procederà a [distribuire un modello](https://aka.ms/aml-iot-edge-doc) per IoT Edge e a ospitarlo in un registro contenitori, ad esempio [Registro contenitori di Azure](../container-registry/index.yml) o Docker.

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nella prima esercitazione.
* La stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge.
* Il contenitore di Azure ML

## <a name="create-the-azure-ml-container"></a>Creare il contenitore di Azure ML
Per creare il contenitore di Azure ML, seguire le istruzioni disponibili nel [toolkit AI per Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Eseguire la soluzione

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.
1. Passare a **IoT Edge (anteprima)** e selezionare il dispositivo di IoT Edge.
1. Selezionare **Set modules** (Configura i moduli).
1. Selezionare **Add IoT Edge module** (Aggiungi il modulo di IoT Edge).
1. Nel campo **Name** (Nome) immettere `tempSensor`.
1. Nel campo **Image URI** (URI immagine) immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Lasciare invariate le altre impostazioni e fare clic su **Save** (Salva).
1. Sempre nel passaggio **Add Modules** (Aggiungi modulo) selezionare di nuovo **Add IoT Edge module** (Aggiungi il modulo di IoT Edge).
1. Nel campo **Name** (Nome) immettere il nome del contenitore creato nella sezione precedente. Per informazioni su come trovare il nome, vedere il [toolkit AI per Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).
1. Nel campo **Image** (Immagine) immettere l'URI dell'immagine del contenitore creato nella sezione precedente. Per informazioni su come trovare l'immagine, vedere il [toolkit AI per Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).
1. Fare clic su **Salva**.
1. Nel passaggio **Add Modules** (Aggiungi moduli) fare clic su **Next** (Avanti).
1. Aggiornare le route per il modulo:
1. Nel passaggio **Specify Routes** (Specificare le route) copiare il codice JSON seguente nella casella di testo. I moduli pubblicano tutti i messaggi nel runtime di Edge. Le regole dichiarative nel runtime definiscono la destinazione del flusso di messaggi. In questa esercitazione sono necessarie due route. La prima route trasporta i messaggi del sensore di temperatura al modulo di Machine Learning tramite l'endpoint "amlInput", che è l'endpoint usato da tutti i moduli di Azure Machine Learning. La seconda route trasporta i messaggi dal modulo di Machine Learning all'hub IoT. In questa route ''amlOutput'' è l'endpoint usato da tutti i moduli di Azure Machine Learning per l'output dei dati, mentre ''$upstream'' è una destinazione speciale che indica all'hub periferico di inviare i messaggi all'hub IoT. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Fare clic su **Avanti**. 
1. Nel passaggio "Review Template" (Verifica il modello) fare clic su "Submit" (Invia). 
1. Tornare alla pagina dei dettagli del dispositivo e fare clic su "Refresh" (Aggiorna).  Dovrebbe essere visualizzato il nuovo "machinelearningmodule" in esecuzione insieme al "modulo tempSensor" e al "runtime di IoT Edge".

## <a name="view-generated-data"></a>Visualizzare i dati generati

 In VS Code usare il comando di menu **Visualizza | Riquadro comandi | IoT: Start Monitoring D2C Messages** (IoT: Avvia il monitoraggio dei messaggi D2C) per monitorare i dati in arrivo nell'hub IoT. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
