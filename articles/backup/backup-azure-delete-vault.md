---
title: Eliminare un insieme di credenziali di servizi di ripristino in Azure
description: Viene descritto come eliminare un insieme di credenziali di servizi di ripristino.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827888"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino

Questo articolo descrive come eliminare un' [Backup di Azure](backup-overview.md) insieme di credenziali dei servizi di ripristino. Contiene istruzioni per la rimozione delle dipendenze e quindi eliminare un insieme di credenziali.


## <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di servizi di ripristino con dipendenze, ad esempio i server protetti o server di gestione di backup associati con l'insieme di credenziali.<br/>
Impossibile eliminare l'insieme di credenziali che contiene i dati di backup (vale a dire, anche se è stata interrotta la protezione ma mantenuti i dati di backup).

Se si elimina un insieme di credenziali che contiene dipendenze, verrà visualizzato l'errore seguente:

![Errore dell'insieme di credenziali di eliminazione](./media/backup-azure-delete-vault/error.png)

Per eliminare correttamente un insieme di credenziali di seguire i passaggi indicati nella sequenza seguente:
- Arrestare la protezione dati ed eliminare i dati di backup
- Eliminare il server protetti o i server di gestione di backup
- Eliminare l'insieme di credenziali


## <a name="delete-backup-data-and-backup-items"></a>Eliminare i dati di backup e gli elementi di backup

Prima di continuare leggere altre **[ciò ](#before-you-start)** sezione per comprendere le dipendenze e processo di eliminazione dell'insieme di credenziali.

### <a name="for-protected-items-in-cloud"></a>Per gli elementi protetti nel Cloud

Per arrestare la protezione dati ed eliminare i dati di backup, eseguire il seguente:

1. Dal portale > insieme di credenziali dei servizi di ripristino > elementi di Backup scegliere gli elementi protetti nel cloud.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Per ogni elemento, è necessario fare clic e scegliere **Interrompi backup**.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/stop-backup-item.png)

3. Nelle **Interrompi Backup** > **scegliere un'opzione**, selezionare **Elimina dati di Backup**.
4. Digitare il nome dell'elemento e scegliere **Interrompi backup**.
   - In questo modo si verifica che si desidera eliminare l'elemento.
   - Il **Interrompi Backup** pulsante attiva dopo la verifica.
   - Se si mantenere e non eliminare i dati, sarà possibile eliminare l'insieme di credenziali.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Verificare i **Notification** ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png). Dopo il completamento, il servizio viene visualizzato il messaggio: **Interruzione del backup e l'eliminazione di backup dei dati per "*elemento di Backup*"** . **L'operazione è stata completata**.
6. Fare clic su **Refresh** nel **elementi di Backup** menu, per verificare se l'elemento di Backup viene rimosso.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Per l'agente di MARS

Per arrestare la protezione dati ed eliminare i dati di backup, seguire questa procedura nell'ordine indicato di seguito:

- [Passaggio 1: Eliminare gli elementi di backup dalla console di gestione di MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Passaggio 2: Dal portale di rimuovere l'agente Azure Backup](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Passaggio 1: Eliminare gli elementi di backup dalla console di gestione di MARS

Se non si riesce a eseguire questa operazione a causa dell'indisponibilità del server di contattare il supporto tecnico Microsoft.

- Avviare la console di gestione di MARS, visitare il **azioni** riquadro, quindi scegliere **pianifica Backup**.
- Dal **modificare o interrompere un Backup pianificato** procedura guidata, scegliere l'opzione **Interrompi questa pianificazione di backup ed Elimina tutti i backup archiviati** e fare clic su **Next**.

    ![Modifica o interrompi un backup pianificato](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Dal **interrompe un Backup pianificato** procedura guidata, fare clic su **fine**.

    ![Interrompi un Backup pianificato](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Viene chiesto di immettere un Pin di sicurezza. Per generare il PIN, eseguire i passaggi seguenti:
  - Accedere al portale di Azure.
  - Passare a **Insiemi di credenziali dei servizi di ripristino** > **Impostazioni** > **Proprietà**.
  - Fare clic su **Genera** in **PIN di sicurezza**. Copiare questo PIN. (Questo PIN è valido solo per cinque minuti)
- Nella Console di gestione (app client) incollare il codice PIN e fare clic su **accettabile**.

  ![Pin di sicurezza](./media/backup-azure-delete-vault/security-pin.png)

- Nel **modifica stato Backup** procedura guidata verrà visualizzato *i dati di backup eliminati verranno mantenuti per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Eliminare un'infrastruttura di Backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo avere eliminato gli elementi di backup in locale, completare i passaggi successivi dal portale.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Passaggio 2: Dal portale di rimuovere l'agente di Backup di Azure

Assicurarsi [passaggio 1](#step-1-delete-backup-items-from-mars-management-console) viene completata prima di procedere:

1. Nel menu del dashboard dell'insieme di credenziali fare clic su **infrastruttura di Backup**.
2. Fare clic su **Protected Servers** per visualizzare i server dell'infrastruttura.

    ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Nell'elenco **Server protetti** fare clic su Azure Backup Agent.

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Selezionare il server nell'elenco dei server protetti con Azure Backup agent.

    ![Selezionare il server protetto specifico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Nel dashboard di server selezionato, fare clic su **Elimina**.

    ![Eliminare il server selezionato](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Nel **eliminare** menu, digitare il nome del server e fare clic su **eliminare**.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Se viene visualizzato l'errore riportato di seguito, quindi prima di eseguire i passaggi indicati [l'eliminazione degli elementi di backup dalla console di gestione](#step-1-delete-backup-items-from-mars-management-console).
>
>![eliminazione non riuscita](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Se non si riesce a eseguire la procedura per eliminare i backup dalla console di gestione, ad esempio, a causa dell'indisponibilità del server con la console di gestione, contattare il supporto tecnico Microsoft.

7. Verificare i **Notification** ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png). Dopo il completamento, il servizio viene visualizzato il messaggio: **Interruzione del backup e l'eliminazione di backup dei dati per "*elemento di Backup*"** . **L'operazione è stata completata**.
8. Fare clic su **Refresh** nel **elementi di Backup** menu, per verificare se l'elemento di Backup viene rimosso.


### <a name="for-mabs-agent"></a>Per l'agente di backup di Microsoft AZURE

Per arrestare la protezione dati ed eliminare i dati di backup, seguire questa procedura nell'ordine indicato di seguito:

- [Passaggio 1: Eliminare gli elementi di backup dalla console di gestione di backup di Microsoft AZURE](#step-1-delete-backup-items-from-mabs-management-console)
- [Passaggio 2: Rimuovere il server di gestione di Backup di Azure dal portale](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Passaggio 1: Eliminare gli elementi di backup dalla console di gestione di backup di Microsoft AZURE

Se non si riesce a eseguire questa operazione a causa dell'indisponibilità del server di contattare il supporto tecnico Microsoft.

**Metodo 1** per arrestare la protezione dati ed eliminare i dati di backup, eseguire i passaggi seguenti:

1.  Nella Console amministrazione DPM fare clic su **Protection** sulla barra di spostamento.
2.  Nel riquadro informazioni selezionare il membro del gruppo protezione dati che si desidera rimuovere. Pulsante destro del mouse per scegliere **Arresta protezione dati dei membri del gruppo** opzione.
3.  Dal **Arresta protezione** finestra di dialogo **Elimina dati protetti** > **eliminare l'archivio online** casella di controllo e quindi fare clic su **arrestare Protezione**.

    ![Eliminare l'archivio online](./media/backup-azure-delete-vault/delete-storage-online.png)

Lo stato di membro protetto è ora diventato **replica inattiva disponibile**.

5. Il gruppo protezione dati inattiva e scegliere **Rimuovi protezione dati inattiva**.

    ![Rimuovi protezione dati inattiva](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Dal **Elimina protezione dati inattiva** finestra, seleziona **eliminare l'archivio online** e fare clic su **Ok**.

    ![Rimuovere le repliche su disco e non in linea](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metodo 2** avviare la **MABS gestione** console. Nel **Seleziona metodo protezione dati** sezione deselezionate **protezione dati online**.

  ![Seleziona metodo protezione dati](./media/backup-azure-delete-vault/data-protection-method.png)

Dopo avere eliminato gli elementi di backup in locale, completare i passaggi successivi dal portale.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Passaggio 2: Rimuovere il server di gestione di Backup di Azure dal portale

Assicurarsi [passaggio 1](#step-1-delete-backup-items-from-mabs-management-console) viene completata prima di procedere:

1. Nel menu del dashboard dell'insieme di credenziali fare clic su **infrastruttura di Backup**.
2. Fare clic su **server di gestione Backup** per visualizzare i server.

    ![Seleziona insieme di credenziali per aprire il dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Fare doppio clic su elemento > **Elimina**.
4. Nel **eliminare** menu, digitare il nome del server e fare clic su **eliminare**.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Se viene visualizzato l'errore riportato di seguito, quindi prima di eseguire i passaggi indicati [l'eliminazione degli elementi di backup dalla console di gestione](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![eliminazione non riuscita](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Se non si riesce a eseguire la procedura per eliminare i backup dalla console di gestione, ad esempio, a causa dell'indisponibilità del server con la console di gestione, contattare il supporto tecnico Microsoft.

5. Verificare i **Notification** ![Elimina dati di backup](./media/backup-azure-delete-vault/messages.png). Dopo il completamento, il servizio viene visualizzato il messaggio: **Interruzione del backup e l'eliminazione di backup dei dati per "*elemento di Backup*"** . **L'operazione è stata completata**.
6. Fare clic su **Refresh** nel **elementi di Backup** menu, per verificare se l'elemento di Backup viene rimosso.


## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

1. Quando tutte le dipendenze sono state rimosse, scorrere fino al **Essentials** riquadro del menu dell'insieme di credenziali.
2. Verificare che non vi siano **elementi di Backup**, **server di gestione di Backup**, o **elementi replicati** elencati. Se nell'insieme di credenziali, sono ancora presenti elementi [rimuoverli](#delete-backup-data-and-backup-items).

3. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**. L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminare l'insieme di credenziali di servizi di ripristino usando il client di Azure Resource Manager

Questa opzione per eliminare l'insieme di credenziali di servizi di ripristino è consigliata solo quando vengono rimosse tutte le dipendenze e si riceve comunque i *insieme di credenziali di errore di eliminazione*.



- Dal **Essentials** riquadro nel menu dell'insieme di credenziali, verificare che non vi siano **elementi di Backup**, **server di gestione di Backup**, o **elementireplicati** elencati. Se sono presenti elementi di backup, quindi eseguire la procedura nel [Elimina dati di backup e gli elementi di backup](#delete-backup-data-and-backup-items).
- Ripetere [eliminando l'insieme di credenziali dal portale](#delete-the-recovery-services-vault).
- Se vengono rimosse tutte le dipendenze e si riceve comunque i *insieme di credenziali di errore di eliminazione* quindi utilizzare lo strumento ARMClient per eseguire i passaggi indicati di seguito;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installazione di chocolatey dal [qui](https://chocolatey.org/) e per installare ARMClient eseguire il comando seguente:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Accedi al tuo account Azure ed eseguire questo comando:

    `ARMClient.exe login [environment name]`

3. Nel portale di Azure, raccogliere il nome del gruppo risorse e l'ID sottoscrizione dell'insieme di credenziali che si desidera eliminare.

Per altre informazioni sul comando ARMClient, fare riferimento a questo [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usare il client di Azure Resource Manager per eliminare l'insieme di credenziali di servizi di ripristino

1. Eseguire il comando seguente utilizzando l'ID sottoscrizione, nome del gruppo di risorse e il nome dell'insieme di credenziali. Quando si esegue il comando, si elimina l'insieme di credenziali se non si ha alcuna dipendenza.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se l'insieme di credenziali non è vuota, si riceverà l'errore "Insieme di credenziali non può essere eliminato sono disponibili le risorse esistenti all'interno di questo insieme di credenziali". Per rimuovere un elementi protetti / contenitore all'interno di un insieme di credenziali, eseguire le operazioni seguenti:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nel portale di Azure, verificare che l'insieme di credenziali viene eliminato.


## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](backup-azure-recovery-services-vault-overview.md) insiemi di credenziali dei servizi di ripristino.<br/>
[Informazioni su](backup-azure-manage-windows-server.md) monitorare e gestire gli insiemi di credenziali di servizi di ripristino.
