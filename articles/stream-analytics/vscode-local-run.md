---
title: Testare le query di analisi di flusso di Azure con Visual Studio Code
description: Questo articolo descrive come testare le query in locale con gli strumenti di analisi di flusso di Azure per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924979"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testare le query di analisi di flusso localmente con Visual Studio Code

È possibile usare gli strumenti di analisi di flusso di Azure per Visual Studio Code per testare i processi di analisi di flusso localmente con dati di esempio.

Usare questa [Guida introduttiva](quick-create-vs-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code.

## <a name="prerequisites"></a>Prerequisiti
* Installare [.NET Core SDK](https://dotnet.microsoft.com/download).
* Riavviare Visual Studio Code.
 
## <a name="run-queries-locally"></a>Eseguire query localmente

È possibile usare l'estensione analisi di flusso di Azure per Visual Studio Code testare i processi di analisi di flusso localmente con dati di esempio.

1. Dopo aver creato il processo di analisi di flusso, premere **CTRL + MAIUSC + P** per aprire il riquadro comandi. Quindi digitare e selezionare **ASA: Add input**.

    ![Aggiungere input ASA in Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Selezionare **input locale**.

    ![Aggiungere input locale ASA in Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Selezionare **+ nuovo input locale**.

    ![Aggiungere un nuovo input locale ASA in Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Immettere lo stesso alias di input usato nella query.

    ![Aggiungere un nuovo alias di input locale ASA](./media/vscode-local-run/new-local-input-alias.png)

5. Nel file **LocalInput_DefaultLocalStream. JSON** immettere il percorso del file in cui si trova il file di dati locale.

    ![Immettere il percorso del file locale in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Tornare all'editor di query e selezionare **Esegui localmente**.

    ![Selezionare Esegui localmente nell'editor di query](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo cloud di analisi di flusso di Azure in Visual Studio Code (anteprima)](quick-create-vs-code.md)

* [Esplorare i processi di analisi di flusso di Azure con Visual Studio Code (anteprima)](vscode-explore-jobs.md)
