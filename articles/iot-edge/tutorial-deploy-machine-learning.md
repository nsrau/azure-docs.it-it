---
title: Distribuire Azure Machine Learning con Azure IoT Edge | Microsoft Docs
description: Distribuire Azure Machine Learning come modulo in un dispositivo periferico
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/06/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e2314f589456f604c8c008e10fb8084e0524575d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuire Azure Machine Learning come modulo di IoT Edge - anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra in modo dettagliato la distribuzione di un modulo di Azure Machine Learning che prevede quando un dispositivo restituisce un errore in base ai dati dei sensori nel dispositivo di IoT Edge simulato creato nelle esercitazioni sulla distribuzione di Azure IoT Edge in un dispositivo simulato su [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. 

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Creare un modulo di Azure Machine Learning
> * Eseguire il push di un contenitore di modulo nel registro contenitori di Azure
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

Il modulo di Azure Machine Learning che si creerà in questa esercitazione legge i dati ambientali generati dal dispositivo e etichetta i messaggi come anomali o non anomali. 

## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nella prima esercitazione.
* La stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge.
* Un account di Azure Machine Learning. Per creare un account, attenersi alle istruzioni riportate in [Creare account di Azure Machine Learning e installare Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts). Non è necessario installare l'applicazione workbench per questa esercitazione. 
* Gestione modelli per Azure ML nel computer in uso. Per configurare l'ambiente e creare un account, attenersi alle istruzioni riportate in [Configurazione di Gestione modelli](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Creare il contenitore di Azure ML
In questa sezione scaricare i file del modello sottoposto a training e convertirli in un contenitore di Azure ML.  

Nel computer che esegue Gestione modelli per Azure ML scaricare e salvare [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) e [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) dal toolkit di Azure ML IoT su GitHub. Questi file definiscono il modello di Machine Learning sottoposto a training che verrà distribuito al dispositivo Iot Edge. 

Usare il modello sottoposto a training per creare un contenitore che può essere distribuito ai dispositivi IoT Edge.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
Il nome del servizio, *machinelearningmodule* in questo esempio, diventa il nome dell'immagine del contenitore Docker.

### <a name="view-the-container-repository"></a>Visualizzare il repository di contenitori

Verificare che l'immagine del contenitore sia stata creata e archiviata correttamente nel repository dei contenitori di Azure associato all'ambiente di Machine Learning.

1. Nel [portale di Azure](https://portal.azure.com) passare a **Tutti i servizi** e selezionare **Registri contenitori**.
2. Selezionare il registro. Il nome deve iniziare con **mlcr** e fare riferimento al gruppo di risorse, alla posizione e alla sottoscrizione usati per configurare Gestione modelli.
3. Selezionare **Chiavi di accesso**.
4. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**.  Queste informazioni sono necessarie per accedere al registro dai dispositivi Edge.
5. Selezionare **Repository**
6. Selezionare **machinelearningmodule**
7. A questo punto si disporrà del percorso completo dell'immagine del contenitore. Prendere nota del percorso dell'immagine per la sezione successiva. Dovrebbe essere simile al seguente: **<nome_registro>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Aggiungere le credenziali del registro al dispositivo perimetrale

Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale, Questo comando consente al runtime l'accesso per il pull del contenitore.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Eseguire la soluzione

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.
1. Passare a **IoT Edge (anteprima)** e selezionare il dispositivo di IoT Edge.
1. Selezionare **Set modules** (Configura i moduli).
1. Se il modulo tempSensor è stato distribuito in precedenza nel dispositivo IoT Edge, la compilazione dei campi potrebbe essere automatica. Se non è già incluso nell'elenco dei moduli, aggiungerlo.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Selezionare **Salva**.
1. Aggiungere il modulo di Machine Learning creato.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    1. Nel campo **Nome** immettere `machinelearningmodule`.
    1. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Selezionare **Salva**.
1. Nel passaggio **Add Modules** (Aggiungi moduli) selezionare **Next** (Avanti).
1. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. La prima route trasporta i messaggi del sensore di temperatura al modulo di Machine Learning tramite l'endpoint "amlInput", che è l'endpoint usato da tutti i moduli di Azure Machine Learning. La seconda route trasporta i messaggi dal modulo di Machine Learning all'hub IoT. In questa route ''amlOutput'' è l'endpoint usato da tutti i moduli di Azure Machine Learning per l'output dei dati, mentre ''$upstream'' fa riferimento all'hub IoT. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Selezionare **Avanti**. 
1. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**. 
1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  Dovrebbe essere visualizzato il nuovo **machinelearningmodule** in esecuzione insieme ai moduli **tempSensor** e runtime di IoT Edge.

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare i messaggi da dispositivo a cloud inviati dal dispositivo IoT Edge tramite [IoT Hub Explorer](https://github.com/azure/iothub-explorer) o l'estensione Azure IoT Toolkit per Visual Studio Code. 

1. In Visual Studio Code selezionare **IoT Hub Devices** (Dispositivi hub IoT). 
2. Selezionare **...** e quindi **Set IoT Hub Connection String** (Imposta stringa di connessione hub IoT) dal menu. 

   ![Menu ... per dispositivi dell'hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Nella casella di testo visualizzata nella parte superiore della pagina immettere la stringa di connessione iothubowner per l'hub IoT. Il dispositivo IoT Edge dovrebbe essere visualizzato nell'elenco di dispositivi dell'hub IoT.
4. Selezionare nuovamente **...** e quindi selezionare **Start monitoring D2C message** (Avvia monitoraggio messaggio da dispositivo a cloud).
5. Osservare i messaggi provenienti da tempSensor ogni cinque secondi. Il corpo del messaggio contiene una proprietà denominata **anomaly** a cui è assegnato un valore true o false. Se il modello è stato eseguito correttamente, la proprietà **AzureMLResponse** contiene il valore "OK". 

   ![Risposta di Azure ML nel corpo del messaggio](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
