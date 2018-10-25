---
title: Eliminare un insieme di credenziali di Servizi di ripristino in Azure
description: Questo articolo descrive come eliminare un insieme di credenziali dei servizi di ripristino. L'articolo include procedure per la risoluzione dei problemi quando si tenta di eliminare un insieme di credenziali senza riuscirci.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: e5bc68b55fdefc07b9764b8393ea6f4062468252
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816464"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino

Questo articolo descrive come rimuovere tutti gli elementi da un insieme di credenziali dei servizi di ripristino e successivamente eliminare l'insieme di credenziali. Se un insieme di credenziali dei servizi di ripristino è registrato in un server e conserva dati di backup, non è possibile eliminarlo. Se si tenta di eliminare un insieme di credenziali senza riuscirci, l'insieme di credenziali risulta ancora configurato per ricevere dati di backup.

Per informazioni su come eliminare un insieme di credenziali, vedere la sezione [Eliminare un insieme di credenziali dal portale di Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Se non si desidera conservare dati nell'insieme di credenziali dei servizi di ripristino e si desidera eliminare l'insieme di credenziali, vedere la sezione [Eliminare forzatamente l'insieme di credenziali](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Se non si conosce con sicurezza il contenuto dell'insieme di credenziali e si vuole essere certi di poter eliminare l'insieme di credenziali, vedere la sezione [Rimuovere le dipendenze dell'insieme di credenziali ed eliminare l'insieme di credenziali](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Eliminare un insieme di credenziali dal portale di Azure

Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al secondo passaggio.

1. Aprire il portale di Azure e dal dashboard aprire l'insieme di credenziali da eliminare.

   Se l'insieme di credenziali dei servizi di ripristino non è aggiunto al dashboard, nel menu Hub fare clic su **Tutti i servizi** e nell'elenco di risorse digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Per visualizzare l'elenco degli insiemi di credenziali presenti nella propria sottoscrizione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. 

   ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. Selezionare dall'elenco l'insieme di credenziali da eliminare. Quando si seleziona l'insieme di credenziali, viene aperto il dashboard corrispondente.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Per eliminare un insieme di credenziali, nel relativo dashboard fare clic su **Elimina**. Verrà chiesto di confermare di voler eliminare l'insieme di credenziali.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Se viene visualizzato il messaggio **Errore di eliminazione dell'insieme di credenziali**, è possibile rimuovere le dipendenze dall'insieme di credenziali oppure è possibile usare PowerShell per eliminare l'insieme di credenziali forzatamente. Le sezioni seguenti descrivono come eseguire queste attività.

    ![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Eliminare l'insieme di credenziali dei servizi di ripristino

È possibile usare PowerShell per eliminare forzatamente un insieme di credenziali dei servizi di ripristino. Per forzatamente si intende che l'insieme di credenziali dei servizi di ripristino e tutti i dati di backup associati vengono eliminati in modo definitivo. 

> [!Warning]
> Quando si usa PowerShell per eliminare un insieme di credenziali dei servizi di ripristino, è necessario essere certi di voler eliminare definitivamente tutti i dati di backup presenti nell'insieme di credenziali.
>

Per eliminare un insieme di credenziali dei servizi di ripristino:

1. Accedere all'account Azure.

   Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e seguire le istruzioni visualizzate.

   ```powershell
    Connect-AzureRmAccount
   ```
   Quando si usa Backup di Azure per la prima volta, è necessario registrare il provider di Servizi di ripristino di Azure nella sottoscrizione con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Aprire una finestra di PowerShell con privilegi di amministratore.

1. Usare `Set-ExecutionPolicy Unrestricted` per rimuovere eventuali restrizioni.

1. Eseguire il comando seguente per scaricare il pacchetto client Azure Resource Manager da chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Usare il comando seguente per installare il client API Azure Resource Manager.

   `choco.exe install armclient`

1. Nel portale di Azure raccogliere l'ID sottoscrizione e il nome del gruppo di risorse associato per l'insieme di credenziali dei servizi di ripristino che si desidera eliminare.

1. In PowerShell, eseguire il comando riportato di seguito usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali dei servizi di ripristino. Quando si esegue il comando, l'insieme di credenziali e tutte le dipendenze vengono eliminati.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   L'insieme di credenziali deve essere vuoto prima che sia possibile eliminarlo. In caso contrario, viene visualizzato l'errore "Non è possibile eliminare l'insieme di credenziali perché contiene risorse esistenti". Il comando seguente illustra come rimuovere un contenitore in un insieme di credenziali:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```
   
1. Accedere alla propria sottoscrizione nel portale di Azure e verificare che l'insieme di credenziali sia eliminato.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Rimuovere le dipendenze dell'insieme di credenziali ed eliminare l'insieme di credenziali

Per rimuovere manualmente le dipendenze dell'insieme di credenziali, eliminare la configurazione tra ogni elemento o server e l'insieme di credenziali dei servizi di ripristino. Man mano che si eseguono i passaggi della procedura seguente, usare il menu **Elementi di backup** (vedere l'immagine) per:

* Backup di Archiviazione di Azure (File di Azure)
* Backup di SQL Server in macchine virtuali di Azure
* Backup di macchine virtuali di Azure
* Backup dell'agente di Servizi di ripristino di Microsoft Azure

Usare il menu **Infrastruttura di backup** (vedere l'immagine) per:

* Backup di server di Backup di Azure
* Backup di System Center DPM

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. Nel menu del dashboard dell'insieme di credenziali, scorrere fino alla sezione Elementi protetti e fare clic su **Elementi di backup**. In questo menu è possibile arrestare ed eliminare i file server di Azure, i server SQL nella macchina virtuale di Azure e le macchine virtuali di Azure. In questo esempio vengono rimossi i dati di backup da un file server di Azure.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Selezionare un tipo di backup per visualizzare tutti gli elementi di quel tipo.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Per tutti gli elementi nell'elenco, fare clic con il pulsante destro del mouse su un elemento e nel menu contestuale selezionare **Interrompi backup**.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Verrà visualizzato il menu Arresta backup.

1. Nel menu **Interrompi backup**, nel menu **Scegliere un'opzione** selezionare **Elimina dati di backup**, digitare il nome dell'elemento e fare clic su **Interrompi backup**.

    Digitare il nome dell'elemento per confermare che si desidera eliminarlo. Il pulsante **Interrompi backup** si attiva dopo aver verificato l'elemento. Se si conservano i dati, non sarà possibile eliminare l'insieme di credenziali.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Se lo si desidera, è possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Al termine del processo, il servizio invia un messaggio che indica che *il processo di backup è stato arrestato e che i dati di backup sono stati eliminati*.

1. Dopo l'eliminazione di un elemento dall'elenco, nel menu **Elementi di backup** fare clic su **Aggiorna** per visualizzare gli elementi nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel menu Insieme di credenziali di Servizi di ripristino. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  

1. Quando sulla barra degli strumenti dell'insieme di credenziali non sono più presenti elementi, fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="removing-azure-backup-server-or-dpm"></a>Rimozione del server di Backup di Azure o del server DPM

1. Nel menu del dashboard dell'insieme di credenziali, scorrere fino alla sezione Gestione e fare clic su **Infrastruttura di backup**. 

1. Nel sottomenu fare clic su **Server di gestione di backup** per visualizzare i server di Backup di Azure e il server System Center DPM. È possibile arrestare ed eliminare i file server di Azure, i server SQL nella macchina virtuale di Azure e le macchine virtuali di Azure. 

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Fare clic con il pulsante destro del mouse sull'elemento che si desidera eliminare e nel sottomenu selezionare **Elimina**.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Verrà visualizzato il menu Arresta backup.

1. Nel menu **Interrompi backup**, nel menu **Scegliere un'opzione** selezionare **Elimina dati di backup**, digitare il nome dell'elemento e fare clic su **Interrompi backup**.

    Per confermare l'elemento che si desidera eliminare, digitarne il nome. Il pulsante **Interrompi backup** si attiva dopo aver verificato l'elemento. Se si conservano i dati, non sarà possibile eliminare l'insieme di credenziali.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Al termine del processo, il servizio invia un messaggio che indica che il processo di backup è stato arrestato e che i dati di backup sono stati eliminati.

1. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel menu Insieme di credenziali di Servizi di ripristino. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  
1. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.


## <a name="removing-azure-backup-agent-recovery-points"></a>Rimozione di punti di ripristino di Azure Backup Agent

1. Nel menu del dashboard dell'insieme di credenziali, scorrere fino alla sezione Gestione e fare clic su **Infrastruttura di backup**.

1. Nel sottomenu fare clic su **Server protetti** per visualizzare l'elenco di tipi di server protetti, incluso Azure Backup Agent.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. Nell'elenco **Server protetti** fare clic su Azure Backup Agent.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Viene aperto l'elenco di server protetti che usano Azure Backup Agent.

    ![Selezionare il server protetto specifico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. Nell'elenco di server selezionarne uno per aprire il relativo menu.

    ![Visualizzare il dashboard del server selezionato](./media/backup-azure-delete-vault/selected-protected-server.png)

1. Nel menu del dashboard del server selezionato, fare clic su **Elimina**.

    ![Eliminare il server selezionato](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. Nel menu **Elimina** digitare il nome dell'elemento e fare clic su **Elimina**.

    Digitare il nome dell'elemento per confermare che si desidera eliminarlo. Il pulsante **Elimina** si attiva dopo che l'elemento è stato confermato.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Al termine del processo, il servizio invia un messaggio che indica che il processo di backup è stato arrestato e che i dati di backup sono stati eliminati.

1. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel menu Insieme di credenziali di Servizi di ripristino. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  
1. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Conseguenze dell'arresto del processo di backup con conservazione dei dati

Se si arresta il processo di backup ma si *conservano* accidentalmente i dati, è necessario eliminare prima di tutto i dati di backup per poter eliminare l'insieme di credenziali. Per eliminare i dati di backup:

1. Nel menu **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Elimina dati di backup** dal menu contestuale.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Verrà visualizzato il menu **Elimina dati di backup**.
1. Nel menu **Elimina dati di backup** immettere il nome dell'elemento e fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Dopo avere eliminato i dati, tornare al passaggio 4c e continuare con il processo.
