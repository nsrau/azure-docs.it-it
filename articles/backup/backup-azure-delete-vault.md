---
title: Eliminare un insieme di credenziali di servizi di ripristino in Azure
description: Viene descritto come eliminare un insieme di credenziali di servizi di ripristino.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: raynew
ms.openlocfilehash: e7cea725a25d48ac9f1ffad6acc434e21145890e
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473253"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino

Questo articolo descrive come eliminare un' [Backup di Azure](backup-overview.md) insieme di credenziali dei servizi di ripristino. Contiene istruzioni per la rimozione delle dipendenze e quindi eliminare un insieme di credenziali e l'eliminazione di un insieme di credenziali tramite forza.


## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, è importante comprendere che non è possibile eliminare un insieme di credenziali di servizi di ripristino che dispone di server registrati in esso o che contiene dati di backup.

- Per eliminare un insieme di credenziali normalmente, annullare la registrazione di server in essa, rimuovere i dati dell'insieme di credenziali e quindi eliminare l'insieme di credenziali.
- Se si prova a eliminare un insieme di credenziali che ancora dispone di dipendenze, viene generato un messaggio di errore. ed è necessario rimuovere manualmente le dipendenze dell'insieme di credenziali, tra cui:
    - Il backup di elementi
    - Server protetti
    - Server di gestione (Server di Backup di Azure, Data Protection Manager) di backup ![selezionare l'insieme di credenziali per aprire il dashboard](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Se non si desidera conservare i dati nell'insieme di credenziali di servizi di ripristino e si desidera eliminare l'insieme di credenziali, è possibile eliminare l'insieme di credenziali tramite forza.
- Se si tenta di eliminare un insieme di credenziali senza riuscirci, l'insieme di credenziali risulta ancora configurato per ricevere dati di backup.


## <a name="delete-a-vault-from-the-azure-portal"></a>Eliminare un insieme di credenziali dal portale di Azure

1. Aprire il dashboard dell'insieme di credenziali.  
2. Nel dashboard, fare clic su **Elimina**. Verificare che si desidera eliminare.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Se si riceve un errore, rimuovere [elementi di backup](#remove-backup-items), [server di infrastruttura](#remove-backup-infrastructure-servers), e [i punti di ripristino](#remove-azure-backup-agent-recovery-points)e quindi eliminare l'insieme di credenziali.

![Errore dell'insieme di credenziali di eliminazione](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminare l'insieme di credenziali di servizi di ripristino usando il client di Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installazione di chocolatey dal [qui](https://chocolatey.org/) e per installare ARMClient eseguire il comando seguente:

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Accedere all'account Azure, che esegue il comando seguente

    ` ARMClient.exe login [environment name] `

3. Nel portale di Azure, raccogliere il nome del gruppo risorse e l'ID sottoscrizione dell'insieme di credenziali che si desidera eliminare.

Per altre informazioni sul comando ARMClient, fare riferimento a questo [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usare il client di Azure Resource Manager per eliminare l'insieme di credenziali di servizi di ripristino

1. Eseguire il comando seguente utilizzando l'ID sottoscrizione, nome del gruppo di risorse e il nome dell'insieme di credenziali. W\hen è eseguire il comando che elimina l'insieme di credenziali se non si ha alcuna dipendenza.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se l'insieme di credenziali non vuoto, viene visualizzato l'errore "Non è possibile eliminare insieme di credenziali perché contiene risorse esistenti all'interno di questo insieme di credenziali". Per rimuovere un elementi protetti / contenitore all'interno di un insieme di credenziali, eseguire le operazioni seguenti:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nel portale di Azure, verificare che l'insieme di credenziali viene eliminato.


## <a name="remove-vault-items-and-delete-the-vault"></a>Rimuovere gli elementi dell'insieme di credenziali ed eliminare l'insieme di credenziali

Queste procedure forniscono alcuni esempi per la rimozione dei dati di backup e i server di infrastruttura. Dopo che tutto ciò che viene rimosso da un insieme di credenziali, è possibile eliminarlo.

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

3. Fare doppio clic su elemento > **Elimina**.
4. Per verificare che il processo di eliminazione è stata completata, controllare i messaggi di Azure ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png).
5. Dopo il completamento del processo, il servizio invia un messaggio: **è stato arrestato il processo di backup e i dati di backup sono stati eliminati**.
6. Dopo l'eliminazione di un elemento nell'elenco, nella **infrastruttura di Backup** menu, fare clic su **aggiornare** per visualizzare gli elementi nell'insieme di credenziali.


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

     ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Facoltativamente, specificare un motivo per cui si vuole eliminare i dati e aggiungere commenti.
8. Per verificare che il processo di eliminazione è stata completata, controllare i messaggi di Azure ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png).
9. Dopo l'eliminazione di un elemento nell'elenco, nella **infrastruttura di Backup** menu, fare clic su **aggiornare** per visualizzare gli elementi nell'insieme di credenziali.

### <a name="delete-the-vault-after-removing-dependencies"></a>Eliminare l'insieme di credenziali dopo la rimozione delle dipendenze

1. Quando tutte le dipendenze sono state rimosse, scorrere fino al **Essentials** riquadro del menu dell'insieme di credenziali.
2. Verificare che non vi siano **elementi di Backup**, **server di gestione di Backup**, o **elementi replicati** elencati. Se nell'insieme di credenziali sono ancora presenti elementi, rimuoverli.

3. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**. L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Conseguenze dell'arresto del processo di backup con conservazione dei dati

Se si arresta il processo di backup ma conservare accidentalmente i dati, è necessario eliminare i dati di backup come descritto nelle sezioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](backup-azure-recovery-services-vault-overview.md) insiemi di credenziali dei servizi di ripristino.
