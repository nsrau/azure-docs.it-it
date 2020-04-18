---
title: Creare un'attività watcher nell'account di Automazione di Azure
description: Informazioni su come creare un'attività watcher nell'account di Automazione di Azure per controllare i nuovi file creati in una cartella.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617353"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Creare un'attività watcher di Automazione di Azure per tenere traccia delle modifiche dei file in un computer locale

Automazione di Azure usa un'attività Watcher per cercare eventi e attivare azioni con i runbook di PowerShell.Azure Automation uses a watcher task to look for events and trigger actions with PowerShell runbooks. L'attività Watcher contiene due parti: il watcher e l'azione. Un runbook Watcher viene eseguito a un intervallo definito nell'attività Watcher e restituisce i dati a un runbook di azioni. 

In questa esercitazione viene descritto come creare un'attività watcher per monitorare quando viene aggiunto un nuovo file in una directory. Si apprenderà come:

> [!div class="checklist"]
> * Importare un runbook watcher
> * Creare una variabile di automazione
> * Creare un runbook azione
> * Creare un'attività watcher
> * Attivare un watcher
> * Esaminare i risultati

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario quanto segue:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) dove viene eseguita l'attività watcher.
* Runbook di PowerShell.PowerShell runbooks. I runbook del flusso di lavoro di PowerShell non sono supportati dalle attività Watcher.PowerShell Workflow runbooks aren't supported by watcher tasks.

> [!NOTE]
> Watcher tasks are not supported in Azure China.

## <a name="import-a-watcher-runbook"></a>Importare un runbook watcher

Questa esercitazione usa un runbook watcher denominato **Watch-NewFile** per cercare nuovi file in una directory. Il runbook watcher recupera la data e l'ora dell'ultima scrittura nota nei file di una cartella e cerca qualsiasi file che risulti più recente di quel limite.

