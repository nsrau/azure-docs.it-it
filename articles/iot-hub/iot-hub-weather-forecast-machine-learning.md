---
title: Previsioni meteorologiche con Azure Machine Learning Studio (classico) con dati dell'hub Internet
description: Usare Azure Machine Learning Studio (classico) per prevedere la probabilità di pioggia in base ai dati di temperatura e umidità raccolti da un sensore.
author: robinsh
manager: philmea
keywords: previsioni meteo machine learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 8ba68e56d2475b1ff2fb3e63f291f76063ca62e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777157"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Previsioni meteorologiche che usano i dati del sensore dell'hub Internet in Azure Machine Learning Studio (versione classica)

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

L'apprendimento automatico o machine learning è una tecnica di analisi scientifica dei dati che consente ai computer di apprendere dai dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Azure Machine Learning Studio (versione classica) è un servizio di analisi predittiva basato sul cloud che consente di creare e distribuire rapidamente modelli predittivi come soluzioni di analisi.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprenderà come usare Azure Machine Learning Studio (classico) per le previsioni meteorologiche (probabilità di pioggia) usando i dati di temperatura e umidità dall'hub Azure. La probabilità di pioggia è l'output di un modello preparato di previsioni meteo. Il modello usa dati cronologici per prevedere la probabilità di pioggia in base alla temperatura e all'umidità.

## <a name="what-you-do"></a>Operazioni da fare

- Distribuire il modello di previsioni meteo come servizio Web.
- Preparare l'hub IoT per l'accesso dei dati mediante l'aggiunta di un gruppo di consumer.
- Creare un processo di Analisi di flusso e configurarlo per:
  - Leggere i dati di temperatura e umidità dall'hub IoT.
  - Chiamare il servizio Web per definire la probabilità di pioggia.
  - Salvare il risultato in un'archiviazione BLOB di Azure.
