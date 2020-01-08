---
title: Testare le query di analisi di flusso di Azure in locale rispetto all'input del flusso Live usando Visual Studio Code
description: Questo articolo descrive come testare le query localmente in base all'input del flusso Live usando gli strumenti di analisi di flusso di Azure per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660852"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testare le query di analisi di flusso in locale rispetto all'input del flusso Live usando Visual Studio Code

È possibile usare gli strumenti di analisi di flusso di Azure per Visual Studio Code per testare i processi di analisi di flusso in locale rispetto all'input del flusso live. L'input può provenire da un'origine, ad esempio hub eventi di Azure o hub Azure. I risultati dell'output vengono inviati come file JSON a una cartella nel progetto denominato **LocalRunOutputs**.

## <a name="prerequisites"></a>Prerequisiti

* Installare il [.NET Core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare [questa Guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definire un input di flusso Live

1. Fare clic con il pulsante destro del mouse sulla cartella **inputs** nel progetto di analisi di flusso. Quindi selezionare **ASA: Aggiungi input** dal menu di scelta rapida.

   ![Aggiungere input dalla cartella inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   È anche possibile premere **CTRL + MAIUSC + P** per aprire il riquadro comandi e immettere **ASA: Aggiungi input**.

   ![Aggiungere input di analisi di flusso in Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Scegliere un tipo di origine di input dall'elenco a discesa.

   ![Selezionare l'hub Internet come opzione di input](./media/quick-create-vs-code/iot-hub.png)

3. Se l'input è stato aggiunto dal riquadro comandi, scegliere lo script di query di analisi di flusso che userà l'input. Deve essere popolato automaticamente con il percorso del file **myASAproj. asaql**.

   ![Selezionare uno script di analisi di flusso in Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Scegliere **Seleziona dalle sottoscrizioni di Azure** dal menu a discesa.

    ![Selezionare le sottoscrizioni](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configurare il file JSON appena generato. È possibile usare la funzionalità CodeLens per immettere una stringa, selezionare da un elenco a discesa o modificare il testo direttamente nel file. La schermata seguente mostra come esempio la **selezione delle sottoscrizioni** .

   ![Configurare l'input in Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Anteprima input

Per assicurarsi che i dati di input vengano ricevuti, selezionare **Anteprima dati** nel file di configurazione dell'input Live dalla riga superiore. Alcuni dati di input provengono da un hub Internet e vengono visualizzati nella finestra di anteprima. La visualizzazione dell'anteprima potrebbe richiedere alcuni secondi.

 ![Anteprima input Live](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Eseguire query localmente

Tornare all'editor di query e selezionare **Esegui localmente**. Selezionare quindi **Usa input Live** dall'elenco a discesa.

![Selezionare "Esegui localmente" nell'editor di query](./media/vscode-local-run/run-locally.png)

![Selezionare "Usa input Live"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Il risultato viene visualizzato nella finestra a destra e aggiornato ogni 3 secondi. È possibile selezionare **Esegui per eseguire** nuovamente il test. È anche possibile selezionare **Apri in cartella** per visualizzare i file dei risultati in Esplora file e aprirli con Visual Studio Code o uno strumento come Excel. Si noti che i file dei risultati sono disponibili solo in formato JSON.

L'ora predefinita per l'avvio del processo di creazione dell'output è impostata su **Now**. È possibile personalizzare l'ora selezionando il pulsante **ora di inizio output** nella finestra risultati.

![Visualizza risultato esecuzione locale](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare i processi di analisi di flusso di Azure con Visual Studio Code (anteprima)](visual-studio-code-explore-jobs.md)

* [Configurare pipeline CI/CD usando il pacchetto NPM](setup-cicd-vs-code.md)
