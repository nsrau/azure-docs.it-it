---
title: Eliminare un insieme di credenziali di servizi di ripristino in Azure
description: Viene descritto come eliminare un insieme di credenziali di servizi di ripristino.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: 1cc86470b9e45469d633d47121869b3c2dc1b052
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439006"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino

Questo articolo descrive come eliminare un' [Backup di Azure](backup-overview.md) insieme di credenziali dei servizi di ripristino. Contiene istruzioni per la rimozione delle dipendenze e quindi eliminare un insieme di credenziali e l'eliminazione di un insieme di credenziali tramite forza.




## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, è importante comprendere che non è possibile eliminare un insieme di credenziali di servizi di ripristino che dispone di server registrati in esso o che contiene dati di backup.


- Per eliminare un insieme di credenziali normalmente, annullare la registrazione di server in essa e rimuovere i dati dell'insieme di credenziali.
- Se non si desidera conservare i dati nell'insieme di credenziali di servizi di ripristino e si desidera eliminare l'insieme di credenziali, è possibile eliminare l'insieme di credenziali tramite forza.
- Se si tenta di eliminare un insieme di credenziali senza riuscirci, l'insieme di credenziali risulta ancora configurato per ricevere dati di backup.

Per informazioni su come eliminare un insieme di credenziali, vedere la sezione [Eliminare un insieme di credenziali dal portale di Azure](#delete-a-vault-from-the-azure-portal). Se sezione [eliminare l'insieme di credenziali tramite forza](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Se non si conosce con sicurezza il contenuto dell'insieme di credenziali e si vuole essere certi di poter eliminare l'insieme di credenziali, vedere la sezione [Rimuovere le dipendenze dell'insieme di credenziali ed eliminare l'insieme di credenziali](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-the-azure-portal"></a>Eliminare un insieme di credenziali dal portale di Azure

1. Aprire l'elenco degli insiemi di credenziali di servizi di ripristino nel portale.
2. Selezionare dall'elenco l'insieme di credenziali da eliminare. Viene aperto il dashboard dell'insieme di credenziali.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Nel dashboard dell'insieme di credenziali, fare clic su **Elimina**. Verificare che si desidera eliminare.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. Se sono presenti dipendenze di insieme di credenziali, il **insieme di credenziali di errore di eliminazione** viene visualizzata: 

    ![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-delete-error.png)

    - Seguire queste istruzioni per rimuovere le dipendenze prima di eliminare l'insieme di credenziali, esaminare
    - [Seguire queste istruzioni](#delete-the-recovery-services-vault-by-force) usare PowerShell per eliminare l'insieme di credenziali tramite forza. 

## <a name="delete-the-recovery-services-vault-by-force"></a>Eliminare l'insieme di credenziali dei servizi di ripristino

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile usare PowerShell per eliminare forzatamente un insieme di credenziali dei servizi di ripristino. Ciò significa che l'insieme di credenziali e tutti i relativi dati di backup viene eliminato definitivamente. 

> [!Warning]
> Quando si usa PowerShell per eliminare un insieme di credenziali di servizi di ripristino, verificare che si desidera eliminare definitivamente tutti i dati di backup nell'insieme di credenziali.
>

Per eliminare un insieme di credenziali dei servizi di ripristino:

1. Accedi alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire sullo schermo le direzioni.

   ```powershell
    Connect-AzAccount
   ```
2. La prima volta che si usa Backup di Azure, è necessario registrare il provider di servizi di ripristino di Azure nella sottoscrizione con [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Aprire una finestra di PowerShell con privilegi di amministratore.
4. Usare `Set-ExecutionPolicy Unrestricted` per rimuovere eventuali restrizioni.
5. Eseguire il comando seguente per scaricare il pacchetto client Azure Resource Manager da chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Usare il comando seguente per installare il client API Azure Resource Manager.

   `choco.exe install armclient`

7. Nel portale di Azure, raccogliere il nome del gruppo risorse e l'ID sottoscrizione dell'insieme di credenziali che si desidera eliminare.
8. In PowerShell eseguire il comando seguente utilizzando l'ID sottoscrizione, nome del gruppo di risorse e il nome dell'insieme di credenziali. Quando si esegue il comando, l'insieme di credenziali e tutte le dipendenze vengono eliminati.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Se l'insieme di credenziali non vuoto, viene visualizzato l'errore "Non è possibile eliminare insieme di credenziali perché contiene risorse esistenti all'interno di questo insieme di credenziali". Per rimuovere un contenitore all'interno di un insieme di credenziali, eseguire le operazioni seguenti:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Accedi alla sottoscrizione nel portale di Azure e verificare che l'insieme di credenziali viene eliminato.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Rimuovere le dipendenze dell'insieme di credenziali ed eliminare l'insieme di credenziali

È possibile rimuovere manualmente le dipendenze di insieme di credenziali, come indicato di seguito:

- Nel **elementi di Backup** menu, rimuovere le dipendenze:
    - Backup di Archiviazione di Azure (File di Azure)
    - Backup di SQL Server in macchine virtuali di Azure
    - Backup di macchine virtuali di Azure
- Nel **infrastruttura di Backup** menu, rimuovere le dipendenze:
    - Backup del Server Backup di Microsoft Azure (MABS)
    - Backup di System Center DPM

![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

### <a name="remove-backup-items"></a>Rimuovere gli elementi di backup

Questa procedura viene fornito un esempio che illustra come rimuovere i dati di backup dai file di Azure.

1. Fare clic su **elementi di Backup** > **Azure archiviazione (file di Azure)**

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Fare doppio clic su ogni elemento di file di Azure per rimuovere e fare clic su **Interrompi backup**.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/stop-backup-item.png)


3. Nelle **Interrompi Backup** > **scegliere un'opzione**, selezionare **Elimina dati di Backup**.
4. Digitare il nome dell'elemento e scegliere **Interrompi backup**. 
   - In questo modo si verifica che si desidera eliminare l'elemento.
   - Il **Interrompi Backup** pulsante attiva dopo la verifica.
   - Se si mantenere e non eliminare i dati, sarà possibile eliminare l'insieme di credenziali.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Facoltativamente, specificare un motivo per cui si vuole eliminare i dati e aggiungere commenti.
6. Per verificare che il processo di eliminazione è stata completata, controllare i messaggi di Azure ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png).
7. Dopo il completamento del processo, il servizio invia un messaggio: **è stato arrestato il processo di backup e i dati di backup sono stati eliminati**.
8. Dopo l'eliminazione di un elemento dall'elenco, nel menu **Elementi di backup** fare clic su **Aggiorna** per visualizzare gli elementi nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Rimuovere i server di infrastruttura di backup

1. Nel menu del dashboard dell'insieme di credenziali fare clic su **infrastruttura di Backup**.
2. Fare clic su **server di gestione Backup** per visualizzare i server. 

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Fare doppio clic su elemento > **Elimina**.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . Nelle **Interrompi Backup** > **scegliere un'opzione**, selezionare **Elimina dati di Backup**.
4. Digitare il nome dell'elemento e scegliere **Interrompi backup**. 
   - In questo modo si verifica che si desidera eliminare l'elemento.
   - Il **Interrompi Backup** pulsante attiva dopo la verifica.
   - Se si mantenere e non eliminare i dati, sarà possibile eliminare l'insieme di credenziali.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Facoltativamente, specificare un motivo per cui si vuole eliminare i dati e aggiungere commenti.
6. Per verificare che il processo di eliminazione è stata completata, controllare i messaggi di Azure ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png).
7. Dopo il completamento del processo, il servizio invia un messaggio: **è stato arrestato il processo di backup e i dati di backup sono stati eliminati**.
8. Dopo l'eliminazione di un elemento dall'elenco, nel menu **Elementi di backup** fare clic su **Aggiorna** per visualizzare gli elementi nell'insieme di credenziali.


### <a name="remove-azure-backup-agent-recovery-points"></a>Rimuovere i punti di ripristino dell'agente di Backup di Azure

1. Nel menu del dashboard dell'insieme di credenziali fare clic su **infrastruttura di Backup**.
2. Fare clic su **server di gestione Backup** per visualizzare i server dell'infrastruttura.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Nell'elenco **Server protetti** fare clic su Azure Backup Agent.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Selezionare il server nell'elenco dei server protetti con Azure Backup agent.

    ![Selezionare il server protetto specifico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Nel dashboard di server selezionato, fare clic su **Elimina**.

    ![Eliminare il server selezionato](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Nel menu **Elimina** digitare il nome dell'elemento e fare clic su **Elimina**.
   - In questo modo si verifica che si desidera eliminare l'elemento.
   - Il **Interrompi Backup** pulsante attiva dopo la verifica.
   - Se si mantenere e non eliminare i dati, sarà possibile eliminare l'insieme di credenziali.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Facoltativamente, specificare un motivo per cui si vuole eliminare i dati e aggiungere commenti.
8. Per verificare che il processo di eliminazione è stata completata, controllare i messaggi di Azure ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png).
7. Dopo il completamento del processo, il servizio invia un messaggio: **è stato arrestato il processo di backup e i dati di backup sono stati eliminati**.
8. Dopo l'eliminazione di un elemento dall'elenco, nel menu **Elementi di backup** fare clic su **Aggiorna** per visualizzare gli elementi nell'insieme di credenziali.



### <a name="delete-the-vault-after-removing-dependencies"></a>Eliminare l'insieme di credenziali dopo la rimozione delle dipendenze

1. Quando tutte le dipendenze sono state rimosse, scorrere fino al **Essentials** riquadro del menu dell'insieme di credenziali.

    - Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**.
    - Se nell'insieme di credenziali sono ancora presenti elementi, rimuoverli.

2. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**. L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Conseguenze dell'arresto del processo di backup con conservazione dei dati

Se si arresta il processo di backup ma conservare accidentalmente i dati, è necessario eliminare i dati di backup come descritto nelle sezioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](backup-azure-recovery-services-vault-overview.md) insiemi di credenziali dei servizi di ripristino.
