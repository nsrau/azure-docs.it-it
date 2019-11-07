---
title: Copiare o eseguire il backup dei processi di analisi di flusso di Azure
description: Questo articolo descrive come copiare o eseguire il backup di un processo di analisi di flusso di Azure.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 8ae97a3ef6e354bb07e257b4997341297e8abe51
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588135"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copiare o eseguire il backup dei processi di analisi di flusso di Azure

È possibile copiare o eseguire il backup dei processi di analisi di flusso di Azure distribuiti usando Visual Studio Code o Visual Studio. 

## <a name="before-you-begin"></a>Prima di iniziare
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Accedere al [portale di Azure](https://portal.azure.com/).

* Installare l' [estensione analisi di flusso di Azure per Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension) o [gli strumenti di analisi di flusso di Azure per Visual Studio](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension).  



## <a name="visual-studio-code"></a>Visual Studio Code 

1. Fare clic sull'icona di **Azure** sulla barra delle attività di Visual Studio Code, quindi espandere nodo di **analisi di flusso** . I processi verranno visualizzati sotto le sottoscrizioni.

   ![Apri Esplora analisi di flusso](./media/vscode-explore-jobs/open-explorer.png)

2. Per esportare un processo in un progetto locale, individuare il processo che si vuole esportare in **Esplora analisi di flusso** in Visual Studio Code. Quindi selezionare una cartella per il progetto. 

    ![Esporta processo ASA in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Il progetto viene esportato nella cartella selezionata e aggiunta all'area di lavoro corrente.

    ![Esporta processo ASA in Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Per pubblicare il processo in un'altra area o backup usando un altro nome, selezionare **Seleziona dalle sottoscrizioni da pubblicare** nell'editor di query (\*. asaql) e seguire le istruzioni. 

    ![Pubblicare in Azure in Visual Studio Code](./media/quick-create-vs-code/select-subscription.png)


## <a name="visual-studio"></a>Visual Studio 

1. Seguire le [istruzioni per esportare un processo di analisi di flusso di Azure distribuito in un progetto](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project). 

2. Aprire il file \*. asaql nell'editor di query, selezionare **Invia ad Azure** nell'editor di script e seguire le istruzioni per pubblicare il processo in un'altra area o in un altro backup usando un nuovo nome. 


## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un processo di analisi di flusso usando Visual Studio Code](quick-create-vs-code.md)
* [Guida introduttiva: creare un processo di analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Distribuire un processo di Analisi di flusso di Azure con CI/CD usando Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)