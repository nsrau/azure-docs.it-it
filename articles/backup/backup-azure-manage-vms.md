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
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295777"
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

     ![Scegliere i criteri di backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

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

- Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero. In questo caso, sarà possibile ripristinare la macchina virtuale.
- Arrestare tutti i processi di backup futuri e mantenere i punti di ripristino. Anche se è necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali, sarà possibile ripristinare la macchina virtuale, se necessario. Per altre informazioni, vedere [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Se si elimina un'origine dati senza interrompere i backup, i nuovi backup non riuscirà. Scadranno tra vecchi punti di ripristino in base ai criteri, ma un ultimo punto di ripristino verrà conservato sempre fino a quando non si arresta il backup ed eliminare i dati.
>

Arrestare la protezione per una macchina virtuale:

1. Nel [del dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Interrompi backup**.
2. Scegliere se conservare o eliminare i dati di backup e confermare la selezione in base alle esigenze. Se si desidera, aggiungere un commento. Se non si è certi del nome dell'elemento, passare il puntatore sul punto esclamativo per visualizzare il nome.

    ![Arresta protezione](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Una notifica informa l'utente che sono stati arrestati i processi di backup.

## <a name="resume-protection-of-a-vm"></a>Riprendere la protezione di una macchina virtuale

Se si mantengono i dati di backup quando si arresta la macchina virtuale, è possibile riprendere la protezione in un secondo momento. Se si eliminano i dati di backup, è Impossibile riprendere la protezione.

Per riprendere la protezione per una macchina virtuale:

1. Nel [del dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Riprendi backup**.

2. Seguire i passaggi descritti in [gestire i criteri di backup](#manage-backup-policy-for-a-vm) per assegnare i criteri per la macchina virtuale. Non è necessario scegliere i criteri di protezione iniziale della macchina virtuale.
3. Dopo aver applicato il criterio di backup nella macchina virtuale, viene visualizzato il messaggio seguente:

    ![Messaggio che indica che una macchina virtuale è stata protetta](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

È possibile eliminare i dati di backup della macchina virtuale durante la **Interrompi backup** processo o al termine del processo di backup. Prima di eliminare i dati di backup, tenere presente questi dettagli:

- Potrebbe essere una buona idea ad attendere giorni o settimane prima di eliminare i punti di ripristino.
- A differenza del processo per il ripristino dei punti di ripristino, quando si eliminano dati di backup, non è possibile scegliere di eliminare i punti di ripristino specifico. Se si eliminano i dati di backup, eliminare tutti i punti di ripristino associati.

Dopo che si arresta o disabilita il processo di backup della macchina virtuale, è possibile eliminare i dati di backup:


1. Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard), selezionare **Elimina dati di backup**.

    ![Selezionare Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

    ![Confermare che si desidera eliminare i punti di ripristino](./media/backup-azure-manage-vms/item-verification-box.png)

1. Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica consente di scoprire che i dati di backup sono stati eliminati.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [eseguire il backup di macchine virtuali di Azure dalle impostazioni della macchina virtuale](backup-azure-vms-first-look-arm.md).
- Informazioni su come [ripristino di macchine virtuali](backup-azure-arm-restore-vms.md).
- Informazioni su come [monitorare i backup della macchina virtuale di Azure](backup-azure-monitor-vms.md).
