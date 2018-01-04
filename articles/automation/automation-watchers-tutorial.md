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
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 294faa48f9840919b087594835706bad8048d45b
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Creare attività per tenere traccia delle modifiche del file in un computer locale di un controllo di automazione di Azure

Automazione di Azure utilizza le attività di controllo per il controllo degli eventi e attivare le azioni. In questa esercitazione viene descritto come creare un'attività di monitoraggio per il monitoraggio quando viene aggiunto un nuovo file in una directory.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Importare un runbook di monitoraggio
> * Creare una variabile di automazione
> * Creare un runbook di azione
> * Creare un'attività di controllo
> * Attivare un controllo
> * Esaminare l'output

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, siano soddisfatti i seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) dove viene eseguita l'attività watcher.

## <a name="import-a-watcher-runbook"></a>Importare un runbook di monitoraggio

Questa esercitazione viene utilizzato un runbook di controllo denominato **Watch NewFile** per cercare i nuovi file in una directory. Il runbook controllo recupera l'ora dell'ultima scrittura noti per i file in una cartella ed esamina tutti i file più recenti rispetto a tale limite. In questo passaggio, si importa questo runbook nell'account di automazione.

1. Aprire l'account di automazione e fare clic su di **runbook** pagina.
1. Fare clic su di **Sfoglia raccolta** pulsante.
1. Ricerca di "Controllo runbook", selezionare **runbook di monitoraggio che controlla la presenza di nuovi file in una directory** e selezionare **importazione**.
  ![Importazione di runbook di automazione interfaccia utente](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Assegnare il runbook, un nome e descrizione e selezionare **OK** per importare il runbook nell'account di automazione.
1. Selezionare **modifica** e quindi fare clic su **pubblica**. Quando viene richiesto di selezionare **Sì** a pubblicare il runbook.

## <a name="create-an-automation-variable"></a>Creare una variabile di automazione

Un [variabile di automazione](automation-variables.md) viene utilizzato per archiviare i timestamp che il runbook precedente legge e archivia ogni file. 

1. Selezionare **variabili** in **risorse CONDIVISE** e selezionare **+ Aggiungi una variabile**.
1. Immettere "Watch-NewFileTimestamp" per il nome
1. Selezionare una data/ora per il tipo.
1. Fare clic su di **crea** pulsante. Crea la variabile di automazione.

## <a name="create-an-action-runbook"></a>Creare un runbook di azione

Un runbook di azione viene usato in un'attività di controllo per agire sui dati passati da un runbook di monitoraggio. In questo passaggio, si aggiorna importazione un runbook di azione predefinito denominato "Processo NewFile".

1. Passare al proprio account di automazione e selezionare **runbook** sotto il **automazione dei processi** categoria.
1. Fare clic su di **Sfoglia raccolta** pulsante.
1. Eseguire la ricerca di "Azione di controllo" e selezionare **azione di controllo che elabora gli eventi attivati da un runbook watcher** e selezionare **importazione**.
  ![Importare runbook azione dell'interfaccia utente](media/automation-watchers-tutorial/importsourceaction.png)
1. Assegnare il runbook, un nome e descrizione e selezionare **OK** per importare il runbook nell'account di automazione.
1. Selezionare **modifica** e quindi fare clic su **pubblica**. Quando viene richiesto di selezionare **Sì** a pubblicare il runbook.

## <a name="create-a-watcher-task"></a>Creare un'attività di controllo

L'attività di controllo contiene due parti. Il controllo e l'azione. Il controllo viene eseguito in un intervallo definito nell'attività di controllo. Dati di controllo runbook sono passati al runbook azione. In questo passaggio, configurare l'attività di monitoraggio che fanno riferimento ai runbook di monitoraggio e azione definiti nei passaggi precedenti.

1. Passare al proprio account di automazione e selezionare **attività controllo** sotto il **automazione dei processi** categoria.
1. Selezionare la pagina attività di controllo e fare clic su **+ Aggiungi un'attività Monitoraggio** pulsante.
1. Immettere "WatchMyFolder" come nome.

1. Selezionare **Configura controllo** e selezionare il **Watch NewFile** runbook.

1. Per i parametri inserire i valori seguenti:

   * **FOLDERPATH** -una cartella nel processo di lavoro ibrido in cui verranno creati nuovi file. d:\examplefiles
   * **ESTENSIONE** -lasciare vuoto per elaborare tutte le estensioni di file.
   * **RECURSE** -specifica un valore come impostazione predefinita.
   * **IMPOSTAZIONI di esecuzione** -selezionare il ruolo di lavoro ibrido.

1. Fare clic su OK e quindi su Seleziona per tornare alla pagina del watcher.
1. Selezionare **configurare azione** e selezionare il runbook di "Processo NewFile".
1. Per i parametri inserire i valori seguenti:

   *    **EVENTDATA** -lasciare vuoto. I dati vengono passati dal runbook watcher.  
   *    **Impostazioni esecuzione test** -lasciare Azure come il runbook viene eseguito nel servizio di automazione.

1. Fare clic su **OK**, quindi selezionare per tornare alla pagina di controllo.
1. Fare clic su **OK** per creare l'attività Monitoraggio.

![Configurare l'azione di controllo dall'interfaccia utente](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Attivare un controllo

Per testare il controllo funziona come previsto, è necessario creare un file di test.

Accedere in remoto il worker ibrido. Aprire **PowerShell** e creare un file di test nella cartella.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

Nell'esempio seguente viene illustrato l'output previsto.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Esaminare l'output

1. Passare al proprio account di automazione e selezionare **attività controllo** sotto il **automazione dei processi** categoria.
1. Selezionare l'attività di monitoraggio "WatchMyFolder".
1. Fare clic su **visualizzare flussi di controllo** in **flussi** per verificare che il controllo trovato il nuovo file e avviato il runbook di azione.
1. Per visualizzare i processi runbook di azione, fare clic su di **visualizzare i processi di azione di controllo**. Ogni processo può essere selezionata la vista Dettagli del processo.

   ![Processi di azione di controllo dall'interfaccia utente](media/automation-watchers-tutorial/WatcherActionJobs.png)

L'output previsto quando viene trovato il nuovo file può essere visualizzato nell'esempio seguente:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Importare un runbook di monitoraggio
> * Creare una variabile di automazione
> * Creare un runbook di azione
> * Creare un'attività di controllo
> * Attivare un controllo
> * Esaminare l'output

Fare clic sul collegamento per ulteriori informazioni sulla creazione di propri runbook.

> [!div class="nextstepaction"]
> [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