- Usare Microsoft Azure Storage Explorer per visualizzare le previsioni meteo.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione del [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sui dispositivi; ad esempio, [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Sono previsti i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi ad Azure IoT hub.
- Un account di [Azure Machine Learning Studio (versione classica)](https://studio.azureml.net/).
- È preferibile un account di [archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json#types-of-storage-accounts), un account per **utilizzo generico V2** , ma anche qualsiasi account di archiviazione di Azure che supporta l'archiviazione BLOB di Azure.

> [!Note]
> Questo articolo USA analisi di flusso di Azure e molti altri servizi a pagamento. Sono stati addebitati costi aggiuntivi in analisi di flusso di Azure quando i dati devono essere trasferiti tra le aree di Azure. Per questo motivo, è opportuno assicurarsi che il gruppo di risorse, l'hub di Internet e l'account di archiviazione di Azure, oltre all'area di lavoro Machine Learning Studio (classica) e al processo di analisi di flusso di Azure aggiunto più avanti in questa esercitazione, si trovino tutti nella stessa area di Azure. È possibile controllare il supporto a livello di area per Azure Machine Learning Studio (classico) e altri servizi di Azure nella [pagina disponibilità del prodotto Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuire il modello di previsioni meteo come servizio Web

In questa sezione si ottiene il modello di previsione meteorologica dalla libreria di intelligenza artificiale di Azure. Aggiungere quindi un modulo R-script al modello per ripulire i dati di temperatura e umidità. Il modello verrà infine distribuito come servizio Web predittivo.

### <a name="get-the-weather-prediction-model"></a>Ottenere il modello di previsioni meteo

In questa sezione si ottiene il modello di previsioni meteo da Azure AI Gallery e lo si apre in Azure Machine Learning Studio (versione classica).

1. Andare alla [pagina del modello di previsioni meteo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Aprire la pagina del modello di previsioni meteo in Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Selezionare **Apri in studio (classico)** per aprire il modello in Microsoft Azure Machine Learning Studio (classico). Selezionare un'area vicino all'hub Internet e l'area di lavoro corretta nell' **esperimento di copia dalla** finestra popup della raccolta.

   ![Aprire il modello di previsioni meteo in Azure Machine Learning Studio (versione classica)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Aggiungere quindi un modulo R-script per ripulire i dati di temperatura e umidità

Per il corretto funzionamento del modello, i dati di temperatura e umidità devono essere convertibili in dati numerici. In questa sezione viene aggiunto un modulo R-script al modello di previsioni meteo che rimuove tutte le righe con valori di dati per la temperatura o l'umidità che non possono essere convertite in valori numerici.

1. Sul lato sinistro della finestra Azure Machine Learning Studio (classica), selezionare la freccia per espandere il pannello strumenti. Immettere "Esegui" nella casella di ricerca. Selezionare il modulo **Esegui script R**.

   ![Selezionare il modulo Esegui script R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Trascinare il modulo **Esegui script R** accanto al modulo **Pulisci dati mancanti** e al modulo **Esegui script R** esistente nel diagramma. Eliminare la connessione tra i moduli **Pulisci i dati mancanti** ed **Esegui script R**, quindi connettere gli input e gli output del nuovo modulo come illustrato.

   ![Aggiungere il modulo Esegui script R](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selezionare il modulo nuovo **Esegui script R** per aprire la relativa finestra delle proprietà. Copiare e incollare il codice seguente nella casella **Script R**.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Al termine, la finestra Proprietà avrà un aspetto simile a quello riportato di seguito.

   ![Aggiungere codice al modulo Esegui script R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Distribuire un servizio Web predittivo

In questa sezione viene convalidato il modello, viene configurato un servizio Web predittivo in base al modello, quindi viene distribuito il servizio Web.

1. Selezionare **Esegui** per convalidare i passaggi nel modello. Il completamento di questo passaggio potrebbe richiedere alcuni minuti.

   ![Eseguire l'esperimento per convalidare i passaggi](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Selezionare **set up Web Service**  >  **Predictive Web Service**. Viene visualizzato il diagramma dell'esperimento predittivo.

   ![Distribuire il modello di previsioni meteo in Azure Machine Learning Studio (versione classica)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Nel diagramma dell'esperimento predittivo, eliminare la connessione tra il modulo di **input del servizio Web** e le **colonne SELECT nel set di dati** nella parte superiore. Quindi trascinare il modulo **Web service input** (Input servizio Web) accanto al modulo **Score Model** (Modello di punteggio) e collegarli come indicato di seguito.

   ![Collegare due moduli in Azure Machine Learning Studio (versione classica)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Selezionare **Esegui** per convalidare i passaggi nel modello.

1. Selezionare **Distribuisci servizio Web** per distribuire il modello come servizio Web.

1. Nel dashboard del modello scaricare **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versioni precedenti) per **REQUEST/RESPONSE** (Richiesta/risposta).

   > [!Note]
   > Verificare di scaricare la cartella **Excel 2010 or earlier workbook** anche se nel computer è in esecuzione una versione di Excel successiva.

   ![Scaricare la cartella di lavoro di Excel per l'endpoint REQUEST/RESPONSE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Aprire la cartella di lavoro di Excel e annotare i valori **WEB SERVICE URL** (URL servizio Web) e **ACCESS KEY** (Chiave di accesso).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nella [portale di Azure](https://portal.azure.com/)selezionare **Crea una risorsa**. Digitare "processo di analisi di flusso" nella casella di ricerca e selezionare **processo di analisi di flusso** dall'elenco a discesa risultati. Quando si apre il riquadro del **processo di analisi di flusso** , selezionare **Crea**.
1. Immettere le seguenti informazioni per il processo.

   **Nome processo**: Nome del processo. Il nome deve essere univoco a livello globale.

   **Sottoscrizione**: selezionare la sottoscrizione se è diversa da quella predefinita.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: usare lo stesso percorso del gruppo di risorse.

   Lasciare i valori predefiniti per tutti gli altri campi.

   ![Creare un processo di analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Selezionare **Crea**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

1. Aprire il processo di analisi di flusso.
1. In **Topologia processo** selezionare **Input**.
1. Nel riquadro **input** selezionare **Aggiungi input di flusso**, quindi selezionare **Hub** tutto dall'elenco a discesa. Nel riquadro **nuovo input** , scegliere l'hub Internet delle cose **dalle sottoscrizioni** e immettere le informazioni seguenti:

   **Alias di input**: l'alias univoco per l'input.

   **Sottoscrizione**: selezionare la sottoscrizione se è diversa da quella predefinita.

   **Hub**Internet delle cose: selezionare l'hub Internet delle cose dalla sottoscrizione.

   **Nome dei criteri di accesso condiviso**: selezionare il  **servizio**. È anche possibile usare **iothubowner**.

   **Gruppo di consumer**: selezionare il gruppo di consumer creato.

   Lasciare i valori predefiniti per tutti gli altri campi.

   ![Aggiungere un input al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Selezionare **Salva**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** selezionare **Output**.
1. Nel riquadro **output** selezionare **Aggiungi**, quindi selezionare **archiviazione BLOB/data Lake storage** dall'elenco a discesa. Nel riquadro **nuovo output** scegliere il **selezionare archiviazione dalle sottoscrizioni** e immettere le informazioni seguenti:

   **Alias di output**: l'alias univoco per l'output.

   **Sottoscrizione**: selezionare la sottoscrizione se è diversa da quella predefinita.

   **Account di archiviazione**: l'account per l'archiviazione BLOB. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

   **Contenitore**: il contenitore in cui viene salvato l'archivio BLOB. È possibile usare un contenitore esistente o crearne uno nuovo.

   **Formato di serializzazione eventi**: Selezionare **CSV**.

   ![Aggiungere un output al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Selezionare **Salva**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Aggiungere una funzione al processo di Analisi di flusso per chiamare il servizio Web che è stato distribuito

1. In **topologia processo**selezionare **funzioni**.
1. Nel riquadro **funzioni** selezionare **Aggiungi**e quindi selezionare **Azure ml Studio** dall'elenco a discesa. Assicurarsi di selezionare **azure ml Studio**, non il **servizio Azure ml**. Nel riquadro **nuova funzione** scegliere **specificare le impostazioni della funzione Azure Machine Learning manualmente** e immettere le informazioni seguenti:

   **Alias di funzione**: Immettere `machinelearning`.

   **URL**: immettere l'URL del servizio Web annotato dalla cartella di lavoro di Excel.

   **Chiave**: immettere la chiave di accesso annotata in precedenza dalla cartella di lavoro di Excel.

   ![Aggiungere una funzione al processo di Analisi di flusso in Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Selezionare **Salva**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** selezionare **Query**.
1. Sostituire il codice esistente con il seguente:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Sostituire `[YourInputAlias]` con l'alias di input del processo.

   Sostituire `[YourOutputAlias]` con l'alias di output del processo.

1. Selezionare **Salva query**.

> [!Note]
> Se si seleziona **query di test**, verrà visualizzato il messaggio seguente: il test delle query con funzioni Machine Learning non è supportato. Modificare la query e riprovare. È possibile ignorare questo messaggio e selezionare **OK** per chiudere la finestra di messaggio. Assicurarsi di salvare la query prima di procedere alla sezione successiva.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di analisi di flusso selezionare **Panoramica** nel riquadro sinistro. Quindi selezionare **Avvia**  >  **ora**  >  **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

![Eseguire il processo di Analisi di flusso](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Usare Microsoft Azure Storage Explorer per visualizzare le previsioni meteo

Eseguire l'applicazione client per avviare la raccolta e l'invio dei dati di temperatura e umidità all'hub IoT. Ogni volta che l'hub IoT riceve un messaggio il processo di Analisi di flusso chiama il servizio Web di previsioni meteo per elaborare la probabilità di pioggia. Il risultato viene quindi salvato nell'archiviazione BLOB di Azure. Azure Storage Explorer è uno strumento che consente di visualizzare il risultato.

1. [Scaricare e installare Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Aprire Azure Storage Explorer.
1. Accedere all'account Azure.
1. Selezionare la propria sottoscrizione.
1. Selezionare la sottoscrizione > **account di archiviazione** > account di archiviazione > **contenitori BLOB** > contenitore.
1. Scaricare un file con estensione .csv per visualizzare il risultato. L'ultima colonna registra la probabilità di pioggia.

   ![Ottenere i risultati delle previsioni meteorologiche con Azure Machine Learning Studio (versione classica)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Riepilogo

È stato usato correttamente Azure Machine Learning Studio (classico) per produrre la probabilità di pioggia in base ai dati di temperatura e umidità ricevuti dall'hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
