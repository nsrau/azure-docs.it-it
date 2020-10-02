---
title: Distribuire un'app Web di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'analisi spaziale in un'applicazione Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: aahi
ms.openlocfilehash: 8032c3607dd74cddbaa5fd6690a95ebdf218809a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628195"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Procedura: distribuire un'applicazione Web per il conteggio di persone

Usare questo articolo per informazioni su come integrare l'analisi spaziale in un'app Web che comprende lo spostamento delle persone e monitora il numero di persone che occupano uno spazio fisico. 

In questa esercitazione si apprenderà come:

* Distribuire il contenitore di analisi spaziale
* Configurare l'operazione e la fotocamera
* Configurare la connessione dell'hub Internet all'interno dell'applicazione Web
* Distribuire e testare l'applicazione Web

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Conoscenza di base delle configurazioni di distribuzione di Azure IoT Edge e di un [Hub Azure](https://docs.microsoft.com/azure/iot-hub/) .
* Un [computer host](spatial-analysis-container.md)configurato.

## <a name="deploy-the-spatial-analysis-container"></a>Distribuire il contenitore di analisi spaziale

Compilare l' [applicazione di richiesta](https://aka.ms/csgate) per ottenere l'accesso per eseguire il contenitore. 

Seguire [la configurazione del computer host](./spatial-analysis-container.md) per configurare il computer host e connettere un dispositivo IOT Edge all'hub Azure. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Distribuire un servizio Hub Azure Internet nella sottoscrizione

Prima di tutto, creare un'istanza di un servizio Hub Azure Internet con il piano tariffario standard (S1) o il livello gratuito (S0). Seguire queste istruzioni per creare questa istanza usando l'interfaccia della riga di comando di Azure.

Specificare i parametri obbligatori:
* Sottoscrizione: il nome o l'ID della sottoscrizione di Azure
* Gruppo di risorse: creare un nome per il gruppo di risorse
* Nome dell'hub Internet: creare un nome per l'hub Internet delle cose
* Nome IoTHub: il nome dell'hub Internet delle cose creato 
* Nome dispositivo perimetrale: creare un nome per il dispositivo perimetrale

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Distribuire il contenitore in Azure IoT Edge nel computer host

Distribuire il contenitore di analisi spaziale come modulo di Internet delle cose nel computer host usando l'interfaccia della riga di comando di Azure. Il processo di distribuzione richiede un file manifesto di distribuzione che descrive i contenitori, le variabili e le configurazioni necessari per la distribuzione. È possibile trovare un [manifesto di distribuzione](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) di esempio in GitHub, che include una configurazione di distribuzione di base per il contenitore di *analisi spaziale* . 

> [!NOTE] 
> I contenitori *Spatial-Analysis-Telegraf* e *Spatial-Analysis-Diagnostics* sono facoltativi. È possibile decidere di rimuoverli dalla *DeploymentManifest.jssu* file. Per altre informazioni, vedere l'articolo [telemetria e risoluzione dei problemi](./spatial-analysis-logging.md) . È possibile trovare un *DeploymentManifest.jsdi esempio su* file [in GitHub](https://go.microsoft.com/fwlink/?linkid=2142179) 

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La maggior parte delle **variabili di ambiente** per il modulo IOT Edge sono già impostate nell' *DeploymentManifest.js* di esempio nel file collegato sopra. Nel file cercare le `BILLING_ENDPOINT` variabili di ambiente e `API_KEY` , mostrate di seguito. Sostituire i valori con l'URI dell'endpoint e la chiave API creata in precedenza. Verificare che il valore EULA sia impostato su "Accetto". 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Configurare i parametri delle operazioni

Ora che la configurazione iniziale del contenitore di *analisi spaziale* è stata completata, il passaggio successivo consiste nel configurare i parametri delle operazioni e aggiungerli alla distribuzione. 

Il primo passaggio consiste nell'aggiornare il [manifesto di distribuzione](https://go.microsoft.com/fwlink/?linkid=2142179) di esempio e configurare OperationId per `cognitiveservices.vision.spatialanalysis-personcount` come illustrato di seguito:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Al termine dell'aggiornamento del [manifesto di distribuzione](https://go.microsoft.com/fwlink/?linkid=2142179) , seguire le istruzioni del produttore della fotocamera per installare la fotocamera, configurare l'URL della fotocamera e configurare il nome utente e la password. 

Impostare quindi sull' `VIDEO_URL` URL RTSP della fotocamera e le credenziali per la connessione alla fotocamera.

Se il dispositivo perimetrale ha più di una GPU, selezionare la GPU in cui eseguire l'operazione. Assicurarsi di bilanciare il carico delle operazioni in cui non sono presenti più di 8 operazioni in esecuzione in una singola GPU alla volta.  

Configurare quindi la zona in cui si desidera contare le persone. Per configurare il poligono di zona, seguire prima le istruzioni del produttore per recuperare un frame dalla fotocamera. Per determinare ogni vertice del poligono, selezionare un punto nel frame, prendere le coordinate x, y del punto rispetto a sinistra, angolo superiore del frame e dividere per le dimensioni del frame corrispondenti. Impostare i risultati come coordinate x, y del vertice. È possibile impostare la configurazione del poligono di zona nel `SPACEANALYTICS_CONFIG` campo.

Si tratta di un frame video di esempio che Mostra come vengono calcolate le coordinate del vertice per un frame di dimensioni pari a 1920/1080.
![Fotogramma video di esempio](./media/spatial-analysis/sample-video-frame.jpg)

È anche possibile selezionare una soglia di confidenza quando vengono conteggiati gli utenti rilevati e vengono generati gli eventi. Se si desidera che tutti gli eventi vengano restituiti, impostare la soglia su 0.

### <a name="execute-the-deployment"></a>Eseguire la distribuzione

Ora che il [manifesto di distribuzione](https://go.microsoft.com/fwlink/?linkid=2142179) è completo, usare questo comando nell'interfaccia della riga di comando di Azure per distribuire il contenitore nel computer host come modulo IOT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Specificare i parametri obbligatori:

* Nome dell'hub Internet: nome dell'hub di Azure
* DeploymentManifest.js: il nome del file di distribuzione
* Nome dispositivo IoT Edge: il nome del dispositivo IoT Edge del computer host
* Sottoscrizione: l'ID o il nome della sottoscrizione

Questo comando inizierà la distribuzione ed è possibile visualizzare lo stato della distribuzione nell'istanza dell'hub Azure Internet nel portale di Azure. Lo stato può essere visualizzato come *417: la configurazione della distribuzione del dispositivo non viene impostata* fino a quando il dispositivo non completa il download delle immagini del contenitore e avvia l'esecuzione.

### <a name="validate-that-the-deployment-was-successful"></a>Verificare che la distribuzione sia stata completata correttamente

Individuare lo *stato di runtime* nelle impostazioni del modulo IOT Edge per il modulo Spatial-Analysis nell'istanza dell'hub internet nel portale di Azure. Il **valore desiderato** e il **valore restituito** per lo *stato di runtime* dovrebbero indicare `Running` . Per informazioni sul portale di Azure, vedere di seguito.

![Esempio di verifica della distribuzione](./media/spatial-analysis/deployment-verification.png)

A questo punto, il contenitore di analisi spaziale esegue l'operazione. Emette informazioni dettagliate di intelligenza artificiale per l' `cognitiveservices.vision.spatialanalysis-personcount` operazione e instrada tali informazioni come dati di telemetria all'istanza dell'hub Azure. Per configurare altre fotocamere, è possibile aggiornare il file [manifesto di distribuzione](https://go.microsoft.com/fwlink/?linkid=2142179) ed eseguire di nuovo la distribuzione.

## <a name="person-counting-web-application"></a>Applicazione Web di conteggio persone

Questa utente che conta l'applicazione Web consente di configurare rapidamente un'app Web di esempio e di ospitarla nell'ambiente Azure.

### <a name="get-the-person-counting-app-container"></a>Ottenere il contenitore di app per il conteggio delle persone

Un modulo contenitore di questa app disponibile nel Container Registry di Azure. Usare il comando docker pull seguente per scaricarlo. Contattare Microsoft all'indirizzo projectarchon@microsoft.com per il token di accesso.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Eseguire il push del contenitore nel Container Registry di Azure (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Per installare il contenitore, creare una nuova app Web per contenitori di Azure e specificare i parametri necessari. Passare quindi alla scheda **Docker** e selezionare **singolo contenitore**, quindi **container Registry di Azure**. Usare l'istanza di Azure Container Registry in cui è stato eseguito il push dell'immagine sopra.

![Immettere i dettagli dell'immagine](./media/spatial-analysis/solution-app-create-screen.png)

Dopo aver immesso i parametri precedenti, fare clic su **Verifica + crea** e crea l'app.

### <a name="configure-the-app"></a>Configurare l'app 

Attendere il completamento dell'installazione e passare alla risorsa nella portale di Azure. Passare alla sezione di **configurazione** e aggiungere le seguenti due **impostazioni dell'applicazione**.

* `EventHubConsumerGroup` : Il nome di stringa del gruppo di consumer dall'hub di Azure, è possibile creare un nuovo gruppo di consumer nell'hub Internet o usare il gruppo predefinito. 
* `IotHubConnectionString` : La stringa di connessione all'hub di Azure, che può essere recuperata dalla sezione chiavi della risorsa Hub Azure per ![ configurare i parametri](./media/spatial-analysis/solution-app-config-page.png)

Una volta aggiunte queste due impostazioni, fare clic su **Salva**. Quindi fare clic su **autenticazione/autorizzazione** nel menu di spostamento a sinistra e aggiornarlo con il livello di autenticazione desiderato. È consigliabile usare Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>Testare l'app

Passare all'app Web di Azure e verificare che la distribuzione sia stata completata correttamente e che l'app Web sia in esecuzione. Passare all'URL configurato: `<yourapp>.azurewebsites.net` per visualizzare l'app in esecuzione.

![Test della distribuzione](./media/spatial-analysis/solution-app-output.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le operazioni di analisi spaziale](./spatial-analysis-operations.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
