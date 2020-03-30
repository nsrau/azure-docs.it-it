---
title: Testare le query di Analisi di flusso di Azure in locale sull'input del flusso live usando il codice di Visual StudioTest Azure Stream Analytics queries locally against live stream input by using Visual Studio Code
description: Questo articolo descrive come testare le query in locale sull'input del flusso live usando gli strumenti di Analisi di flusso di Azure per il codice di Visual Studio.This article describes how to test queries locally against live stream input by using Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660852"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testare le query di Analisi di flusso in locale sull'input del flusso live usando il codice di Visual StudioTest Stream Analytics queries locally against live stream input by using Visual Studio Code

È possibile usare gli strumenti di Analisi di flusso di Azure per il codice di Visual Studio per testare i processi di Analisi di flusso in locale rispetto all'input del flusso live. L'input può provenire da un'origine come Hub eventi di Azure o Hub IoT di Azure.The input can come from a source like Azure Event Hubs or Azure IoT Hub. I risultati dell'output vengono inviati come file JSON a una cartella del progetto denominata **LocalRunOutputs**.

## <a name="prerequisites"></a>Prerequisiti

* Installare [.NET Core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare [questa guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di Analisi di flusso usando il codice di Visual Studio.Use this quickstart to learn how to create a Stream Analytics job by using Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definire un input di flusso liveDefine a live stream input

1. Fare clic con il pulsante destro del mouse sulla cartella **Inputs** nel progetto di Analisi di flusso. Quindi selezionare **ASA: Aggiungi input** dal menu di scelta rapida.

   ![Aggiungi input dalla cartella Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Per aprire la tavolozza dei comandi e immettere **ASA: Aggiungi input,** è anche possibile selezionare **Ctrl .**

   ![Aggiunta di input di Analisi di flusso in Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Scegliere un tipo di origine di input dall'elenco a discesa.

   ![Selezionare Hub IoT come opzione di input](./media/quick-create-vs-code/iot-hub.png)

3. Se l'input è stato aggiunto dal riquadro comandi, selezionare lo script di query di Analisi di flusso che userà l'input. Verrà automaticamente popolato con il percorso del file **myASAproj.asaql**.

   ![Selezionare uno script di Analisi di flusso in Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Scegliere **Select from your Azure Subscriptions** (Seleziona dalle sottoscrizioni di Azure) dal menu a discesa.

    ![Selezionare dalle sottoscrizioni](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configurare il file JSON appena generato. È possibile usare la funzionalità CodeLens per immettere una stringa, selezionare una voce da un elenco a discesa o modificare il testo direttamente nel file. Lo screenshot seguente mostra **Select from your Subscriptions** (Seleziona dalle sottoscrizioni) come esempio.

   ![Configurare l'input in Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Visualizzare l'input in anteprima

Per assicurarsi che i dati di input siano in arrivo, selezionare **Anteprima dati** nel file di configurazione di input live dalla riga superiore. Alcuni dati di input provengono da un hub IoT e vengono visualizzati nella finestra di anteprima. La visualizzazione dell'anteprima potrebbe richiedere alcuni secondi.

 ![Visualizzare l'input live in anteprima](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Eseguire query in locale

Tornare all'editor di query e selezionare **Esegui localmente**. Quindi selezionare **Usa Live Input** dall'elenco a discesa.

![Selezionare "Esegui localmente" nell'editor di query](./media/vscode-local-run/run-locally.png)

![Selezionare "Usa Live Input"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Il risultato viene visualizzato nella finestra di destra e aggiornato ogni 3 secondi. È possibile selezionare **Esegui** per eseguire di nuovo il test. È anche possibile selezionare Apri nella cartella per visualizzare i file dei risultati in Esplora file e aprirli con Visual Studio Code o uno strumento come Excel.You can also select **Open in folder** to see the result files in File Explorer and open them with Visual Studio Code or a tool like Excel. Si noti che i file dei risultati sono disponibili solo in formato JSON.

Il tempo predefinito per l'avvio della creazione dell'output da parte del processo è impostato su **Ora**. È possibile personalizzare l'ora selezionando il pulsante **Ora di inizio output** nella finestra dei risultati.

![Visualizzare il risultato dell'esecuzione locale](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare i processi di Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics jobs with Visual Studio Code (preview)](visual-studio-code-explore-jobs.md)

* [Configurare pipeline CI/CD con il pacchetto npm](setup-cicd-vs-code.md)
