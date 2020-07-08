---
title: Testare un processo di analisi di flusso di Azure localmente con dati di esempio usando Visual Studio Code
description: Questo articolo descrive come testare le query localmente con dati di esempio usando gli strumenti di analisi di flusso di Azure per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: a47b641f4f5049beb605490122a33d407ac3e222
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044363"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Eseguire test locali delle query di Analisi di flusso con dati di esempio con Visual Studio Code

È possibile usare gli strumenti di analisi di flusso di Azure per Visual Studio Code per testare i processi di analisi di flusso localmente con dati di esempio. È possibile trovare i risultati della query in file JSON nella cartella **LocalRunOutputs** del progetto.

## <a name="prerequisites"></a>Prerequisiti

* Installare [.NET Core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare questa [Guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code.

## <a name="prepare-sample-data"></a>Preparare i dati di esempio

Prima di tutto è necessario preparare i file di dati di input di esempio. Se si dispone già di alcuni file di dati di esempio nel computer, è possibile ignorare questo passaggio e passare a quello successivo.

1. Fare clic su **Anteprima dati** nel file di configurazione di input dalla riga superiore. Alcuni dati di input verranno recuperati dall'hub Internet e visualizzati nella finestra di anteprima. Si noti che l'operazione potrebbe richiedere alcuni minuti.

2. Una volta visualizzati i dati, fare clic su **Salva con nome** per salvare i dati in un file locale.

 ![Visualizzare l'input live in anteprima](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definire un input locale

1. Fare clic **suinput.js** nella cartella inputs nel progetto di analisi di flusso. Quindi selezionare **Aggiungi input locale** dalla riga superiore.

    ![Aggiungi input locale dal progetto](./media/quick-create-vs-code/add-input-from-project.png)

    È anche possibile usare **CTRL + MAIUSC + P** per aprire il riquadro comandi e immettere **ASA: Aggiungi input**.

   ![Aggiungere input di Analisi di flusso in VS Code](./media/quick-create-vs-code/add-input.png)

2. Selezionare **input locale**.

    ![Aggiungere input locale ASA in Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Selezionare **+ nuovo input locale**.

    ![Aggiungere un nuovo input locale ASA in Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Immettere lo stesso alias di input usato nella query.

    ![Aggiungere un nuovo alias di input locale ASA](./media/vscode-local-run/new-local-input-alias.png)

5. NelLocalInput_Input.jsappena generato **su** file immettere il percorso del file in cui si trova il file di dati locale.

    ![Immettere il percorso del file locale in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Selezionare **Anteprima dati** per visualizzare in anteprima i dati di input. Il tipo di serializzazione per i dati viene rilevato automaticamente se il relativo JSON o CSV. Usare il selettore per visualizzare i dati in formato **tabella** o non **elaborato** . La tabella seguente è un esempio di dati nel **formato tabella**:

     ![Anteprima dei dati locali in formato tabella](./media/vscode-local-run/local-file-preview-table.png)

    La tabella seguente è un esempio di dati in **formato non elaborato**:

    ![Anteprima dati locali in formato non elaborato](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Eseguire query in locale

Tornare all'editor di query e selezionare **Esegui localmente**. Selezionare quindi **Usa input locale** nell'elenco a discesa.

![Selezionare Esegui localmente nell'editor di query](./media/vscode-local-run/run-locally.png)

![Usa input locale](./media/vscode-local-run/run-locally-use-local-input.png)

Il risultato viene visualizzato nella finestra a destra. È possibile fare clic su **Esegui per eseguire** nuovamente il test. È anche possibile selezionare **Apri in cartella** per visualizzare i file dei risultati in Esplora file e aprirli di nuovo con altri strumenti. Si noti che i file dei risultati sono disponibili solo in formato JSON.

![Visualizza risultato esecuzione locale](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Passaggi successivi

* [Testare i processi di analisi di flusso di Azure localmente con input Live con Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Esplorare i processi di analisi di flusso di Azure con Visual Studio Code (anteprima)](visual-studio-code-explore-jobs.md)