Questo processo di importazione può essere eseguito tramite [PowerShell Gallery](https://www.powershellgallery.com).

1. Passare alla pagina della raccolta per [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Nella scheda **Automazione di Azure** fare clic su **Distribuisci in Automazione di Azure**.

È anche possibile importare questo runbook nell'account di automazione dal portale attenendosi alla procedura seguente.

1. Aprire l'account di automazione e fare clic sulla pagina Runbook.
2. Fare clic su **Sfoglia raccolta**.
3. Cercare **Watcher runbook**, selezionare **Watcher runbook che cerca nuovi file in una directory**e fare clic su **Importa**.
  ![Importare un runbook di automazione dall'interfaccia utente](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Assegna un nome e una descrizione al runbook e fai clic **su OK** per importare il runbook nel tuo account Automation.
5. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Quando richiesto, fare clic su **Sì** per pubblicare il runbook.

## <a name="create-an-automation-variable"></a>Creare una variabile di automazione

Una [variabile di automazione](automation-variables.md) viene usata per archiviare i timestamp che il runbook precedente legge e archivia da ogni file.

1. Selezionare **Variabili** in **Risorse condivise** e fare clic su **Aggiungi una variabile**.
1. Immettere Watch-NewFileTimestamp come nome.
1. Selezionare DateTime come tipo.
1. Fare clic su **Crea** per creare la variabile di automazione.

## <a name="create-an-action-runbook"></a>Creare un runbook azione

Un runbook azione viene usato in un'attività watcher per agire sui dati passati da un runbook watcher. È necessario importare un runbook di azioni predefinito denominato **Process-NewFile** da [PowerShell Gallery](https://www.powershellgallery.com). 

Per creare un runbook delle azioni:

1. Passare alla pagina della raccolta per [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Nella scheda **Automazione di Azure** fare clic su **Distribuisci in Automazione di Azure**.

È anche possibile importare questo runbook nell'account di automazione dal portale di Azure:You can also import this runbook into your Automation account from the Azure portal:

1. Passare all'account di automazione e selezionare **Runbook** in **Process Automation**.
1. Fare clic su **Sfoglia raccolta**.
1. Cercare **l'azione Watcher**, selezionare **l'azione Watcher che elabora gli eventi attivati da un runbook Watcher**e fare clic su **Importa**.
  ![Importare un runbook azione dall'interfaccia utente](media/automation-watchers-tutorial/importsourceaction.png)
1. Assegna un nome e una descrizione al runbook e fai clic **su OK** per importare il runbook nel tuo account Automation.
1. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Quando richiesto, fare clic su **Sì** per pubblicare il runbook.

## <a name="create-a-watcher-task"></a>Creare un'attività watcher

In questo passaggio viene configurata l'attività Watcher che fa riferimento ai runbook Watcher e Action definiti nelle sezioni precedenti.

1. Passare all'account Di automazione e selezionare **Attività Watcher** in **Automazione processo**.
1. Selezionare la pagina Attività Watcher e fare clic su **Aggiungi un'attività Watcher**.
1. Immettere **WatchMyFolder** come nome.

1. Selezionare Configura watcher e scegliere il runbook **Watch-NewFile.Select** **Configure watcher** and choose the Watch-NewFile runbook.

1. Per i parametri inserire i valori seguenti:

   * **FOLDERPATH** - Una cartella nel ruolo di lavoro ibrido per runbook in cui vengono creati nuovi file, ad esempio **d:.**
   * **EXTENSION** - Estensione per la configurazione. Lasciare vuoto per elaborare tutte le estensioni di file.
   * **RECURSE** - Operazione ricorsiva. Lasciare questo valore come predefinito.
   * **RUN SETTINGS** - Impostazione per l'esecuzione del runbook. Selezionare il ruolo di lavoro ibrido.

1. Fare clic **su OK**e quindi **su Seleziona** per tornare alla pagina Watcher.
1. Selezionare **l'azione Configura** e scegliere il runbook **Process-NewFile.Select** Configure action and choose the Process-NewFile runbook.
1. Per i parametri inserire i valori seguenti:

   * **EVENTDATA** - Dati evento. Lasciare vuoto. I dati vengono passati dal runbook watcher.
   * **Run Settings** - Impostazione per l'esecuzione del runbook. Lasciare come Azure, mentre questo runbook viene eseguito in Automazione di Azure.Leave as Azure, as this runbook runs in Azure Automation.

1. Fare clic **su OK**e quindi **su Seleziona** per tornare alla pagina Watcher.
1. Fare clic **su OK** per creare l'attività Watcher.

![Configurare l'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Attivare un watcher

È necessario eseguire un test come descritto di seguito per assicurarsi che l'attività Watcher funzioni come previsto. 

1. Remoto nel ruolo di lavoro ibrido per runbook. 
2. Aprire **PowerShell** e creare un file di test nella cartella.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

L'esempio seguente illustra l'output previsto.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Esaminare i risultati

1. Passare all'account Di automazione e selezionare **Attività Watcher** in **Automazione processo**.
1. Selezionare l'attività **Watcher WatchMyFolder**.
1. Fare clic su **Visualizza flussi watcher** in **Flussi** per verificare che il watcher abbia trovato il nuovo file e abbia avviato il runbook delle azioni.
1. Per visualizzare i processi del runbook delle azioni, fare clic su **Visualizza processi azione watcher**. Ogni processo può essere selezionato per visualizzare i dettagli del processo.

   ![Processi dell'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/WatcherActionJobs.png)

L'esempio seguente illustra l'output previsto quando viene trovato il nuovo file:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Importare un runbook watcher
> * Creare una variabile di automazione
> * Creare un runbook azione
> * Creare un'attività watcher
> * Attivare un watcher
> * Esaminare i risultati

Per altre informazioni sulla creazione di un runbook, seguire questo collegamento.

> [!div class="nextstepaction"]
> [Il mio primo runbook di PowerShell](automation-first-runbook-textual-powershell.md).

