---
title: "Creare un'attività watcher nell'account di Automazione di Azure | Microsoft Docs"
description: "Informazioni su come creare un'attività watcher nell'account di Automazione di Azure per controllare i nuovi file creati in una cartella."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Le attività watcher di Automazione di Azure consentono di rispondere a eventi che si verificano nel data center locale

In questa esercitazione si imparerà a creare una nuova attività watcher per:

> [!div class="checklist"]
> * Creare un runbook watcher che verifica la presenza di nuovi file in una directory.
> * Creare una variabile di automazione per conservare l'ora dell'ultima elaborazione di un file da parte del watcher.
> * Creare un runbook azione che viene chiamato quando il runbook watcher rileva un nuovo file.
> * Creare un'attività watcher che seleziona il runbook watcher e il runbook azione.
> * Attivare un watcher mediante l'aggiunta di un nuovo file in una directory.
> * Esaminare l'output dal runbook azione che mostra le informazioni sul nuovo file.  

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:
+ Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
+ Un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) dove viene eseguita l'attività watcher.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Creare un runbook watcher che verifica la presenza di nuovi file
1.  Aprire l'account di automazione e fare clic sulla pagina Runbook.
2.  Fare clic sul pulsante "Sfoglia raccolta".
![Elenco di runbook dall'interfaccia utente](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Cercare "Watch-NewFile" e importare il runbook nell'account di automazione.
![Pubblicare il runbook dall'interfaccia utente](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Fare clic su "Modifica" per visualizzare l'origine del runbook e fare clic sul pulsante "Pubblica".

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Creare una variabile di automazione per conservare l'ora dell'ultima elaborazione di un file da parte del watcher.
1.  In Risorse condivise aprire la pagina delle variabili e fare clic su "Aggiungi variabile" ![Elenco di variabili dall'interfaccia utente](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Immettere "Watch-NewFileTimestamp" nel campo Nome
3.  Nel campo Tipo selezionare DataOra e quindi fare clic sul pulsante "Crea".
![Creare una variabile di esempio dall'interfaccia utente](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Creare un runbook azione che viene chiamato quando il runbook watcher rileva un nuovo file
1.  Fare clic sulla pagina Runbook sotto la categoria "Automazione processi".
2.  Fare clic sul pulsante "Sfoglia raccolta".
3.  Cercare "Process-NewFile" e importare il runbook nell'account di automazione.
4.  Fare clic su "Modifica" per visualizzare l'origine del runbook e fare clic sul pulsante "Pubblica".
![Elaborare il watcher dall'interfaccia utente](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Creare un'attività watcher che seleziona il runbook watcher e il runbook azione.
1.  Aprire la pagina Attività watcher e fare clic sul pulsante "Aggiungi un'attività watcher".
![Elenco di watcher dall'interfaccia utente](media/automation-watchers-tutorial/WatchersList.png)
2.  Immettere "WatchMyFolder" come nome.
3.  Selezionare "Configura watcher" e selezionare il runbook "Watch-NewFile".
![Configurare il watcher dall'interfaccia utente](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Per i parametri inserire i valori seguenti:
    *   FOLDERPATH. Una cartella nel ruolo di lavoro ibrido in cui verranno creati nuovi file.
    *   EXTENSION. Lasciare vuoto per elaborare tutte le estensioni di file.
    *   RECURSE. Lasciare il valore predefinito.
    *   Impostazioni esecuzione. Selezionare il ruolo di lavoro ibrido.
5.  Fare clic su OK e quindi su Seleziona per tornare alla pagina del watcher.
6.  Selezionare "Configura azione" e quindi selezionare il runbook "Process-NewFile".
![Configurare l'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Per i parametri inserire i valori seguenti:
    *   EVENTDATA. Lasciare vuoto. I dati vengono passati dal runbook watcher.
    *   Impostazioni esecuzione. Lasciare questo campo impostato su Azure in quanto questo runbook viene eseguito nel servizio Automazione.
8.  Fare clic su OK e quindi su Seleziona per tornare alla pagina del watcher.
9.  Fare clic su OK per creare l'attività watcher.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Attivare un watcher mediante l'aggiunta di un nuovo file in una directory
1.  Passare al ruolo di lavoro ibrido.
2.  Aggiungere un nuovo file di testo nella cartella monitorata dall'attività watcher.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Esaminare l'output dal runbook azione che mostra le informazioni sul nuovo file
1.  Fare clic sull'attività watcher relativa a "WatchMyFolder".
2.  Fare clic su "Visualizza flussi watcher" per verificare se il watcher ha rilevato il nuovo file e avviato il runbook azione.
3.  Fare clic su "Visualizza processi dell'azione watcher" per visualizzare il processo del runbook azione.
![Processi dell'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Passaggi successivi:

Per altre informazioni, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).








