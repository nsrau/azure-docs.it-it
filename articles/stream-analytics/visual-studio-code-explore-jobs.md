---
title: Esplorare i processi di analisi di flusso di Azure in Visual Studio Code
description: Questo articolo illustra come esportare un processo di analisi di flusso di Azure in un progetto locale, elencare i processi e visualizzare le entità del processo.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4b4ddd7db4891c642ec2002bbe992314fe67a20f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891973"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Esplora analisi di flusso di Azure con Visual Studio Code (anteprima)

Analisi di flusso di Azure per Visual Studio Code estensione offre agli sviluppatori un'esperienza semplificata per la gestione dei processi di analisi di flusso. Può essere usato in Windows, Mac e Linux. Con l'estensione analisi di flusso di Azure è possibile:

- [Creare](quick-create-visual-studio-code.md), avviare e arrestare processi
- Esportare i processi esistenti in un progetto locale
- Elencare i processi e visualizzare le entità del processo

## <a name="export-a-job-to-a-local-project"></a>Esportare un processo in un progetto locale

Per esportare un processo in un progetto locale, individuare il processo che si vuole esportare in **Esplora analisi di flusso** in Visual Studio Code. Quindi selezionare una cartella per il progetto. Il progetto viene esportato nella cartella selezionata ed è possibile continuare a gestire il processo da Visual Studio Code. Per altre informazioni sull'uso di Visual Studio Code per gestire i processi di analisi di flusso, vedere la [Guida introduttiva](quick-create-visual-studio-code.md)di Visual Studio Code.

![Esporta processo ASA in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Elencare il processo e visualizzare le entità del processo

È possibile usare la visualizzazione del processo per interagire con i processi di Analisi di flusso di Azure da Visual Studio.


1. Fare clic sull'icona di **Azure** sulla barra delle attività Visual Studio Code, quindi espandere **nodo di analisi di flusso**. I processi verranno visualizzati sotto le sottoscrizioni.

   ![Apri Esplora analisi di flusso](./media/vscode-explore-jobs/open-explorer.png)

2. Espandere il nodo del processo. è possibile aprire e visualizzare la query del processo, la configurazione, gli input, gli output e le funzioni. 

3. Fare clic con il pulsante destro del mouse sul nodo del processo e scegliere la visualizzazione del processo nel nodo del **portale** per aprire la visualizzazione del processo nel portale di Azure.

   ![Aprire la visualizzazione processi nel portale](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo cloud di Analisi di flusso di Azure in Visual Studio Code (Anteprima)](quick-create-visual-studio-code.md)
