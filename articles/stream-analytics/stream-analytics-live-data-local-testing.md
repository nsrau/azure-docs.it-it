---
title: Testare dati live con Analisi di flusso di Azure per Visual Studio
description: Informazioni su come testare il processo di Analisi di flusso di Azure in locale usando i dati in streaming live.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479783"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio (anteprima)

Gli strumenti di Analisi di flusso di Azure per Visual Studio consentono di testare i processi in locale dall'IDE usando flussi di eventi live da Hub eventi, Hub IoT e Archiviazione BLOB di Azure. Il test locale dei dati live non può sostituire il [test delle prestazioni e della scalabilità](stream-analytics-streaming-unit-consumption.md) che è possibile eseguire nel cloud, ma consente di risparmiare tempo durante il test funzionale perché non è necessario inviare i dati al cloud ogni volta che si vuole testare il processo di Analisi di flusso. Questa funzionalità è in anteprima e non dovrebbe essere usata per i carichi di lavoro di produzione.

## <a name="testing-options"></a>Opzioni per il test

Sono supportate le opzioni di test locale seguenti:

|**Input**  |**Output**  |**Tipo di processo**  |
|---------|---------|---------|
|Dati statici locali   |  Dati statici locali   |   Cloud/Edge |
|Dati di input live   |  Dati statici locali   |   Cloud |
|Dati di input live   |  Dati di output live   |   Cloud |

## <a name="local-testing-with-live-data"></a>Test locale con dati live

1. Dopo aver creato un [progetto cloud di Analisi di flusso di Azure in Visual Studio](stream-analytics-quick-create-vs.md), aprire **script.asaql**. Per impostazione predefinita, il test locale usa input locale e output locale.

   ![Test di Analisi di flusso di Azure in Visual Studio con input locale e output locale](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Per testare i dati live, scegliere **Use Cloud Input** (Usa input cloud) dalla casella discesa.

   ![Test di Analisi di flusso di Azure in Visual Studio con input cloud live](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Impostare l'**ora di inizio** per definire quando il processo inizierà a elaborare i dati di input. Per garantire l'accuratezza dei risultati, è possibile che il processo debba leggere i dati di input in anticipo. L'ora predefinita è impostata su 30 minuti prima dell'ora corrente.

   ![Test di Analisi di flusso di Azure in Visual Studio con ora di inizio dei dati live](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Fare clic su **Esegui localmente**. Verrà visualizzata una finestra della console con lo stato dell'esecuzione e le metriche del processo. È possibile arrestare il processo manualmente. 

   ![Test di Analisi di flusso di Azure in Visual Studio con finestra del processo dei dati live](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   I risultati di output vengono aggiornati ogni tre secondi con le prime 500 righe di output nella finestra dei risultati di esecuzione locale e i file di output vengono inseriti nella cartella **ASALocalRun** del percorso del progetto. È anche possibile aprire i file di output facendo clic sul pulsante **Apri cartella risultati** nella finestra dei risultati locale.

   ![Test di Analisi di flusso di Azure in Visual Studio con cartella dei risultati dei dati live aperta](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Per eseguire l'output dei risultati nei sink di output cloud, scegliere **Output to Cloud** (Output nel cloud) dalla seconda casella a discesa. Power BI e Azure Data Lake Storage non sono sink di output supportati.

   ![Test di Analisi di flusso di Azure in Visual Studio con output dei dati live nel cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Limitazioni

* Power BI e Azure Data Lake Storage non sono sink di output supportati a causa delle limitazioni del modello di autenticazione.

* Solo le opzioni di input cloud hanno il supporto dei [criteri temporali](stream-analytics-out-of-order-and-late-events.md), a differenza delle opzioni di input locale.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
* [Installare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Eseguire test locali delle query di Analisi di flusso con Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)