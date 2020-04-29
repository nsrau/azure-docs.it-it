---
title: Creare un'attività watcher nell'account di Automazione di Azure
description: Informazioni su come creare un'attività watcher nell'account di Automazione di Azure per controllare i nuovi file creati in una cartella.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617353"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Creare un'attività watcher di Automazione di Azure per tenere traccia delle modifiche dei file in un computer locale

Automazione di Azure usa un'attività Watcher per cercare eventi e attivare azioni con manuali operativi di PowerShell. L'attività Watcher contiene due parti: Watcher e Action. Un Runbook Watcher viene eseguito a un intervallo definito nell'attività Watcher e restituisce i dati a un'azione runbook. 

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
* Manuali operativi di PowerShell. I manuali operativi del flusso di lavoro PowerShell non sono supportati dalle attività Watcher.

> [!NOTE]
> Le attività Watcher non sono supportate in Azure Cina.

## <a name="import-a-watcher-runbook"></a>Importare un runbook watcher

Questa esercitazione usa un runbook watcher denominato **Watch-NewFile** per cercare nuovi file in una directory. Il runbook watcher recupera la data e l'ora dell'ultima scrittura nota nei file di una cartella e cerca qualsiasi file che risulti più recente di quel limite.

Questo processo di importazione può essere eseguito tramite il [PowerShell Gallery](https://www.powershellgallery.com).

1. Passare alla pagina della raccolta per [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Nella scheda **automazione di Azure** fare clic su **Distribuisci in automazione di Azure**.

È anche possibile importare questo runbook nell'account di automazione dal portale seguendo questa procedura.

1. Aprire l'account di automazione e fare clic sulla pagina Runbook.
2. Fare clic su **Esplora raccolta**.
3. Cercare **Watcher Runbook**, selezionare **Watcher Runbook che cerca i nuovi file in una directory**e fare clic su **Importa**.
  ![Importare un runbook di automazione dall'interfaccia utente](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Assegnare a Runbook un nome e una descrizione e fare clic su **OK** per importare il Runbook nell'account di automazione.
5. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Quando richiesto, fare clic su **Sì** per pubblicare il Runbook.

## <a name="create-an-automation-variable"></a>Creare una variabile di automazione

Una [variabile di automazione](automation-variables.md) viene usata per archiviare i timestamp che il runbook precedente legge e archivia da ogni file.

1. Selezionare **variabili** in **risorse condivise** e fare clic su **+ Aggiungi variabile**.
1. Immettere Watch-NewFileTimestamp per il nome.
1. Selezionare DateTime per il tipo.
1. Fare clic su **Crea** per creare la variabile di automazione.

## <a name="create-an-action-runbook"></a>Creare un runbook azione

Un runbook azione viene usato in un'attività watcher per agire sui dati passati da un runbook watcher. È necessario importare un Runbook di azione predefinito denominato **Process-NewFile** dal [PowerShell Gallery](https://www.powershellgallery.com). 

Per creare un'azione runbook:

1. Passare alla pagina della raccolta per [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Nella scheda **automazione di Azure** fare clic su **Distribuisci in automazione di Azure**.

È anche possibile importare questo runbook nell'account di automazione dall'portale di Azure:

1. Passare all'account di automazione e selezionare **manuali operativi** in **automazione processi**.
1. Fare clic su **Esplora raccolta**.
1. Cercare **azione Watcher**, selezionare **azione Watcher che elabora gli eventi attivati da un Runbook Watcher**e fare clic su **Importa**.
  ![Importare un runbook azione dall'interfaccia utente](media/automation-watchers-tutorial/importsourceaction.png)
1. Assegnare a Runbook un nome e una descrizione e fare clic su **OK** per importare il Runbook nell'account di automazione.
1. Selezionare **Modifica** e quindi fare clic su **Pubblica**. Quando richiesto, fare clic su **Sì** per pubblicare il Runbook.

## <a name="create-a-watcher-task"></a>Creare un'attività watcher

In questo passaggio viene configurata l'attività Watcher che fa riferimento alle manuali operativi Watcher e Action definite nelle sezioni precedenti.

1. Passare all'account di automazione e selezionare **attività Watcher** in **automazione processi**.
1. Selezionare la pagina attività Watcher e fare clic su **+ Aggiungi un'attività Watcher**.
1. Immettere **WatchMyFolder** come nome.

1. Selezionare **Configura Watcher** e scegliere il Runbook **Watch-NewFile** .

1. Per i parametri inserire i valori seguenti:

   * **FOLDERPATH** : cartella nel ruolo di lavoro ibrido per Runbook in cui vengono creati nuovi file, ad esempio **d:\examplefiles**.
   * **Extension** : estensione per la configurazione. Lasciare vuoto per elaborare tutte le estensioni di file.
   * **Recurse** -operazione ricorsiva. Lasciare il valore predefinito.
   * **Impostazioni esecuzione test** : impostazione per l'esecuzione del Runbook. Selezionare il ruolo di lavoro ibrido.

1. Fare clic su **OK**e quindi **selezionare** per tornare alla pagina Watcher.
1. Selezionare **Configura azione** e scegliere il **processo-NewFile** Runbook.
1. Per i parametri inserire i valori seguenti:

   * **EventData** -dati dell'evento. Lasciare vuoto. I dati vengono passati dal runbook watcher.
   * **Impostazioni esecuzione test** : impostazione per l'esecuzione del Runbook. Lascia come Azure, perché questo Runbook viene eseguito in automazione di Azure.

1. Fare clic su **OK**e quindi **selezionare** per tornare alla pagina Watcher.
1. Fare clic su **OK** per creare l'attività Watcher.

![Configurare l'azione watcher dall'interfaccia utente](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Attivare un watcher

È necessario eseguire un test come descritto di seguito per assicurarsi che l'attività Watcher funzioni come previsto. 

1. Accedere in remoto al ruolo di lavoro ibrido per Runbook. 
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

1. Passare all'account di automazione e selezionare **attività Watcher** in **automazione processi**.
1. Selezionare l'attività Watcher **WatchMyFolder**.
1. Fare clic su **Visualizza flussi Watcher** sotto **flussi** per verificare che il Watcher abbia trovato il nuovo file e abbia avviato l'azione runbook.
1. Per visualizzare l'azione runbook processi, fare clic su **Visualizza processi di azione Watcher**. Ogni processo può essere selezionato per visualizzare i dettagli del processo.

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
> [Il primo Runbook PowerShell](automation-first-runbook-textual-powershell.md).

