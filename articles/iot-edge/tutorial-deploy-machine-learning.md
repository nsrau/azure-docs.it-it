---
title: Distribuire Azure Machine Learning con Azure IoT Edge | Microsoft Docs
description: In questa esercitazione Azure Machine Learning viene distribuito come modulo in un dispositivo periferico
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1cc1115612e8ffbe383f563e30ed5c36055df50a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158109"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Esercitazione: Distribuire Azure Machine Learning come modulo di IoT Edge (anteprima)

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la distribuzione di un modulo di Azure Machine Learning che stima quando un dispositivo ha esito negativo in base ai dati di temperatura del computer simulati. Per altre informazioni su Azure ML in IoT Edge, consultare la [documentazione di Azure Machine Learning](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md).

Il modulo di Azure Machine Learning che si creerà in questa esercitazione legge i dati ambientali generati dal dispositivo e etichetta i messaggi come anomali o non anomali.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un modulo di Azure Machine Learning
> * Eseguire il push di un contenitore di modulo nel registro contenitori di Azure
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

>[!NOTE]
>I moduli di Azure Machine Learning in Azure IoT Edge sono in anteprima pubblica. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).
* Il modulo Azure Machine Learning non supporta i processori ARM.

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 
* Un'area di lavoro di Azure Machine Learning. Per crearne una, seguire le istruzioni incluse in [Preparare la distribuzione di modelli nei dispositivi IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md).


### <a name="disable-process-identification"></a>Disabilitare l'identificazione di un processo

>[!NOTE]
>
> Durante l'anteprima, Azure Machine Learning non supporta la funzionalità di sicurezza di identificazione di un processo abilitata per impostazione predefinita con IoT Edge. 
> Ecco la procedura per disabilitarla, che tuttavia non è adatta per l'uso nell'ambiente di produzione. Questa procedura è necessaria solo in Linux, perché in Windows è già stata completata durante l'installazione del runtime Windows Edge.

Per disabilitare l'identificazione di un processo nel dispositivo IoT Edge, sarà necessario fornire l'indirizzo IP e la porta per **workload_uri** e **management_uri** nella sezione **connect** della configurazione del daemon IoT Edge.

Ottenere prima l'indirizzo IP. Immettere `ipconfig` nella riga di comando e copiare l'indirizzo IP dell'interfaccia **docker0**.

Modificare il file di configurazione del daemon IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Aggiornare la sezione **connect** della configurazione con l'indirizzo IP. Ad esempio:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Immettere gli stessi indirizzi nella sezione **listen** della configurazione. Ad esempio:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Creare una variabile di ambiente IOTEDGE_HOST con l'indirizzo management_uri. Per impostarla in modo permanente, aggiungerla a `/etc/environment`. Ad esempio:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Creare il contenitore di Azure ML
In questa sezione scaricare i file del modello sottoposto a training e convertirli in un contenitore di Azure ML.

