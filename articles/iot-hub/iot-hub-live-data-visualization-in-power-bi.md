---
title: Visualizzazione in tempo reale dei dati del sensore dall&quot;hub IoT di Azure - Power BI | Microsoft Docs
description: "Usare Power BI per visualizzare i dati di temperatura e umidità raccolti dal sensore e inviati all&quot;hub IoT di Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: visualizzazione dei dati in tempo reale, visualizzazione dei dati dal vivo, visualizzazione dei dati del sensore
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6a99749a96a6239428e5b018a26a6e8fd440c9d2
ms.lasthandoff: 04/12/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizzare i dati del sensore in tempo reale da IoT Hub di Azure tramite Power BI

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come visualizzare i dati del sensore in tempo reale che l'hub IoT di Azure riceve da Power BI. Se si desidera provare a visualizzare i dati nell'hub IoT con le app Web, consultare [Usare App Web di Azure per visualizzare i dati del sensore in tempo reale da Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Operazioni da fare

- Preparare l'hub IoT per l'accesso dei dati mediante l'aggiunta di un gruppo di consumer.
- Creare, configurare ed eseguire un processo di analisi di flusso per il trasferimento dei dati dall'hub IoT all'account Power BI.
- Creare e pubblicare un report di Power BI per visualizzare i dati.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Collegare ESP8266 all'hub IoT di Azure](iot-hub-arduino-huzzah-esp8266-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi all'hub IoT di Azure.
- Un account di Power BI. ([Provare gratuitamente Power BI](https://powerbi.microsoft.com/))

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. In questa lezione viene creato un gruppo di consumer che il processo di analisi di flusso userà per leggere i dati dall'hub IoT.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://ms.portal.azure.com/), aprire l'hub IoT.
1. Fare clic su **Endpoint** nel riquadro a sinistra, selezionare **Eventi** nel riquadro centrale, immettere un nome in **Gruppi di consumer** nel riquadro a destra, quindi **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT di Azure](media/iot-hub-live-data-visualization-in-power-bi/1_iot-hub-create-consumer-group-azure.png)

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel portale di Azure, fare clic su Nuovo > Internet delle cose > Processo di Analisi di flusso.
1. Immettere le seguenti informazioni per il processo.

   **Nome processo**: il nome del processo. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Percorso**: utilizzare lo stesso percorso del gruppo di risorse.

   **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

   ![Creare un processo di analisi di flusso in Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Fare clic su **Crea**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di analisi di flusso

1. Aprire il processo di analisi di flusso.
1. In **Topologia processo** fare clic su **Input**.
1. Nel riquadro **Input** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di input**: l'alias univoco per l'input.

   **Origine**: selezionare **Hub IoT**.

   **Gruppo di consumer**: selezionare il gruppo di consumer appena creato.
1. Fare clic su **Crea**.

   ![Aggiungere un input al processo di analisi di flusso in Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di analisi di flusso

1. In **Topologia processo** fare clic su **Output**.
1. Nel riquadro **Output** fare clic su **Aggiungi**, quindi immettere le informazioni seguenti:

   **Alias di output**: l'alias univoco per l'output.

   **Sink**: selezionare **Power BI**.
1. Fare clic su **Autorizza** e quindi accedere all'account di Power BI.
1. Dopo aver concesso l'autorizzazione immettere le seguenti informazioni:

   **Area di lavoro del gruppo**: selezionare l'area di lavoro del gruppo di destinazione.

   **Nome set di dati**: immettere un nome per il set di dati.

   **Nome tabella**: immettere un nome per la tabella.
1. Fare clic su **Crea**.

   ![Aggiungere un output al processo di analisi di flusso in Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di analisi di flusso

1. In **Topologia processo** fare clic su **Query**.
1. Sostituire `[YourInputAlias]` con l'alias di input del processo.
1. Sostituire `[YourOutputAlias]` con l'alias di output del processo.
1. Fare clic su **Salva**.

   ![Aggiungere una query al processo di analisi di flusso in Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di analisi di flusso, **Avvia** > **Ora** > **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

![Eseguire un processo di analisi di flusso in Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Creare e pubblicare un report di Power BI per visualizzare i dati

1. Verificare che l'applicazione di esempio sia in esecuzione. In caso contrario, eseguire questo comando per eseguire l'applicazione in Pi:

   ```bash
   gulp run
   ```
1. Accedere all'account [Power BI](https://powerbi.microsoft.com/en-us/).
1. Passare all'area di lavoro del gruppo impostata quando è stato creato l'output del processo di analisi di flusso.
1. Fare clic su **Set di dati in streaming**.

   Dovrebbero essere elencati i set di dati specificati durante la creazione di output per il processo di analisi di flusso.
1. In **AZIONI**, fare clic sulla prima icona per creare un report.

   ![Creare un report di Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Creare un grafico a linee per visualizzare la temperatura in tempo reale nel tempo.
   1. Nella pagina di creazione del report, aggiungere un grafico a linee.
   1. Nel riquadro **Campi** espandere la tabella specificata durante la creazione di output per il processo di analisi di flusso.
   1. Trascinare **EventEnqueuedUtcTime** in **Asse** sul riquadro **Visualizzazioni**.
   1. Trascinare la **temperatura** in **Valori**.

      A questo punto viene creato un grafico a linee. L'asse x del grafico mostra la data e ora per il fuso orario UTC. L'asse y mostra la temperatura dal sensore.

      ![Aggiungere un grafico a linee per la temperatura a un report di Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Creare un altro grafico a linee in modo da visualizzare in tempo reale l'umidità nel tempo. A tale scopo, attenersi alla stessa procedura precedente e inserire **EventEnqueuedUtcTime** sull'asse x e l'**umidità** sull'asse y.

   ![Aggiungere un grafico a linee per l'umidità a un report di Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Fare clic su **Salva** per salvare il report.
1. Fare clic su **File** > **Pubblica sul Web**.
1. Fare clic su **Crea codice di incorporamento**, quindi fare clic su **Pubblica**.

Viene indicato il collegamento al report che è possibile condividere con utenti che debbano accedere al report e un frammento di codice per integrare il report nel blog o nel sito Web.

![Pubblicare un report di Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft offre anche le [App per dispositivi mobili di Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) per la visualizzazione e l'interazione con i dashboard di Power BI e i report sul tuo dispositivo mobile.

## <a name="next-steps"></a>Passaggi successivi

Si è utilizzato correttamente Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure.
Esiste un modo alternativo per visualizzare i dati dall'hub IoT di Azure. Vedere [Usare App Web di Azure per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
