---
title: "Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure"
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un processo di Analisi di flusso di Azure.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/01/2020
ms.openlocfilehash: 1613486880885a3b7838b1bf806c17f88e3be06d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231268"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per definire un processo di Analisi di flusso che filtra in tempo reale i messaggi del sensore contenenti letture della temperatura maggiori di 27. Il processo di Analisi di flusso leggerà i dati dall'hub IoT, li trasformerà e quindi li scriverà in un contenitore nell'archiviazione BLOB. I dati di input usati in questo argomento di avvio rapido sono generati da un simulatore online Raspberry Pi.

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

   Accedere usando il comando [az login](/cli/azure/reference-index#az-login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

2. Installare l'estensione dell'interfaccia della riga di comando di Azure.

   Quando si usano riferimenti a estensioni per l'interfaccia della riga di comando di Azure, è prima di tutto necessario installare l'estensione.  Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base.  Per altre informazioni sulle estensioni, incluse le procedure di aggiornamento e disinstallazione, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

   Eseguire questo comando per installare l'[estensione per Analisi di flusso](/cli/azure/ext/stream-analytics/stream-analytics):

    ```azurecli
    az extension add --name stream-analytics
    ```

   Eseguire questo comando per installare l'[estensione per Azure IoT](/cli/azure/ext/azure-iot):

    ```azurecli
    az extension add --name azure-iot
    ```

3. Creare un gruppo di risorse.

   Tutte le risorse di Azure devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

   Per questo argomento di avvio rapido, creare un gruppo di risorse denominato *streamanalyticsrg* nell'area *eastus* con il comando [az group create](/cli/azure/group#az-group-create) seguente:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Preparare i dati di input

Prima di definire il processo di Analisi di flusso, è necessario preparare i dati che verranno usati come input del processo.

I blocchi di codice dell'interfaccia della riga di comando di Azure riportati di seguito sono comandi per preparare i dati di input richiesti dal processo. Esaminare le sezioni per comprendere il codice.

1. Creare un hub IoT con il comando [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). Questo esempio crea un hub IoT denominato **MyASAIoTHub**. Poiché i nomi degli hub IoT sono univoci, è necessario indicare un nome personalizzato per il proprio hub IoT. Impostare lo SKU su F1 per usare il livello gratuito se è disponibile con la sottoscrizione. In caso contrario, scegliere il successivo livello più basso.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Dopo aver creato l'hub IoT, ottenere la stringa di connessione dell'hub IoT usando il comando [az iot hub show-connection-string](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest). Copiare l'intera stringa di connessione e salvarla per quando si aggiungerà l'hub IoT come input nel processo di Analisi di flusso di Azure.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Aggiungere un dispositivo all'hub IoT usando il comando [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Questo esempio crea un dispositivo denominato **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Ottenere la stringa di connessione del dispositivo usando il comando [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Copiare l'intera stringa di connessione e salvarla per quando si creerà il simulatore Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Esempio di output:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Creare un account di archiviazione BLOB

I blocchi di codice dell'interfaccia della riga di comando di Azure seguenti creano un account di archiviazione BLOB usato per l'output del processo. Esaminare le sezioni per comprendere il codice.

1. Creare un account di archiviazione per utilizzo generico con il comando [az storage account create](/cli/azure/storage/account). L'account di archiviazione per utilizzo generico può essere usato per tutti e quattro i servizi: BLOB, file, tabelle e code.

   È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Per ottenere la chiave dell'account di archiviazione, eseguire il comando[az storage account keys list](/cli/azure/storage/account/keys). Salvare la chiave per poterla usare nel passaggio successivo.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Creare un contenitore per l'archiviazione di BLOB con il comando [az storage container create](/cli/azure/storage/container). La chiave dell'account di archiviazione consente autorizzare l'operazione di creazione del contenitore. Per altre informazioni sull'autorizzazione delle operazioni sui dati con l'interfaccia della riga di comando di Azure, vedere [Autorizzare l'accesso ai dati di BLOB o code con l'interfaccia della riga di comando di Azure](/azure/storage/common/authorize-data-operations-cli).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

I blocchi di codice dell'interfaccia della riga di comando di Azure seguenti creano un processo di Analisi di flusso. Esaminare le sezioni per comprendere il codice

1. Creare un processo di Analisi di flusso con il comando [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-job-create).

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Configurare l'input per il processo

Aggiungere un input al processo tramite il cmdlet [az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-input-create). Questo cmdlet accetta il nome del processo, il nome dell'input del processo, il nome del gruppo di risorse e la definizione dell'input del processo come parametri. La definizione dell'input del processo è un file JSON che contiene le proprietà necessarie per configurare l'input del processo. In questo esempio come input verrà creato un hub IoT.

Nel computer locale creare un file denominato `datasource.json` e aggiungere i dati JSON seguenti. Assicurarsi di sostituire il valore di `sharedAccessPolicyKey` con la parte `SharedAccessKey` della stringa di connessione dell'hub IoT salvato in una sezione precedente.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Nel computer locale creare un file denominato `serialization.json` e aggiungere i dati JSON seguenti.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Eseguire quindi il cmdlet `az stream-analytics input create`. Assicurarsi di sostituire il valore della variabile `datasource` con il percorso in cui è stato archiviato il file JSON di definizione dell'input del processo e il valore della variabile `serialization` con il percorso in cui è stato archiviato il file JSON di serializzazione.

```azurecli
az stream-analytics input create 
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Configurare l'output per il processo

Aggiungere un output al processo tramite il cmdlet [az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-output-create). Questo cmdlet accetta il nome del processo, il nome dell'output del processo, il nome del gruppo di risorse e la definizione dell'output del processo come parametri. La definizione dell'output del processo è un file JSON che contiene le proprietà necessarie per configurare l'output del processo. In questo esempio come output viene usato l'archivio BLOB.

Nel computer locale creare un file denominato `datasink.json` e aggiungere i dati JSON seguenti. Assicurarsi di sostituire il valore di `accountKey` con la chiave di accesso dell'account di archiviazione corrispondente al valore archiviato in $storageAccountKey.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Eseguire quindi il cmdlet `az stream-analytics output`. Assicurarsi di sostituire il valore della variabile `datasource` con il percorso in cui è stato archiviato il file JSON di definizione dell'output del processo e il valore della variabile `serialization` con il percorso in cui è stato archiviato il file JSON di serializzazione.

```azurecli
az stream-analytics output create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definire la query di trasformazione

Aggiungere una trasformazione al processo tramite il cmdlet [az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-transformation-create). Questo cmdlet accetta il nome del processo, il nome della trasformazione del processo, il nome del gruppo di risorse e la definizione della trasformazione del processo come parametri. 

Eseguire il cmdlet `az stream-analytics transformation create`.

```azurecli
az stream-analytics transformation create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Eseguire il simulatore IoT

1. Aprire il [simulatore online Azure IoT Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Sostituire il segnaposto nella riga 15 con l'intera stringa di connessione del dispositivo hub IoT di Azure salvato in una sezione precedente.

3. Fare clic su **Esegui**. L'output mostra i dati del sensore e i messaggi inviati all'hub IoT.

    ![Simulatore online Azure IoT Raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Avviare il processo di Analisi di flusso e controllare l'output

Avviare il processo usando il cmdlet [az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-job-start). Questo cmdlet accetta il nome del processo, il nome del gruppo di risorse, la modalità di avvio dell'output e l'ora di inizio come parametri. `OutputStartMode` accetta i valori `JobStartTime`, `CustomTime` o `LastOutputEventTime`.

Dopo aver eseguito il cmdlet seguente, verrà restituito `True` come output se il processo viene avviato. Nel contenitore di archiviazione viene creata una cartella di output con i dati trasformati.

```azurecli
az stream-analytics job start 
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile non eliminarlo e arrestarlo per il momento. Se non si intende continuare a usare il processo, eliminare tutte le risorse create con questo avvio rapido eseguendo il cmdlet seguente:

```powershell
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato distribuito un semplice processo di Analisi di flusso con l'interfaccia della riga di comando di Azure. È anche possibile distribuire processi di Analisi di flusso usando il [portale di Azure](stream-analytics-quick-create-portal.md) e [Visual Studio](stream-analytics-quick-create-vs.md).

Per informazioni sulla configurazione di altre origini di input e sull'esecuzione del rilevamento in tempo reale, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Rilevamento delle frodi in tempo reale tramite Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