Per creare un contenitore Docker con il modello di Machine Learning, seguire le istruzioni incluse nell'argomento [Preparare la distribuzione di modelli nei dispositivi IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md) della documentazione.  Tutti i componenti necessari per l'immagine Docker sono disponibili nel [repository GIT di AI Toolkit per Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a name="view-the-container-repository"></a>Visualizzare il repository di contenitori

Verificare che l'immagine del contenitore sia stata creata e archiviata correttamente nel registro contenitori di Azure associato all'ambiente di Machine Learning.

1. Nel [portale di Azure](https://portal.azure.com) passare a **Tutti i servizi** e selezionare **Registri contenitori**.
2. Selezionare il registro. Il nome deve iniziare con **mlcr** e fare riferimento al gruppo di risorse, alla posizione e alla sottoscrizione usati per configurare Gestione modelli.
3. Selezionare **Chiavi di accesso**.
4. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**.  Queste informazioni sono necessarie per accedere al registro dai dispositivi Edge.
5. Selezionare **Repository**
6. Selezionare **machinelearningmodule**
7. A questo punto si disporrà del percorso completo dell'immagine del contenitore. Prendere nota del percorso dell'immagine per la sezione successiva. Dovrebbe essere simile al seguente: **<nome_registro>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.

1. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge.

1. Selezionare **Imposta moduli**.

1. Nella sezione **Impostazioni registro** aggiungere le credenziali copiate dal registro contenitori di Azure. 

   ![Aggiungere le credenziali del registro](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Se il modulo tempSensor è stato distribuito in precedenza nel dispositivo IoT Edge, la compilazione dei campi potrebbe essere automatica. Se non è già incluso nell'elenco dei moduli, aggiungerlo.

    1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
    2. Nel campo **Nome** immettere `tempSensor`.
    3. Nel campo **URI immagine** immettere `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Selezionare **Salva**.

1. Aggiungere il modulo di Machine Learning creato.

    1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
    1. Nel campo **Nome** immettere `machinelearningmodule`.
    1. Nel campo **Immagine** immettere l'indirizzo dell'immagine, ad esempio `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Selezionare **Salva**.

1. Nel passaggio **Aggiungi moduli** selezionare **Avanti**.

1. Nel passaggio **Specifica route** copiare il codice JSON seguente nella casella di testo. La prima route trasporta i messaggi del sensore di temperatura al modulo di Machine Learning tramite l'endpoint "amlInput", che è l'endpoint usato da tutti i moduli di Azure Machine Learning. La seconda route trasporta i messaggi dal modulo di Machine Learning all'hub IoT. In questa route ''amlOutput'' è l'endpoint usato da tutti i moduli di Azure Machine Learning per l'output dei dati, mentre ''$upstream'' fa riferimento all'hub IoT.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Selezionare **Avanti**.

1. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.

1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**.  Dovrebbe essere visualizzato il nuovo **machinelearningmodule** in esecuzione insieme ai moduli **tempSensor** e runtime di IoT Edge.

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare i messaggi generati da ogni modulo IoT Edge e quelli recapitati all'hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Visualizzare i dati nel dispositivo IoT Edge

Nel dispositivo IoT Edge è possibile visualizzare i messaggi inviati da ogni singolo modulo. 

Se si eseguono questi comandi in un dispositivo Linux, potrebbe essere necessario usare `sudo` per le autorizzazioni elevate.

1. Visualizzare tutti i moduli nel dispositivo IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Visualizzare i messaggi inviati da un dispositivo specifico. Usare il nome del modulo presente nell'output del comando precedente.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Visualizzare i dati in arrivo all'hub IoT

È possibile visualizzare i messaggi da dispositivo a cloud ricevuti dall'hub IoT tramite l'[estensione Azure IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

I passaggi seguenti mostrano come configurare Visual Studio Code per il monitoraggio dei messaggi da dispositivo a cloud in arrivo all'hub IoT. 

1. In Visual Studio Code selezionare **IoT Hub Devices** (Dispositivi hub IoT).

2. Selezionare **...** e quindi **Set IoT Hub Connection String** (Imposta stringa di connessione hub IoT) dal menu.

   ![Menu ... per dispositivi dell'hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Nella casella di testo visualizzata nella parte superiore della pagina immettere la stringa di connessione iothubowner per l'hub IoT. Il dispositivo IoT Edge dovrebbe essere visualizzato nell'elenco di dispositivi dell'hub IoT.

4. Selezionare nuovamente **...** e quindi selezionare **Start monitoring D2C message** (Avvia monitoraggio messaggio da dispositivo a cloud).

5. Osservare i messaggi provenienti da tempSensor ogni cinque secondi. Il corpo del messaggio contiene una proprietà denominata **anomaly** a cui è assegnato un valore true o false. Se il modello è stato eseguito correttamente, la proprietà **AzureMLResponse** contiene il valore "OK".

   ![Risposta di Azure ML nel corpo del messaggio](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Filtrare i dati del sensore usando il codice C#](tutorial-csharp-module.md)

