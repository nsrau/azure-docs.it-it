---
title: Esplorare Azure Stream Analitica con Visual Studio Code (anteprima)
description: Questo articolo illustra come esportare un processo di Analitica Stream di Azure in un progetto locale, elencare i processi ed entità job view e configurare una pipeline di integrazione continua/recapito Continuo per il processo di Stream Analitica.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079211"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Esplorare Azure Stream Analitica con Visual Studio Code (anteprima)

L'Analitica Stream di Azure per l'estensione di Visual Studio Code offre agli sviluppatori un'esperienza di caricamento leggera per la gestione delle mansioni che esercitano Stream Analitica. Con l'estensione Azure Stream Analitica, è possibile:

- [Creare](quick-create-vs-code.md), avviare e arrestare i processi
- Esportare i processi esistenti in un progetto locale
- Eseguire query in locale
- Configurare una pipeline di integrazione continua/recapito Continuo

## <a name="export-a-job-to-a-local-project"></a>Esportare un processo in un progetto locale

Per esportare un processo in un progetto locale, individuare il processo di cui si desidera esportare nei **Stream Analitica Esplora** in Visual Studio code. Selezionare quindi una cartella per il progetto. Il progetto viene esportato per la cartella selezionata ed è possibile continuare a gestire il processo da Visual Studio Code. Per altre informazioni sull'uso di Visual Studio Code per gestire i processi di Analitica di Stream, vedere Visual Studio Code [Guida introduttiva](quick-create-vs-code.md).

![Esporta processo ASA in Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Eseguire query in locale

È possibile usare l'estensione Azure Stream Analitica per Visual Studio Code per testare i processi di Analitica di Stream in locale con i dati di esempio.

1. Dopo aver creato il processo di Stream Analitica, usare **Ctrl + MAIUSC + P** per aprire il gruppo di comando. Quindi digitare e selezionare **ASA: Aggiungi Input**.

    ![Aggiungi Input di ASA in Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Selezionare **Input locale**.

    ![Aggiungi input locale ASA in Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Selezionare **+ nuovo Input locale**.

    ![Aggiungere un ASA nuovo locale di input in Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Immettere lo stesso alias di input che è stato utilizzato nella query.

    ![Aggiungere un nuovo alias di input locale ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. Nel **LocalInput_DefaultLocalStream.json** file, immettere il percorso del file in cui si trova il file di dati locale.

    ![Immettere il percorso file locale in Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Tornare all'editor di query e selezionare **vengono eseguite localmente**.

    ![Selezionare Esegui in locale nell'editor di query](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Configurare una pipeline di integrazione continua/recapito Continuo

È possibile abilitare l'integrazione continua e distribuzione per i processi di Analitica Stream di Azure con il **asa continue strumenti** pacchetto NPM. Il pacchetto NPM fornisce gli strumenti per la distribuzione automatica dei progetti di Stream Analitica Visual Studio Code. Può essere utilizzato in Windows, macOS e Linux senza installare Visual Studio Code.

Dopo aver [scaricato il pacchetto](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), usare il comando seguente per restituire i modelli di Azure Resource Manager. Se il **outputPath** non viene specificato, i modelli di disponibilità verranno inseriti nel **Distribuisci** cartella del progetto **bin** cartella.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di cloud di Azure Stream Analitica in Visual Studio Code (anteprima)](quick-create-vs-code.md)