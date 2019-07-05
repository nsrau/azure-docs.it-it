---
title: Gestire e monitorare i backup della macchina virtuale di Azure usando il servizio Backup di Azure
description: Informazioni su come gestire e monitorare i backup della macchina virtuale di Azure usando il servizio Backup di Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: add2c72535b5be0edcbc00c077dfe20a6deaa3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434224"
---
# <a name="manage-azure-vm-backups"></a>Gestire i backup delle macchine virtuali di Azure

Questo articolo descrive come gestire macchine virtuali di Azure (VM) che viene eseguito il backup usando il [servizio Backup di Azure](backup-overview.md). Inoltre, l'articolo riepiloga le informazioni di backup che è possibile trovare nel dashboard dell'insieme di credenziali.


Nel portale di Azure, il dashboard dell'insieme di credenziali di servizi di ripristino fornisce l'accesso per l'insieme di credenziali di informazioni, tra cui:

* Il backup più recente, che corrisponde anche al punto di ripristino più recente
* Il criterio di backup.
* Le dimensioni totali di tutti gli snapshot di backup.
* Il numero di macchine virtuali abilitate per il backup.

È possibile gestire i backup utilizzando il dashboard e drill-down singole macchine virtuali. Per iniziare i backup delle macchine, aprire l'insieme di credenziali nel dashboard.

