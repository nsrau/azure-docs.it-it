---
title: Distribuire Azure Machine Learning con Azure IoT Edge | Microsoft Docs
description: Distribuire Azure Machine Learning come modulo in un dispositivo periferico
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/13/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2afdb257421b1333f451eb9d0dd4c2af5a12e946
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuire Azure Machine Learning come modulo di IoT Edge - anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione viene illustrata la distribuzione di un modulo di Azure Machine Learning che stima il caso di esito negativo in base ai dati del sensore sul dispositivo simulato IoT Edge che è stato creato in un dispositivo di [distribuire Azure IoT Edge su un dispositivo simulato in Windows] [ lnk-tutorial1-win] o [Linux] [ lnk-tutorial1-lin] esercitazioni. 

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Creare un modulo di Azure Machine Learning
> * Push di un contenitore di modulo nel Registro di sistema contenitore di Azure
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

Il modulo di Azure Machine Learning creati in questa esercitazione legge i dati di temperatura generati dal dispositivo e invia solo messaggi a monte all'IoT Hub Azure quando vengono eseguite stime in un errore (denominato un'anomalia). 


## <a name="prerequisites"></a>Prerequisiti

* Il dispositivo Azure IoT Edge creato nella guida introduttiva o nella prima esercitazione.
* La stringa di connessione per l'hub IoT a cui si connette il dispositivo di IoT Edge.
* Un account di Azure Machine Learning. Per creare un account, seguire le istruzioni in [creare Azure Machine Learning account e installare Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-accounts). Non è necessario installare l'applicazione di area di lavoro per questa esercitazione. 
* Modulo di gestione per Azure ML nel computer. Per configurare l'ambiente e creare un account, seguire le istruzioni in [installazione della gestione modello](https://docs.microsoft.com/en-us/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Creare il contenitore di Azure ML
In questa sezione di scaricare i file di modello con training e di convertirli in un contenitore di Azure ML.  

Nel computer che esegue Gestione dei moduli per Machine Learning di Azure, scaricare e salvare [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) e [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) di Azure ML IoT Toolkit su GitHub. Questi file definiscono il training modello di machine learning che verrà distribuito al dispositivo Iot Edge. 

Utilizzare il modello con training per creare un contenitore che può essere distribuito ai dispositivi IoT Edge.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
Il nome del servizio, *machinelearningmodule* in questo esempio, diventa il nome dell'immagine contenitore docker.

### <a name="view-the-container-repository"></a>Visualizzare il repository del contenitore

Verificare che l'immagine contenitore è stato correttamente creato e archiviato nel repository del contenitore di Azure associata con l'ambiente di machine learning.

1. Nel [portale di Azure](https://portal.azure.com), passare a **tutti i servizi** e selezionare **registri contenitore**.
2. Selezionare il Registro di sistema. Il nome deve iniziare con **mlcr** e a cui appartiene il gruppo di risorse, posizione e sottoscrizione utilizzata per impostare la gestione di modulo.
3. Selezionare **le chiavi di accesso**
4. Copia il **server di accesso**, **Username**, e **Password**.  Sono necessarie per accedere al registro dai dispositivi Edge.
5. Selezionare **repository**
6. Selezionare **machinelearningmodule**
7. Ora è il percorso completo dell'immagine del contenitore. Prendere nota del percorso immagine per la sezione successiva. Dovrebbe essere simile al seguente: **.azureacr.io/machinelearningmodule:1 < registry_name >**

## <a name="add-registry-credentials-to-your-edge-device"></a>Aggiungere le credenziali del registro al dispositivo di Edge

Aggiungere le credenziali per il registro al runtime di Edge nel computer in cui si esegue il dispositivo perimetrale di Edge, Il seguente comando consente l'accesso al runtime per effettuare il pull del contenitore.

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
1. Se in precedenza è stato distribuito il modulo tempSensor del dispositivo IoT Edge, potrebbe essere immissione automatica. Se non è già nell'elenco dei moduli, è necessario aggiungerlo.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Selezionare **Salva**.
1. Aggiunta di machine learning modulo creato.
    1. Selezionare **Add IoT Edge Module** (Aggiungi il modulo di IoT Edge).
    1. Nel **nome** immettere`machinelearningmodule`
    1. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Selezionare **Salva**.
1. Nel **aggiungere moduli** passaggio seleziona **Avanti**.
1. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. La prima route trasporta i messaggi del sensore di temperatura al modulo di Machine Learning tramite l'endpoint "amlInput", che è l'endpoint usato da tutti i moduli di Azure Machine Learning. La seconda route trasporta i messaggi dal modulo di Machine Learning all'hub IoT. In questa route, ' amlOutput ' endpoint che utilizzano tutti i moduli di Azure Machine Learning per i dati di output e '$monte' indica IoT Hub. 

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
1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  Verrà visualizzato il nuovo **machinelearningmodule** in esecuzione con il **tempSensor** modulo e i moduli runtime IoT Edge.

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare i messaggi da dispositivo a cloud del dispositivo IoT Edge invia tramite l'estensione Azure IoT Toolkit per Visual Studio Code. 

1. Nel codice di Visual Studio, selezionare **IoT Hub dispositivi**. 
2. Selezionare **...**  selezionare **Set di stringa di connessione Hub IoT** dal menu. 

   ![I dispositivi di Hub IoT ulteriori menu](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Nella casella di testo visualizzata nella parte superiore della pagina, immettere la stringa di connessione iothubowner per l'IoT Hub. Il dispositivo di IoT Edge dovrebbe essere visualizzato nell'elenco dispositivi Hub IoT.
4. Selezionare **...**  nuovamente, quindi selezionare **avviare il monitoraggio di messaggio D2C**.
5. Osservare i messaggi provenienti da tempSensor ogni cinque secondi, quali machinelearningmodule aggiunge con la valutazione dell'integrità di dispositivo. 

   ![Risposta ML Azure nel corpo del messaggio](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
