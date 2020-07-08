---
title: Testare le query di analisi di flusso di Azure in locale rispetto all'input del flusso Live usando Visual Studio Code
description: Questo articolo descrive come testare le query localmente in base all'input del flusso Live usando gli strumenti di analisi di flusso di Azure per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: 1b2268426b66ddb9ffdd48d6901ab991e76cb95c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037444"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testare le query di analisi di flusso in locale rispetto all'input del flusso Live usando Visual Studio Code

È possibile usare gli strumenti di analisi di flusso di Azure per Visual Studio Code per testare i processi di analisi di flusso in locale rispetto all'input del flusso live. L'input può provenire da un'origine, ad esempio hub eventi di Azure o hub Azure. I risultati dell'output vengono inviati come file JSON a una cartella nel progetto denominato **LocalRunOutputs**.

## <a name="prerequisites"></a>Prerequisiti

* Installare il [.NET Core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare [questa Guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definire un input di flusso Live

1. Fare clic con il pulsante destro del mouse sulla cartella **Inputs** nel progetto di Analisi di flusso. Scegliere quindi **ASA: Add Input** (ASA: Aggiungi input) dal menu di scelta rapida.

   ![Aggiungi input dalla cartella Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   È anche possibile premere **CTRL + MAIUSC + P** per aprire il riquadro comandi e immettere **ASA: Aggiungi input**.

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

Per assicurarsi che i dati di input vengano ricevuti, selezionare **Anteprima dati** nel file di configurazione dell'input Live dalla riga superiore. Alcuni dati di input provengono da un hub Internet e vengono visualizzati nella finestra di anteprima. La visualizzazione dell'anteprima potrebbe richiedere alcuni secondi.

 ![Visualizzare l'input live in anteprima](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Eseguire query in locale

Tornare all'editor di query e selezionare **Esegui localmente**. Selezionare quindi **Usa input Live** dall'elenco a discesa.

![Selezionare "Esegui localmente" nell'editor di query](./media/vscode-local-run/run-locally.png)

![Selezionare "Usa input Live"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Il risultato viene visualizzato nella finestra a destra e aggiornato ogni 3 secondi. È possibile selezionare **Esegui per eseguire** nuovamente il test. È anche possibile selezionare **Apri in cartella** per visualizzare i file dei risultati in Esplora file e aprirli con Visual Studio Code o uno strumento come Excel. Si noti che i file dei risultati sono disponibili solo in formato JSON.

L'ora predefinita per l'avvio del processo di creazione dell'output è impostata su **Now**. È possibile personalizzare l'ora selezionando il pulsante **ora di inizio output** nella finestra risultati.

![Visualizza risultato esecuzione locale](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare i processi di analisi di flusso di Azure con Visual Studio Code (anteprima)](visual-studio-code-explore-jobs.md)

* [Configurare pipeline CI/CD con il pacchetto npm](setup-cicd-vs-code.md)
