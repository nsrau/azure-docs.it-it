---
title: Visualizzazione in tempo reale dei dati del sensore dall'hub IoT di Azure - Power BI | Microsoft Docs
description: Usare Power BI per visualizzare i dati di temperatura e umidità raccolti dal sensore e inviati all'hub IoT di Azure.
author: robinsh
keywords: visualizzazione dei dati in tempo reale, visualizzazione dei dati dal vivo, visualizzazione dei dati del sensore
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065876"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizzare i dati del sensore in tempo reale da IoT Hub di Azure tramite Power BI

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come visualizzare i dati del sensore in tempo reale che l'hub IoT di Azure riceve usando Power BI. Se si desidera provare a visualizzare i dati nell'hub IoT con un'app web, vedere [Usa un'app web per visualizzare i dati del sensore in tempo reale dall'IoT Hub di Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Operazioni da fare

* Preparare l'hub IoT per l'accesso dei dati mediante l'aggiunta di un gruppo di consumer.

* Creare, configurare ed eseguire un processo di analisi di flusso per il trasferimento dei dati dall'hub IoT all'account Power BI.

* Creare e pubblicare un report di Power BI per visualizzare i dati.

## <a name="what-you-need"></a>Elementi necessari

* Completare la [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) esercitazione, oppure una delle esercitazioni dispositivo; ad esempio [Raspberry Pi con Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi articoli descrivono i requisiti seguenti:
  
  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client che invia messaggi ad Azure IoT hub.

* Un account di Power BI. ([Provare gratuitamente Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

Iniziare creando un processo di Analisi di flusso. Dopo aver creato il processo, definire gli input, gli output e la query usata per recuperare i dati.

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

2. Immettere le seguenti informazioni per il processo.

   **Nome processo**: Nome del processo. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: usare lo stesso percorso del gruppo di risorse.

   ![Creare un processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selezionare **Create**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

1. Aprire il processo di analisi di flusso.

2. In **Topologia processo** selezionare **Input**.

3. Nel **input** riquadro, selezionare **Aggiungi input del flusso**, quindi selezionare **dell'IoT Hub** nell'elenco a discesa. Nel nuovo riquadro input, immettere le informazioni seguenti:

   **Alias di input**: Immettere un alias univoco per l'input.

   **Fornisce l'IoT Hub dalla sottoscrizione**: Selezionare questo pulsante di opzione.

   **Sottoscrizione** selezionare la sottoscrizione usata per questa esercitazione.

   **Hub IoT**: Selezionare l'IoT Hub si usa per questa esercitazione.

   **Endpoint**: selezionare **Messaggistica**.

   **Nome criteri di accesso condiviso**: Selezionare il nome dei criteri di accesso condiviso si desidera che il processo di Stream Analitica da utilizzare per l'hub IoT. Per questa esercitazione, è possibile selezionare *servizio*. Il *servizio* criterio viene creato per impostazione predefinita nei nuovi hub IoT e concede l'autorizzazione per inviare e ricevere negli endpoint lato cloud esposti dall'hub IoT. Per altre informazioni, vedere [controllo di accesso e autorizzazioni](iot-hub-devguide-security.md#access-control-and-permissions).

   **Chiave criteri di accesso condiviso**: Questo campo viene compilato automaticamente in base alla selezione per il nome dei criteri di accesso condiviso.

   **Gruppo di consumer**: Selezionare il gruppo di consumer creato in precedenza.

   Lasciare tutti gli altri campi impostate sui valori predefiniti.

   ![Aggiungere un input al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selezionare **Salva**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** selezionare **Output**.

2. Nel **Outputs** riquadro, selezionare **Add** e **Power BI**.

3. Nel **Power BI - nuovo output** riquadro, selezionare **autorizza** e seguire le istruzioni per accedere al proprio account Power BI.

4. Dopo che è stato effettuato l'accesso a Power BI, immettere le informazioni seguenti:

   **Alias di output**: Un alias univoco per l'output.

   **Area di lavoro del gruppo**: selezionare l'area di lavoro del gruppo di destinazione.

   **Nome del set di dati**: immettere un nome per il set di dati.

   **Nome della tabella**: Immettere un nome per la tabella.

   ![Aggiungere un output al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selezionare **Salva**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** selezionare **Query**.

2. Sostituire `[YourInputAlias]` con l'alias di input del processo.

3. Sostituire `[YourOutputAlias]` con l'alias di output del processo.

   ![Aggiungere una query al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selezionare **Salva**.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di Stream Analitica, selezionare **Overview**, quindi selezionare **avviare** > **ora** > **avviare**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

![Eseguire un processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Creare e pubblicare un report di Power BI per visualizzare i dati

1. Verificare che l'applicazione di esempio sia in esecuzione nel dispositivo. Se non lo fosse, è possibile fare riferimento alle esercitazioni descritte in [Configurare il dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Accedere all'account [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selezionare l'area di lavoro è stata usata, **area di lavoro personale**.

4. Selezionare **Set di dati**.

   Dovrebbero essere visualizzati i set di dati specificati quando è stato creato l'output per il processo di Analisi di flusso.

5. Per il set di dati è stato creato, selezionare **Aggiungi Report** (la prima icona a destra del nome del set di dati).

   ![Creare un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Creare un grafico a linee per visualizzare la temperatura in tempo reale nel tempo.

   1. Nel **visualizzazioni** riquadro della pagina della creazione di report, selezionare l'icona di grafico a linee per aggiungere un grafico a linee.

   2. Nel riquadro **Campi** espandere la tabella specificata durante la creazione di output per il processo di analisi di flusso.

   3. Trascinare **EventEnqueuedUtcTime** in **Asse** sul riquadro **Visualizzazioni**.

   4. Trascinare la **temperatura** in **Valori**.

      Viene creato un grafico a linee. L'asse x mostra data e ora per il fuso orario UTC. L'asse y mostra la temperatura dal sensore.

      ![Aggiungere un grafico a linee per la temperatura a un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Creare un altro grafico a linee in modo da visualizzare in tempo reale l'umidità nel tempo. A tale scopo, attenersi alla stessa procedura precedente e inserire **EventEnqueuedUtcTime** sull'asse x e l'**umidità** sull'asse y.

   ![Aggiungere un grafico a linee per l'umidità a un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selezionare **Salva** per salvare il report.

9. Selezionare **report** sul riquadro a sinistra e quindi selezionare il report appena creato.

10. Selezionare **File** > **pubblica sul web**.

11. Selezionare **crea codice di incorporamento**, quindi selezionare **Publish**.

Viene fornito il collegamento al report che è possibile condividere con altri utenti per l'accesso al report e un frammento di codice che è possibile utilizzare per integrare il report nel tuo blog o sito Web.

![Pubblicare un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft offre anche le [App per dispositivi mobili di Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) per la visualizzazione e l'interazione con i dashboard di Power BI e i report sul tuo dispositivo mobile.

## <a name="next-steps"></a>Passaggi successivi

Si è utilizzato correttamente Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure.

Per un altro modo per visualizzare i dati dall'IoT Hub di Azure, vedere [Usa un'app web per visualizzare i dati del sensore in tempo reale dall'IoT Hub di Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
