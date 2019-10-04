---
title: Esplorare i processi di Analitica Stream di Azure con Visual Studio Code (anteprima)
description: Questo articolo illustra come esportare un processo di Analitica Stream di Azure in un progetto locale, elencare i processi ed entità job view.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827801"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Esplorare Azure Stream Analitica con Visual Studio Code (anteprima)

L'Analitica Stream di Azure per l'estensione di Visual Studio Code offre agli sviluppatori un'esperienza di caricamento leggera per la gestione delle mansioni che esercitano Stream Analitica. Può essere utilizzato in Windows, Mac e Linux. Con l'estensione Azure Stream Analitica, è possibile:

- [Creare](quick-create-vs-code.md), avviare e arrestare i processi
- Esportare i processi esistenti in un progetto locale
- Elencare i processi e visualizzare le entità di processo

## <a name="export-a-job-to-a-local-project"></a>Esportare un processo in un progetto locale

Per esportare un processo in un progetto locale, individuare il processo di cui si desidera esportare nei **Stream Analitica Esplora** in Visual Studio Code. Selezionare quindi una cartella per il progetto. Il progetto viene esportato per la cartella selezionata ed è possibile continuare a gestire il processo da Visual Studio Code. Per altre informazioni sull'uso di Visual Studio Code per gestire i processi di Analitica di Stream, vedere Visual Studio Code [Guida introduttiva](quick-create-vs-code.md).

![Esporta processo ASA in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Elencare i processi e visualizzare le entità del processo

È possibile usare la visualizzazione del processo per interagire con i processi di Analisi di flusso di Azure da Visual Studio.


1. Fare clic sui **Azure** icona sulla barra di attività di Visual Studio Code e quindi espandere **nodo Stream Analitica**. I processi dovrebbe essere visualizzato sotto le proprie sottoscrizioni.

   ![Esplora Analitica Stream aperto](./media/vscode-explore-jobs/open-explorer.png)

2. Espandere il nodo del processo, è possibile aprire e visualizzare la query del processo, configurazione, input, output e funzioni. 

3. Fare clic con il pulsante destro del nodo del processo e scegliere il **aprire visualizzazione del processo nel portale** il nodo per visualizzare la visualizzazione del processo nel portale di Azure.

   ![Visualizzazione del processo Apri nel portale](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di cloud di Azure Stream Analitica in Visual Studio Code (anteprima)](quick-create-vs-code.md)
