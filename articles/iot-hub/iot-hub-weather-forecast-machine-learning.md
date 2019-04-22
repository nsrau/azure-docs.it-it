---
title: Previsioni meteo usando Azure Machine Learning con i dati dell'hub IoT | Microsoft Docs
description: Usare Azure Machine Learning per stimare la probabilità di pioggia in base ai dati di temperatura e umidità che l'hub IoT riceve da un sensore.
author: robinsh
manager: philmea
keywords: previsioni meteo machine learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: ffc2e5fb588ce6861f5df6cefdf810c1a015c043
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684039"
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

- Completare la [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) esercitazione, oppure una delle esercitazioni dispositivo; ad esempio [Raspberry Pi con Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi descrivono i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi ad Azure IoT hub.
- Un account di Azure Machine Learning Studio. ([Prova gratuita di Machine Learning Studio](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuire il modello di previsioni meteo come servizio Web

1. Andare alla [pagina del modello di previsioni meteo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Fare clic su **Apri in Studio** in Microsoft Azure Machine Learning Studio.
   ![Aprire la pagina del modello di previsioni meteo in Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Fare clic su **RUN** (Esegui) per convalidare i passaggi nel modello. Il completamento di questo passaggio può richiedere fino a 2 minuti.
   ![Aprire il modello di previsioni meteo in Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Fare clic su **SET UP WEB SERVICE** (Imposta servizio Web) > **Predictive Web Service** (Servizio Web predittivo).
   ![Distribuire il modello di previsioni meteo in Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Nel diagramma trascinare il modulo **Web service input** (Input servizio Web) accanto al modulo **Score Model** (Modello di punteggio).
1. Collegare il modulo **Web service input** (Input servizio Web) al modulo **Score Model** (Modello di punteggio).
   ![Collegare due moduli in Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Fare clic su **RUN** (Esegui) per convalidare i passaggi nel modello.
1. Fare clic su **DEPLOY WEB SERVICE** (Distribuisci servizio Web) per distribuire il modello come servizio Web.
1. Nel dashboard del modello scaricare **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versioni precedenti) per **REQUEST/RESPONSE** (Richiesta/risposta).

   > [!Note]
   > Verificare di scaricare la cartella **Excel 2010 or earlier workbook** anche se nel computer è in esecuzione una versione di Excel successiva.

   ![Scaricare la cartella di lavoro di Excel per l'endpoint REQUEST/RESPONSE](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Aprire la cartella di lavoro di Excel e annotare i valori **WEB SERVICE URL** (URL servizio Web) e **ACCESS KEY** (Chiave di accesso).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.
1. Immettere le seguenti informazioni per il processo.

   **Nome processo**: Nome del processo. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: usare lo stesso percorso del gruppo di risorse.

   **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

   ![Creare un processo di analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Fare clic su **Create**(Crea).

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di analisi di flusso

1. Aprire il processo di analisi di flusso.
1. In **Topologia processo** fare clic su **Input**.
1. Nel riquadro **Input** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di input**: L'alias univoco per l'input.

   **Origine**: Selezionare **Hub IoT**.

   **Gruppo di consumer**: Selezionare il gruppo di consumer creato.

   ![Aggiungere un input al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Fare clic su **Create**(Crea).

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di analisi di flusso

1. In **Topologia processo** fare clic su **Output**.
1. Nel riquadro **Output** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di output**: l'alias univoco per l'output.

   **Sink**: Selezionare **archiviazione Blob**.

   **Account di archiviazione**: L'account di archiviazione per l'archivio blob. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

   **Contenitore**: Il contenitore in cui è salvato il blob. È possibile usare un contenitore esistente o crearne uno nuovo.

   **Formato di serializzazione eventi**: Selezionare **CSV**.

   ![Aggiungere un output al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Fare clic su **Create**(Crea).

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Aggiungere una funzione al processo di Analisi di flusso per chiamare il servizio Web che è stato distribuito

1. In **Topologia processo** fare clic su **Funzioni** > **Aggiungi**.
1. Immettere le seguenti informazioni:

   **Alias della funzione**: Immettere `machinelearning`.

   **Tipo di funzione**: Selezionare **Azure ML**.

   **Opzione di importazione**: Selezionare **Importa da un'altra sottoscrizione**.

   **URL**: Immettere l'URL del servizio WEB annotato dalla cartella di lavoro di Excel.

   **Chiave**: Immettere la chiave di accesso che si è preso nota della cartella di lavoro di Excel.

   ![Aggiungere una funzione al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Fare clic su **Create**(Crea).

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di analisi di flusso

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
1. Aprire il file con estensione csv per visualizzare il risultato. L'ultima colonna registra la probabilità di pioggia.

   ![Ottenere i risultati delle previsioni meteo con Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Summary

Azure Machine Learning è stato usato per stimare la probabilità di pioggia in base ai dati di temperatura e umidità ricevuti dall'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]