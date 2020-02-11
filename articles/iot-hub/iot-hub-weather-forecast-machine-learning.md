---
title: Previsioni meteorologiche usando Azure Machine Learning con i dati dell'hub Internet
description: Usare Azure Machine Learning per stimare la probabilità di pioggia in base ai dati di temperatura e umidità che l'hub IoT riceve da un sensore.
author: robinsh
manager: philmea
keywords: previsioni meteo machine learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122267"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Previsioni meteo usando i dati sensore dell'hub IoT in Azure Machine Learning

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

L'apprendimento automatico o machine learning è una tecnica di analisi scientifica dei dati che consente ai computer di apprendere dai dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Azure Machine Learning è un servizio di analisi predittiva basato sul cloud che consente di creare e distribuire rapidamente modelli predittivi come soluzioni di analisi.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprende come usare Azure Machine Learning per formulare previsioni meteo (possibilità di pioggia) usando i dati di temperatura e umidità dell'hub IoT di Azure. La probabilità di pioggia è l'output di un modello preparato di previsioni meteo. Il modello usa dati cronologici per prevedere la probabilità di pioggia in base alla temperatura e all'umidità.

## <a name="what-you-do"></a>Operazioni da fare

- Distribuire il modello di previsioni meteo come servizio Web.
- Preparare l'hub IoT per l'accesso dei dati mediante l'aggiunta di un gruppo di consumer.
- Creare un processo di Analisi di flusso e configurarlo per:
  - Leggere i dati di temperatura e umidità dall'hub IoT.
  - Chiamare il servizio Web per definire la probabilità di pioggia.
  - Salvare il risultato in un'archiviazione BLOB di Azure.
