---
title: Distribuire Azure Machine Learning con Azure IoT Edge | Microsoft Docs
description: Distribuire Azure Machine Learning come modulo in un dispositivo periferico
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: afc9e7c0635f9920aa3ec7c9e6012aa4e41edb9d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062042"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuire Azure Machine Learning come modulo di IoT Edge - anteprima

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la distribuzione di un modulo di Azure Machine Learning che stima quando un dispositivo ha esito negativo in base ai dati di temperatura del computer simulati. 

Il modulo di Azure Machine Learning che si creerà in questa esercitazione legge i dati ambientali generati dal dispositivo e etichetta i messaggi come anomali o non anomali.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un modulo di Azure Machine Learning
> * Eseguire il push di un contenitore di modulo nel registro contenitori di Azure
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

>[!NOTE]
>I moduli di Azure Machine Learning in Azure IoT Edge sono in anteprima pubblica. 

## <a name="prerequisites"></a>prerequisiti

Per testare il modulo di Machine Learning che si compila in questa esercitazione, è necessario un dispositivo IoT Edge. È possibile usare il dispositivo configurato nella guida introduttiva per [dispositivi Linux](quickstart-linux.md) o [Windows](quickstart.md). 

Il modulo Azure Machine Learning non supporta i processori ARM.

Nel computer di sviluppo sono necessari prerequisiti seguenti: 
* Un account di Azure Machine Learning. Seguire le istruzioni riportate in [Creare account di Azure Machine Learning e installare Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Non è necessario installare l'applicazione workbench per questa esercitazione. 
* Gestione modelli per Azure ML nel computer in uso. Per configurare l'ambiente e creare un account, attenersi alle istruzioni riportate in [Configurazione di Gestione modelli](../machine-learning/desktop-workbench/deployment-setup-configuration.md).

### <a name="disable-process-identification"></a>Disabilitare l'identificazione di un processo

>[!NOTE]
>
> Durante l'anteprima, Azure Machine Learning non supporta la funzionalità di sicurezza di identificazione di un processo abilitata per impostazione predefinita con IoT Edge. 
> Ecco la procedura per disabilitarla, che tuttavia non è adatta per l'uso nell'ambiente di produzione.

Per disabilitare l'identificazione di un processo, sarà necessario fornire l'indirizzo IP e la porta per **workload_uri** e **management_uri** nella sezione **connect** della configurazione del daemon IoT Edge.

Ottenere prima l'indirizzo IP. Immettere `ifconfig` nella riga di comando e copiare l'indirizzo IP dell'interfaccia **docker0**.

Modificare il file di configurazione del daemon IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Aggiornare la sezione **connect** della configurazione. Ad esempio: 
```yaml
connect:
  management_uri: "http://172.17.0.1.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Immettere gli stessi indirizzi nella sezione **listen** della configurazione. Ad esempio: 

```yaml
listen:
  management_uri: "http://172.17.0.1.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Creare una variabile di ambiente IOTEDGE_HOST con l'indirizzo management_uri. Per impostarla in modo permanente, aggiungerla a `/etc/environment`. Ad esempio:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Creare il contenitore di Azure ML
In questa sezione scaricare i file del modello sottoposto a training e convertirli in un contenitore di Azure ML.

Nel computer che esegue Gestione modelli per Azure ML scaricare e salvare [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) e [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) dal toolkit di Azure ML IoT su GitHub. Questi file definiscono il modello di Machine Learning sottoposto a training che verrà distribuito al dispositivo Iot Edge.

Usare il modello sottoposto a training per creare un contenitore che può essere distribuito ai dispositivi IoT Edge. Usare il comando seguente per:

   * Registrare il modello.
   * Creare un manifesto.
   * Creare un'immagine del contenitore Docker denominata *machinelearningmodule*.
   * Distribuire l'immagine nel cluster AKS (Azure Kubernetes Service).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Visualizzare il repository di contenitori

Verificare che l'immagine del contenitore sia stata creata e archiviata correttamente nel registro contenitori di Azure associato all'ambiente di Machine Learning.

1. Nel [portale di Azure](https://portal.azure.com) passare a **Tutti i servizi** e selezionare **Registri contenitori**.
2. Selezionare il registro. Il nome deve iniziare con **mlcr** e fare riferimento al gruppo di risorse, alla posizione e alla sottoscrizione usati per configurare Gestione modelli.
3. Selezionare **Chiavi di accesso**.
4. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**.  Queste informazioni sono necessarie per accedere al registro dai dispositivi Edge.
5. Selezionare **Repository**
6. Selezionare **machinelearningmodule**
7. A questo punto si disporrà del percorso completo dell'immagine del contenitore. Prendere nota del percorso dell'immagine per la sezione successiva. Dovrebbe essere simile al seguente: **<nome_registro>.azureacr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT.

1. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge.

1. Selezionare **Set modules** (Configura i moduli).

1. Nella sezione **Impostazioni registro** aggiungere le credenziali copiate dal registro contenitori di Azure. 

   ![Aggiungere le credenziali del registro](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Se il modulo tempSensor è stato distribuito in precedenza nel dispositivo IoT Edge, la compilazione dei campi potrebbe essere automatica. Se non è già incluso nell'elenco dei moduli, aggiungerlo.

    1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.
    2. Nel campo **Nome** immettere `tempsensor`.
    3. Nel campo **URI immagine** immettere `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Selezionare **Salva**.

1. Aggiungere il modulo di Machine Learning creato.

    1. Fare clic su **Aggiungi** e selezionare **Azure Machine Learning Module** (Modulo Azure Machine Learning).
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

È possibile visualizzare i messaggi da dispositivo a cloud ricevuti dall'hub IoT tramite [IoT Hub Explorer](https://github.com/azure/iothub-explorer) o l'estensione [Azure IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

I passaggi seguenti mostrano come configurare Visual Studio Code per il monitoraggio dei messaggi da dispositivo a cloud in arrivo all'hub IoT. 

1. In Visual Studio Code selezionare **IoT Hub Devices** (Dispositivi hub IoT).

2. Selezionare **...** e quindi **Set IoT Hub Connection String** (Imposta stringa di connessione hub IoT) dal menu.

   ![Menu ... per dispositivi dell'hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Nella casella di testo visualizzata nella parte superiore della pagina immettere la stringa di connessione iothubowner per l'hub IoT. Il dispositivo IoT Edge dovrebbe essere visualizzato nell'elenco di dispositivi dell'hub IoT.

4. Selezionare nuovamente **...** e quindi selezionare **Start monitoring D2C message** (Avvia monitoraggio messaggio da dispositivo a cloud).

5. Osservare i messaggi provenienti da tempSensor ogni cinque secondi. Il corpo del messaggio contiene una proprietà denominata **anomaly** a cui è assegnato un valore true o false. Se il modello è stato eseguito correttamente, la proprietà **AzureMLResponse** contiene il valore "OK".

   ![Risposta di Azure ML nel corpo del messaggio](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Se si continua con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni già create e riutilizzarle.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti. 

> [!IMPORTANT]
> L'eliminazione delle risorse di Azure e del gruppo di risorse è irreversibile. Dopo l'eliminazione, il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare solo l'hub IoT, eseguire il comando seguente usando il nome dell'hub e il nome del gruppo di risorse:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Per eliminare l'intero gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse che contiene l'hub IoT. 

3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Filtrare i dati del sensore usando il codice C#](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
