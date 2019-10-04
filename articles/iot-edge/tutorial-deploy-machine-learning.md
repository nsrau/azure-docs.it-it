---
title: Distribuire Azure Machine Learning a un dispositivo - Azure IoT Edge | Microsoft Docs
description: In questa esercitazione viene creato un modello di Azure Machine Learning che viene poi distribuito come modulo a un dispositivo perimetrale
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 6c48aaf404803c45122ed3fad0d6af1345406f7b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239674"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Esercitazione: Distribuire Azure Machine Learning come modulo di IoT Edge (anteprima)

Usare Azure Notebooks per sviluppare un modulo di machine learning e distribuirlo in un dispositivo Linux che esegue Azure IoT Edge. 

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la distribuzione di un modulo di Azure Machine Learning che stima quando un dispositivo ha esito negativo in base ai dati di temperatura del computer simulati. Per altre informazioni sul servizio Machine Learning in IoT Edge, consultare la [documentazione di Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

Il modulo di Azure Machine Learning che si creerà in questa esercitazione legge i dati ambientali generati dal dispositivo e etichetta i messaggi come anomali o non anomali.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un modulo di Azure Machine Learning
> * Eseguire il push di un contenitore di modulo in un Registro Azure Container
> * Distribuire un modulo di Azure Machine Learning in un dispositivo IoT Edge
> * Visualizzare i dati generati

>[!NOTE]
>I moduli di Azure Machine Learning in Azure IoT Edge sono in anteprima pubblica.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare una macchina virtuale di Azure come dispositivo IoT Edge seguendo la procedura illustrata nell'argomento di avvio rapido per [Linux](quickstart-linux.md).
* Il modulo di Azure Machine Learning non supporta i contenitori Windows.
* Il modulo di Azure Machine Learning non supporta i processori ARM.

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un'area di lavoro di Azure Machine Learning. Seguire le istruzioni contenute in [Iniziare a usare Azure Machine Learning con il portale di Azure](../machine-learning/service/quickstart-get-started.md) per crearne una e imparare a usarla.
   * Prendere nota del nome dell'area di lavoro, del gruppo di risorse e dell'ID sottoscrizione. Tutti questi valori sono disponibili nella panoramica dell'area di lavoro nel portale di Azure. Sarà necessario usare questi valori più avanti nell'esercitazione per connettere un notebook di Azure alle risorse dell'area di lavoro. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Creare e distribuire un modulo di Azure Machine Learning

In questa sezione i file del modello di Machine Learning sottoposto a training vengono convertiti in un contenitore del servizio Azure Machine Learning. Tutti i componenti necessari per l'immagine Docker sono disponibili nel [repository GIT di AI Toolkit per Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Seguire questa procedura per caricare tale repository in Microsoft Azure Notebooks per creare il contenitore ed eseguirne il push in Registro Azure Container.


1. Passare ai progetti Azure Notebooks. È possibile visualizzarli dall'area di lavoro del servizio Azure Machine Learning nel [portale di Azure](https://portal.azure.com) o accedendo a [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) con l'account Azure.

2. Selezionare **Upload GitHub Repo** (Carica repository GitHub).

3. Specificare il nome di repository GitHub seguente: `Azure/ai-toolkit-iot-edge`. Deselezionare la casella **Pubblico** per mantenere privato il progetto. Selezionare **Importa**. 

4. Al termine dell'importazione, passare al nuovo progetto **ai-toolkit-iot-edge** e aprire la cartella **IoT Edge anomaly detection tutorial** (Esercitazione di rilevamento anomalie di IoT Edge). 

5. Verificare che il progetto sia in esecuzione. Se non lo è, selezionare **Run on Free Compute** (Esegui in ambiente di calcolo gratuito).

   ![Run on Free Compute (Esegui in ambiente di calcolo gratuito)](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Aprire il file **aml_config/config.json**.

7. Modificare il file di configurazione in modo da includere i valori per l'ID sottoscrizione di Azure, un gruppo di risorse nella sottoscrizione e il nome dell'area di lavoro del servizio Azure Machine Learning. È possibile ottenere tutti questi valori dalla sezione **Panoramica** dell'area di lavoro in Azure. 

8. Salvare il file di configurazione.

9. Aprire il file **00-anomaly-detection-tutorial.ipynb**.

10. Quando richiesto, selezionare il kernel **Python 3.6** e quindi **Set Kernel** (Imposta kernel).

11. Modificare la prima cella del notebook seguendo le istruzioni nei commenti. Usare lo stesso gruppo di risorse, lo stesso ID sottoscrizione e lo stesso nome dell'area di lavoro aggiunti al file di configurazione.

12. Eseguire le celle del notebook selezionandole e quindi selezionando **Esegui** o premendo `Shift + Enter`.

    >[!TIP]
    >Alcune celle del notebook dell'esercitazione di rilevamento anomalie sono facoltative perché creano risorse che alcuni utenti possono o non possono avere ancora, ad esempio un hub IoT. Se nella prima cella si inseriscono le informazioni relative a risorse esistenti, verranno visualizzati errori se si eseguono le celle che creano nuove risorse perché Azure non creerà risorse duplicate. Questo comportamento è normale e sarà possibile ignorare gli errori o saltare completamente tali sezioni facoltative. 

Terminando tutti i passaggi nel notebook, viene completato il training di un modello di rilevamento anomalie, il modello viene creato come immagine del contenitore Docker e viene eseguito il push di tale immagine in Registro Azure Container. Il modello è stato quindi testato e infine distribuito al dispositivo IoT Edge. 

## <a name="view-container-repository"></a>Visualizzare il repository del contenitore

Verificare che l'immagine del contenitore sia stata creata e archiviata correttamente nel Registro Azure Container associato all'ambiente di Machine Learning. L'immagine del contenitore e le credenziali del registro per il dispositivo IoT Edge sono state fornite automaticamente dal notebook usato nella sezione precedente, ma è importante sapere dove sono archiviate in modo da poter trovare autonomamente tali informazioni in un secondo momento. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'area di lavoro del servizio Azure Machine Learning. 

2. Nella sezione **Panoramica** sono elencati i dettagli dell'area di lavoro e le risorse associate. Selezionare il valore **Registro**, che dovrebbe corrispondere al nome dell'area di lavoro seguito da numeri casuali. 

3. Nel registro contenitori selezionare **Repository**. Dovrebbe essere visualizzato un repository denominato **tempanomalydetection** creato dal notebook eseguito nella sezione precedente. 

4. Selezionare **tempanomalydetection**. Il repository dovrebbe avere un tag: **1**. 

   Ora che il nome del registro, il nome del repository e il tag sono noti, si conosce il percorso completo dell'immagine del contenitore. I percorsi dell'immagine hanno il formato seguente: **\<nome_registro\>.azurecr.io/tempanomalydetection:1**. È possibile usare il percorso dell'immagine per distribuire questo contenitore ai dispositivi IoT Edge. 

5. Nel registro contenitori selezionare **Chiavi di accesso**. Dovrebbero essere visualizzate diverse credenziali di accesso, tra cui **Server di accesso** e **Nome utente** e **Password** per un utente amministratore.

   Queste credenziali possono essere incluse nel manifesto di distribuzione per concedere al dispositivo IoT Edge l'accesso per eseguire il pull delle immagini del contenitore dal registro. 

Ora si conosce il percorso in cui è archiviata l'immagine del contenitore di Machine Learning. La sezione successiva illustra i passaggi da eseguire per visualizzare il contenitore in esecuzione come modulo distribuito nel dispositivo IoT Edge. 

## <a name="view-generated-data"></a>Visualizzare i dati generati

È possibile visualizzare i messaggi generati da ogni modulo IoT Edge e quelli recapitati all'hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Visualizzare i dati nel dispositivo IoT Edge

Nel dispositivo IoT Edge è possibile visualizzare i messaggi inviati da ogni singolo modulo.

Potrebbe essere necessario usare `sudo` per le autorizzazioni elevate per eseguire i comandi `iotedge`. Disconnettendosi e riaccedendo al dispositivo, si aggiornano automaticamente le autorizzazioni.

1. Visualizzare tutti i moduli nel dispositivo IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Visualizzare i messaggi inviati da un dispositivo specifico. Usare il nome del modulo presente nell'output del comando precedente.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Visualizzare i dati in arrivo all'hub IoT

È anche possibile visualizzare i messaggi ricevuti dall'hub IoT usando l'[estensione Azure IoT Hub Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (in precedenza estensione Azure IoT Toolkit).

I passaggi seguenti mostrano come configurare Visual Studio Code per il monitoraggio dei messaggi da dispositivo a cloud in arrivo all'hub IoT.

1. In Visual Studio Code selezionare **IoT Hub Devices** (Dispositivi hub IoT).

2. Selezionare **...** e quindi **Set IoT Hub Connection String** (Imposta stringa di connessione hub IoT) dal menu.

   ![Impostare la stringa di connessione dell'hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Nella casella di testo visualizzata nella parte superiore della pagina immettere la stringa di connessione iothubowner per l'hub IoT. Il dispositivo IoT Edge dovrebbe essere visualizzato nell'elenco di dispositivi dell'hub IoT.

4. Selezionare di nuovo **...** e quindi **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

5. Osservare i messaggi provenienti da tempSensor ogni cinque secondi. Il corpo del messaggio contiene una proprietà denominata **anomaly** a cui il modulo di Machine Learning assegna un valore true o false. Se il modello è stato eseguito correttamente, la proprietà **AzureMLResponse** contiene il valore "OK".

   ![Risposta del servizio Azure Machine Learning nel corpo del messaggio](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un modulo di IoT Edge basato su Azure Machine Learning. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Classificare immagini con Servizio visione artificiale personalizzato](tutorial-deploy-custom-vision.md)

