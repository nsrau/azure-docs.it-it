---
title: Esplorare i processi di Analisi di flusso di Azure nel codice di Visual StudioExplore Azure Stream Analytics jobs in Visual Studio Code
description: Questo articolo illustra come esportare un processo di Analisi di flusso di Azure in un progetto locale, elencare i processi e visualizzare le entità processo.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479241"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Esplora Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics with Visual Studio Code (Preview)

L'estensione di Azure Stream Analytics per il codice di Visual Studio offre agli sviluppatori un'esperienza leggera per la gestione dei processi di Analisi di flusso. Può essere utilizzato su Windows, Mac e Linux. Con l'estensione Analisi di flusso di Azure è possibile:With the Azure Stream Analytics extension, you can:

- [Creare,](quick-create-vs-code.md)avviare e arrestare i processi
- Esportare i processi esistenti in un progetto locale
- Elencare i processi e visualizzare le entità processo

## <a name="export-a-job-to-a-local-project"></a>Esportare un processo in un progetto locale

Per esportare un processo in un progetto locale, individuare il processo che si desidera esportare in Esplora analisi di flusso nel codice di Visual Studio.To export a job to a local project, locate the job you wish to export in the **Stream Analytics Explorer** in Visual Studio Code. Quindi selezionare una cartella per il progetto. Il progetto viene esportato nella cartella selezionata ed è possibile continuare a gestire il processo da Visual Studio Code. Per ulteriori informazioni sull'utilizzo del codice di Visual Studio per gestire i processi di Analisi di flusso, vedere la [guida introduttiva](quick-create-vs-code.md)al codice di Visual Studio .

![Esportare il processo ASA nel codice di Visual StudioExport ASA job in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Elencare il processo e visualizzare le entità processo

È possibile usare la visualizzazione del processo per interagire con i processi di Analisi di flusso di Azure da Visual Studio.


1. Fare clic sull'icona di **Azure** nella barra delle attività del codice di Visual Studio, quindi espandere **il nodo Analisi flusso**. I processi devono essere visualizzati sotto le sottoscrizioni.

   ![Aprire Esplora analisi di flusso](./media/vscode-explore-jobs/open-explorer.png)

2. Espandere il nodo del processo, è possibile aprire e visualizzare la query del processo, la configurazione, gli input, gli output e le funzioni. 

3. Fare clic con il pulsante destro del mouse sul nodo del processo e scegliere Apri visualizzazione processo nel nodo Portale per aprire la visualizzazione del processo nel portale di Azure.Right click your job node, and choose the **Open Job View in Portal** node to open the job view in the Azure portal.

   ![Aprire la visualizzazione processi nel portale](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo cloud di Analisi di flusso di Azure in Visual Studio Code (Anteprima)](quick-create-vs-code.md)