- Usare Microsoft Azure Storage Explorer per visualizzare le previsioni meteo.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione del [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sui dispositivi; ad esempio, [Raspberry Pi con node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi requisiti coprono i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi ad Azure IoT hub.
- Un account [Azure Machine Learning Studio (classico)](https://studio.azureml.net/) .

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuire il modello di previsioni meteo come servizio Web

In questa sezione si ottiene il modello di Previsione meteorologico dalla libreria di intelligenza artificiale di Azure. Si aggiunge quindi un modulo R-script al modello per pulire i dati di temperatura e umidità. Infine, il modello viene distribuito come servizio Web predittivo.

### <a name="get-the-weather-prediction-model"></a>Ottenere il modello Weather Prediction

In questa sezione si ottiene il modello di Previsione meteorologico dal Azure AI Gallery e lo si apre in Azure Machine Learning Studio (classico).

1. Andare alla [pagina del modello di previsioni meteo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Aprire la pagina Weather Prediction Model in Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Fare clic su **Apri in studio (classico)** per aprire il modello in Microsoft Azure Machine Learning Studio (classico).

   ![Aprire il modello di Previsione meteorologico in Azure Machine Learning Studio (classico)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Aggiungere un modulo R-script per pulire i dati di temperatura e umidità

Per il corretto funzionamento del modello, i dati di temperatura e umidità devono essere convertibili in dati numerici. In questa sezione viene aggiunto un modulo R-script al modello Weather Prediction che rimuove tutte le righe con valori di dati per la temperatura o l'umidità che non possono essere convertite in valori numerici.

1. Sul lato sinistro della finestra di Azure Machine Learning Studio fare clic sulla freccia per espandere il pannello strumenti. Immettere "Execute" nella casella di ricerca. Selezionare il modulo **Execute R script** .

   ![Selezionare il modulo Execute R script](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Trascinare il modulo **Execute r script** accanto al modulo **Clean Missing data** e il modulo **Execute r script** esistente nel diagramma. Eliminare la connessione tra i **dati mancanti puliti** e i moduli **Execute R script** , quindi connettere gli input e gli output del nuovo modulo come illustrato.

   ![Aggiungi modulo Execute R script](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selezionare il nuovo modulo **Execute R script (Esegui script R** ) per aprire la relativa finestra Proprietà. Copiare e incollare il codice seguente nella casella di **script R** .

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Al termine, la finestra Proprietà avrà un aspetto simile al seguente:

   ![Aggiungere il codice per eseguire il modulo di script R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Distribuire il servizio Web predittivo

In questa sezione il modello viene convalidato, viene configurato un servizio Web predittivo in base al modello, quindi viene distribuito il servizio Web.

1. Fare clic su **RUN** (Esegui) per convalidare i passaggi nel modello. Il completamento di questo passaggio può richiedere alcuni minuti.

   ![Eseguire l'esperimento per convalidare i passaggi](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Fare clic su **SET UP WEB SERVICE** (Imposta servizio Web) > **Predictive Web Service** (Servizio Web predittivo). Viene aperto il diagramma dell'esperimento predittivo.

   ![Distribuire il modello Weather Prediction in Azure Machine Learning Studio (classico)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Nel diagramma dell'esperimento predittivo eliminare la connessione tra il modulo **Web Service input** e il **set di dati Weather** nella parte superiore. Trascinare quindi il modulo di **input del servizio Web** in un punto vicino al modulo **Score Model** e connetterlo come illustrato di seguito:

   ![Connettere due moduli in Azure Machine Learning Studio (versione classica)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Fare clic su **RUN** (Esegui) per convalidare i passaggi nel modello.

1. Fare clic su **DEPLOY WEB SERVICE** (Distribuisci servizio Web) per distribuire il modello come servizio Web.

1. Nel dashboard del modello scaricare **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versioni precedenti) per **REQUEST/RESPONSE** (Richiesta/risposta).

   > [!Note]
   > Assicurarsi di scaricare la cartella di **lavoro di excel 2010 o precedente** anche se nel computer è in esecuzione una versione di Excel successiva.

   ![Scaricare la cartella di lavoro di Excel per l'endpoint REQUEST/RESPONSE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Aprire la cartella di lavoro di Excel e annotare i valori **WEB SERVICE URL** (URL servizio Web) e **ACCESS KEY** (Chiave di accesso).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.
1. Immettere le seguenti informazioni per il processo.

   **Nome processo**: il nome del processo. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Percorso**: utilizzare lo stesso percorso del gruppo di risorse.

   **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

   ![Creare un processo di analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Fare clic su **Crea**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

1. Aprire il processo di analisi di flusso.
1. In **Topologia processo** fare clic su **Input**.
1. Nel riquadro **Input** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di input**: l'alias univoco per l'input.

   **Origine**: selezionare **Hub IoT**.

   **Gruppo di consumer**: selezionare il gruppo di consumer creato.

   ![Aggiungere un input al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Fare clic su **Crea**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Output**.
1. Nel riquadro **Output** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di output**: l'alias univoco per l'output.

   **Sink**: selezionare **Archivio BLOB**.

   **Account di archiviazione**: l'account per l'archiviazione BLOB. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

   **Contenitore**: il contenitore in cui viene salvato l'archivio BLOB. È possibile usare un contenitore esistente o crearne uno nuovo.

   **Formato di serializzazione eventi**: selezionare **CSV**.

   ![Aggiungere un output al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Fare clic su **Crea**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Aggiungere una funzione al processo di Analisi di flusso per chiamare il servizio Web che è stato distribuito

1. In **Topologia processo** fare clic su **Funzioni** > **Aggiungi**.
1. Immettere le seguenti informazioni:

   **Alias della funzione**: immettere `machinelearning`.

   **Tipo funzione**: selezionare **Azure ML**.

   **Opzione di importazione**: selezionare **Importa da un'altra sottoscrizione**.

   **URL**: immettere l'URL del servizio Web annotato dalla cartella di lavoro di Excel.

   **Chiave**: immettere la chiave di accesso annotata in precedenza dalla cartella di lavoro di Excel.

   ![Aggiungere una funzione al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Fare clic su **Crea**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Query**.
1. Sostituire il codice esistente con il seguente:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Sostituire `[YourInputAlias]` con l'alias di input del processo.

   Sostituire `[YourOutputAlias]` con l'alias di output del processo.

1. Fare clic su **Save**.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di analisi di flusso, **Avvia** > **Ora** > **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

![Eseguire il processo di Analisi di flusso](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Usare Microsoft Azure Storage Explorer per visualizzare le previsioni meteo

Eseguire l'applicazione client per avviare la raccolta e l'invio dei dati di temperatura e umidità all'hub IoT. Ogni volta che l'hub IoT riceve un messaggio il processo di Analisi di flusso chiama il servizio Web di previsioni meteo per elaborare la probabilità di pioggia. Il risultato viene quindi salvato nell'archiviazione BLOB di Azure. Azure Storage Explorer è uno strumento che consente di visualizzare il risultato.

1. [Scaricare e installare Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Aprire Azure Storage Explorer.
1. Accedere all'account Azure.
1. Selezionare la propria sottoscrizione.
1. Fare clic sulla sottoscrizione in uso > **Account di archiviazione** > account di archiviazione in uso > **contenitori BLOB** > contenitore BLOB in uso.
1. Scaricare un file con estensione CSV per visualizzare il risultato. L'ultima colonna registra la probabilità di pioggia.

   ![Ottenere i risultati delle previsioni meteo con Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Riepilogo

Azure Machine Learning è stato usato per stimare la probabilità di pioggia in base ai dati di temperatura e umidità ricevuti dall'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]