![Visualizzazione di dashboard completo con dispositivo di scorrimento](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Visualizzare le macchine virtuali nel dashboard

Per visualizzare le macchine virtuali nel dashboard dell'insieme di credenziali:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu Hub, selezionare **esplorare**. Nell'elenco di risorse digitare **servizi di ripristino**. Durante la digitazione, l'elenco viene filtrato in base alle impostazioni specificate. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali di Servizi di ripristino](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Per semplicità d'uso, fare doppio clic su insieme di credenziali e selezionare **Aggiungi al dashboard**.
4. Aprire il dashboard dell'insieme di credenziali.

    ![Aprire il dashboard dell'insieme di credenziali e il pannello impostazioni](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Nel **elementi di Backup** riquadro, seleziona **macchine virtuali di Azure**.

    ![Aprire il riquadro elementi di Backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Nel **elementi di Backup** pannello, è possibile visualizzare l'elenco di macchine virtuali protette. In questo esempio, l'insieme di credenziali protegge una macchina virtuale: demobackup.  

    ![Visualizzare il pannello elementi di Backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Dal dashboard dell'elemento dell'insieme di credenziali, modificare i criteri di backup, eseguire un arresto di backup, on demand o riprendere la protezione di macchine virtuali, eliminare i dati di backup, visualizzare i punti di ripristino ed eseguire un ripristino.

    ![Il dashboard di elementi di Backup e il pannello impostazioni](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gestire i criteri di backup per una macchina virtuale

Per gestire un criterio di backup:

1. Accedere al [portale di Azure](https://portal.azure.com/). Aprire il dashboard dell'insieme di credenziali.
2. Nel **elementi di Backup** riquadro, seleziona **macchine virtuali di Azure**.

    ![Aprire il riquadro elementi di Backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Nel **degli elementi di Backup** pannello, è possibile visualizzare l'elenco di macchine virtuali protette e stato ultimo backup con ora di punti di ripristino più recente.

    ![Visualizzare il pannello elementi di Backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Dal dashboard dell'elemento dell'insieme di credenziali, è possibile selezionare un criterio di backup.

   * Per passare i criteri, selezionare un criterio diverso e quindi selezionare **salvare**. Il nuovo criterio verrà immediatamente applicato all'insieme di credenziali.

     ![Scegliere un criterio di backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta
È possibile eseguire un backup su richiesta di una macchina virtuale dopo aver impostato la protezione. Tenere presente questi dettagli:

- Se il backup iniziale è in sospeso, il backup su richiesta crea una copia completa della macchina virtuale nell'insieme di credenziali di servizi di ripristino.
- Se il backup iniziale è stato completato, un backup su richiesta invierà solo le modifiche apportate dallo snapshot precedente per l'insieme di credenziali di servizi di ripristino. Vale a dire, i backup successivi sono sempre incrementali.
- L'intervallo di conservazione per un backup su richiesta è il valore di conservazione di specificare quando si avvia il backup.

Per attivare un backup su richiesta:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), in **elemento protetto**, selezionare **Backup elemento**.

    ![L'opzione di Backup now](./media/backup-azure-manage-vms/backup-now-button.png)

2. Dal **tipo di gestione di Backup**, selezionare **macchina virtuale di Azure**. Il **elemento di Backup (macchina virtuale di Azure)** verrà visualizzato il pannello.
3. Selezionare una macchina virtuale e selezionare **Esegui Backup ora** per creare un backup su richiesta. Il **Esegui Backup ora** verrà visualizzato il pannello.
4. Nel **conserva Backup fino a** , specificare una data per il backup da conservare.

    ![Il calendario Esegui Backup ora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selezionare **OK** per eseguire il processo di backup.

Per tenere traccia dello stato del processo, nel dashboard dell'insieme di credenziali, selezionare la **processi di Backup** riquadro.

## <a name="stop-protecting-a-vm"></a>Interrompere la protezione di una macchina virtuale

Esistono due modi per interrompere la protezione di una macchina virtuale:

* **Arrestare la protezione dati e conservare i dati di backup**. Questa opzione interromperà tutti i processi di backup futuri di proteggere la macchina virtuale; Tuttavia, il servizio Backup di Azure manterrà i punti di ripristino che sono stato eseguito il backup.  Dovrai pagare per mantenere i punti di ripristino nell'insieme di credenziali (vedere [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/) per informazioni dettagliate). Sarà in grado di ripristinare la macchina virtuale, se necessario. Se si decide di riprendere la protezione della macchina virtuale, quindi è possibile usare *Riprendi backup* opzione.
* **Arrestare la protezione dati ed eliminare i dati di backup**. Questa opzione consente di arrestare tutti i processi di backup futuri di proteggere la macchina virtuale e di eliminare tutti i punti di ripristino. Non sarà in grado di ripristinare la macchina virtuale né utilizzare *Riprendi backup* opzione.

>[!NOTE]
>Se si elimina un'origine dati senza interrompere i backup, i nuovi backup non riuscirà. Scadranno tra vecchi punti di ripristino in base ai criteri, ma un ultimo punto di ripristino verrà conservato sempre fino a quando non si arresta il backup ed eliminare i dati.
>

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione dati e conservare i dati di backup

Per arrestare la protezione dati e conservare i dati di una macchina virtuale:

1. Nel [del dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Interrompi backup**.
2. Scegli **conserva i dati di Backup**e confermare la selezione in base alle esigenze. Se si desidera, aggiungere un commento. Se non si è certi del nome dell'elemento, passare il puntatore sul punto esclamativo per visualizzare il nome.

    ![Mantenere i dati di Backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Una notifica informa l'utente che sono stati arrestati i processi di backup.

### <a name="stop-protection-and-delete-backup-data"></a>Arrestare la protezione dati ed eliminare i dati di backup

Arrestare la protezione dati ed eliminare i dati di una macchina virtuale:

1. Nel [del dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Interrompi backup**.
2. Scegli **Elimina dati di Backup**e confermare la selezione in base alle esigenze. Immettere il nome dell'elemento di backup e, se necessario, aggiungere un commento.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Riprendere la protezione di una macchina virtuale

Se si è scelto [arrestare la protezione dati e conservare i dati di backup](#stop-protection-and-retain-backup-data) opzione durante l'arresto della protezione della macchina virtuale, è possibile usare **Riprendi backup**. Questa opzione non è disponibile se si sceglie [arrestare la protezione dati ed eliminare i dati di backup](#stop-protection-and-delete-backup-data) opzione oppure [Elimina dati di backup](#delete-backup-data).

Per riprendere la protezione per una macchina virtuale:

1. Nel [del dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Riprendi backup**.

2. Seguire i passaggi descritti in [gestire i criteri di backup](#manage-backup-policy-for-a-vm) per assegnare i criteri per la macchina virtuale. Non è necessario scegliere i criteri di protezione iniziale della macchina virtuale.
3. Dopo aver applicato il criterio di backup nella macchina virtuale, viene visualizzato il messaggio seguente:

    ![Messaggio che indica che una macchina virtuale è stata protetta](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

Esistono due modi per eliminare i dati di backup della macchina virtuale:

- Il dashboard dell'elemento dell'insieme di credenziali, selezionare Interrompi backup e seguire le istruzioni relative [arrestare la protezione dati ed eliminare i dati di backup](#stop-protection-and-delete-backup-data) opzione.

  ![Selezionare Interrompi backup](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- Dal dashboard dell'elemento dell'insieme di credenziali, selezionare Elimina dati di backup. Questa opzione è abilitata se si sceglie [arrestare la protezione dati e conservare i dati di backup](#stop-protection-and-retain-backup-data) opzione durante l'arresto della protezione della macchina virtuale

  ![Selezionare Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Elimina dati di backup**.
  - Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica consente di scoprire che i dati di backup sono stati eliminati.

  > [!NOTE]
  > Quando si eliminano i dati di backup vengono eliminati tutti i punti di ripristino associati. Non è possibile scegliere di eliminare i punti di ripristino specifico.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [eseguire il backup di macchine virtuali di Azure dalle impostazioni della macchina virtuale](backup-azure-vms-first-look-arm.md).
- Informazioni su come [ripristino di macchine virtuali](backup-azure-arm-restore-vms.md).
- Informazioni su come [monitorare i backup della macchina virtuale di Azure](backup-azure-monitor-vms.md).
