---
title: Testare un processo di Analisi di flusso di Azure in locale con dati di esempio usando il codice di Visual StudioTest an Azure Stream Analytics job locally with sample data using Visual Studio Code
description: Questo articolo descrive come testare le query in locale con dati di esempio usando gli strumenti di Analisi di flusso di Azure per il codice di Visual Studio.This article describes how to test queries locally with sample data using Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486469"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>TestStream Analytics esegue query localmente con dati di esempio usando il codice di Visual StudioTest Stream Analytics queries locally with sample data using Visual Studio Code

È possibile usare gli strumenti di Analisi di flusso di Azure per Visual Studio Code per testare i processi di Analisi di flusso in locale con dati di esempio. È possibile trovare i risultati della query nei file JSON nella cartella **LocalRunOutputs** del progetto.

## <a name="prerequisites"></a>Prerequisiti

* Installare [.NET core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare questa [guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di Analisi di flusso usando Visual Studio Code.Use this quickstart to learn how to create a Stream Analytics job using Visual Studio Code.

## <a name="prepare-sample-data"></a>Preparare i dati di esempio

È necessario preparare prima i file di dati di input di esempio. Se nel computer sono già presenti alcuni file di dati di esempio, è possibile ignorare questo passaggio e passare a quello successivo.

1. Fare clic su **Anteprima dati** nel file di configurazione di input dalla riga superiore. Alcuni dati di input verranno recuperati dall'hub IoT e visualizzati nella finestra di anteprima. Si noti che questa operazione potrebbe richiedere un po' di tempo.

2. Una volta visualizzati i dati, fare clic su **Salva con nome** per salvare i dati in un file locale.

 ![Visualizzare l'input live in anteprima](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definire un input localeDefine a local input

1. Fare clic su **input.json** nella cartella Inputs nel progetto di Analisi di flusso. Quindi selezionare **Aggiungi input locale** dalla riga superiore.

    ![Aggiungere input locale dal progettoAdd local input from project](./media/quick-create-vs-code/add-input-from-project.png)

    Per aprire la tavolozza dei comandi e immettere **ASA: Aggiungi input,** è anche possibile utilizzare **Ctrl , Maiusc e P** .

   ![Aggiungere input di Analisi di flusso in VS Code](./media/quick-create-vs-code/add-input.png)

2. Selezionare **Input locale**.

    ![Aggiungere l'input locale ASA nel codice di Visual StudioAdd ASA local input in Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Selezionare **: Nuovo input locale**.

    ![Aggiungere un nuovo input locale ASA nel codice di Visual StudioAdd a new ASA local input in Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Immettere lo stesso alias di input utilizzato nella query.

    ![Aggiungere un nuovo alias di input locale ASAAdd a new ASA local input alias](./media/vscode-local-run/new-local-input-alias.png)

5. Nel file **LocalInput_Input.json** appena generato immettere il percorso del file di dati locale.

    ![Immettere il percorso del file locale in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Selezionare **Anteprima dati** per visualizzare in anteprima i dati di input. Il tipo di serializzazione per i dati viene rilevato automaticamente se è JSON o CSV. Utilizzare il selettore per visualizzare i dati in formato **Tabella** o **Non elaborato.** La tabella seguente è un esempio di dati nel **formato Tabella:**

     ![Visualizzare l'anteprima dei dati locali in formato tabella](./media/vscode-local-run/local-file-preview-table.png)

    La tabella seguente è un esempio di dati in **formato Raw:**

    ![Visualizzare in anteprima i dati locali in formato non elaborato](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Eseguire query in locale

Tornare all'editor di query e selezionare **Esegui localmente**. Quindi selezionare **Usa input locale** dall'elenco a discesa.

![Selezionare Esegui localmente nell'editor di query](./media/vscode-local-run/run-locally.png)

![Usa input locale](./media/vscode-local-run/run-locally-use-local-input.png)

Il risultato viene visualizzato nella finestra di destra. È possibile fare clic su **Esegui** per eseguire di nuovo il test. È inoltre possibile selezionare **Apri nella cartella** per visualizzare i file dei risultati in Esplora file e aprirli ulteriormente con altri strumenti. Si noti che i file dei risultati sono disponibili solo in formato JSON.

![Visualizzare il risultato dell'esecuzione locale](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Passaggi successivi

* [Testare i processi di Analisi di flusso di Azure in locale con input dinamico usando il codice di Visual StudioTest Azure Stream Analytics jobs locally with live input using Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Esplorare i processi di Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics jobs with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
