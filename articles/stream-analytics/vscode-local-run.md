---
title: Testare le query Analitica Stream di Azure in locale con Visual Studio Code (anteprima)
description: Questo articolo descrive come testare le query in locale con Azure Stream Analitica Tools per Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827951"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testare le query Analitica di Stream in locale con Visual Studio Code

È possibile usare gli strumenti di Azure Stream Analitica per Visual Studio Code per testare i processi di Analitica di Stream in locale con i dati di esempio.

Usare questa [Guida introduttiva](quick-create-vs-code.md) per imparare a creare un processo di Stream Analitica usando Visual Studio Code.

## <a name="run-queries-locally"></a>Eseguire query in locale

È possibile usare l'estensione Azure Stream Analitica per Visual Studio Code per testare i processi di Analitica di Stream in locale con i dati di esempio.

1. Dopo aver creato il processo di Stream Analitica, usare **Ctrl + MAIUSC + P** per aprire il riquadro comandi. Quindi digitare e selezionare **ASA: Add Input** (ASA: Aggiungi input).

    ![Aggiungi Input di ASA in Visual Studio code](./media/vscode-local-run/add-input.png)

2. Selezionare **Input locale**.

    ![Aggiungi input locale ASA in Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Selezionare **+ nuovo Input locale**.

    ![Aggiungere un ASA nuovo locale di input in Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Immettere lo stesso alias di input che è stato utilizzato nella query.

    ![Aggiungere un nuovo alias di input locale ASA](./media/vscode-local-run/new-local-input-alias.png)

5. Nel **LocalInput_DefaultLocalStream.json** file, immettere il percorso del file in cui si trova il file di dati locale.

    ![Immettere il percorso file locale in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Tornare all'editor di query e selezionare **vengono eseguite localmente**.

    ![Selezionare Esegui in locale nell'editor di query](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di cloud di Azure Stream Analitica in Visual Studio Code (anteprima)](quick-create-vs-code.md)

* [Esplorare i processi di Analitica Stream di Azure con Visual Studio Code (anteprima)](vscode-explore-jobs.md)
