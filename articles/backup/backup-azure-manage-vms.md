---
title: Gestire e monitorare il backup di macchine virtuali di Azure con il servizio Backup di Azure
description: Informazioni su come gestire e monitorare il backup di macchine virtuali di Azure con il servizio Backup di Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430064"
---
# <a name="manage-azure-vm-backups"></a>Gestire i backup delle macchine virtuali di Azure

Questo articolo descrive come gestire le macchine virtuali di Azure di cui è stato eseguito il backup con il [servizio Backup di Azure](backup-overview.md) e riepiloga le informazioni degli avvisi di backup disponibili nel dashboard del portale.


Nel portale di Azure il dashboard dell'insieme di credenziali di Servizi di ripristino consente di accedere alle informazioni relative all'insieme di credenziali, quali:

* Il backup più recente, che corrisponde anche al punto di ripristino più recente
* I criteri di backup
* Le dimensioni totali di tutti gli snapshot di backup
* Il numero di macchine virtuali abilitate per il backup

È possibile gestire i backup usando il dashboard ed eseguendo il drill-down alle singole macchine virtuali. Il backup di una macchina virtuale inizia con l'apertura dell'insieme di credenziali nel dashboard. 

![Visualizzazione completa con il dispositivo di scorrimento](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Visualizzare le macchine virtuali nel dashboard

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Sfoglia** dal menu Hub e digitare **Servizi di ripristino** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insieme di credenziali dei servizi di ripristino**. 
    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Per semplicità, fare clic con il pulsante destro del mouse sull'insieme di credenziali nell'elenco e scegliere **Aggiungi al dashboard**.
3. Aprire il dashboard dell'insieme di credenziali. 
    ![Aprire il dashboard dell'insieme di credenziali e il pannello Impostazioni](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. Nel riquadro **Elementi di backup** fare clic su **Macchine virtuali di Azure**.

    ![Aprire il riquadro Elementi di backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. Nel pannello **Macchine virtuali di Azure** è indicato l'ultimo processo di backup per ogni elemento. In questo esempio è presente una sola macchina virtuale, demovm-markgal, protetta da questo insieme di credenziali.  

    ![Riquadro Elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. Nel dashboard dell'elemento dell'insieme di credenziali è possibile creare o modificare i criteri di backup, visualizzare i punti di ripristino, eseguire un backup su richiesta, arrestare e riprendere la protezione delle macchine virtuali, eliminare i punti di ripristino ed eseguire un ripristino.

    ![Dashboard degli elementi di backup con il pannello Impostazioni](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Gestire i criteri di backup
1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard) fare clic su **Tutte le impostazioni**.

    ![Pannello Criteri di backup](./media/backup-azure-manage-vms/all-settings-button.png)
2. In **Impostazioni** fare clic su **Criteri di backup**.
3. Dal menu **Scegliere i criteri di backup** :

   * Per modificare i criteri, selezionare criteri diversi e fare clic su **Salva**. I nuovi criteri verranno applicati immediatamente all'insieme di credenziali.
   * Per creare i criteri, selezionare **Crea nuovo**. [Altre informazioni](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Backup di una macchina virtuale](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta
È possibile eseguire un backup su richiesta di una macchina virtuale dopo averla configurata per la protezione.
- Se il backup iniziale è in sospeso, il backup su richiesta crea una copia completa della macchina virtuale nell'insieme di credenziali dei servizi di ripristino.
- Se il backup iniziale è stato completato, un backup su richiesta invierà all'insieme di credenziali dei servizi di ripristino solo le modifiche rispetto allo snapshot precedente. I backup successivi sono sempre incrementali.
- L'intervallo di conservazione di un backup su richiesta è il valore di conservazione specificato nei criteri per il punto di backup giornaliero. Se il punto di backup giornaliero non è selezionato, verrà usato quello settimanale.


Per attivare un backup su richiesta:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard)fare clic su **Esegui backup ora**.

    ![Pulsante Esegui backup ora](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Per avviarlo, fare clic su **Sì** .

    ![Pulsante Esegui backup ora](./media/backup-azure-manage-vms/backup-now-check.png)

 
 Il processo di backup crea un punto di ripristino. L'intervallo di conservazione del punto di ripristino è uguale a quello specificato nei criteri associati alla macchina virtuale. Per tenere traccia dello stato di avanzamento del processo, nel dashboard dell'insieme di credenziali fare clic sul riquadro **Processi di backup** .  

## <a name="stop-protecting-a-vm"></a>Interrompere la protezione di una macchina virtuale

Per interrompere la protezione delle macchine virtuali è possibile procedere in due modi:

- Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero. In questo caso non sarà possibile ripristinare la macchina virtuale.
- Interrompere tutti i processi di backup futuri mantenendo però i punti di ripristino. Al mantenimento dei punti di ripristino nell'archivio è associato un costo. Il vantaggio offerto dalla conservazione dei punti di ripristino è tuttavia la possibilità di ripristinare la macchina virtuale se necessario. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup/) sui dettagli prezzi.

Per arrestare la protezione per una macchina virtuale:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard)fare clic su **Interrompi backup**.

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png
2. Scegliere se mantenere o eliminare i dati di backup e confermare se necessario. Confermare come richiesto e, facoltativamente, specificare un commento. Se non si è certi del nome dell'elemento, passare il puntatore sul punto esclamativo per visualizzarlo.

    ![Arresta protezione](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Un messaggio di notifica informa che i processi di backup sono stati arrestati.


## <a name="resume-protection-of-a-vm"></a>Riprendere la protezione di una macchina virtuale

Se i dati di backup sono stati conservati quando la macchina virtuale è stata arrestata, è possibile riprendere la protezione, cosa che non è invece possibile se i dati di backup sono stati eliminati.

Te

1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard)fare clic su **Riprendi backup**.

2. Per assegnare i criteri della macchina virtuale, seguire la procedura descritta in [Gestire i criteri di backup](backup-azure-manage-vms.md#manage-backup-policies). È possibile scegliere un criterio diverso rispetto ai criteri con cui la macchina virtuale è stata protetta inizialmente.
3. Dopo l'applicazione dei criteri di backup alla macchina virtuale, viene visualizzato il messaggio seguente.

    ![Macchina virtuale protetta correttamente](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminare i dati di backup

È possibile eliminare i dati di backup associati a una macchina virtuale durante il processo **Interrompi backup** o in qualsiasi momento dopo il completamento del backup.

- Attendere settimane o mesi prima di eliminare i punti di ripristino potrebbe anche essere utile.
- A differenza del recupero dei punti di ripristino, quando si eliminano i dati di backup, non è possibile scegliere di eliminare punti di ripristino specifici. Se si sceglie di eliminare i dati di backup, vengono eliminati tutti i punti di ripristino associati all'elemento.

Questa procedura presuppone che il processo di backup per la macchina virtuale sia stato arrestato o disabilitato.


1. Nel [dashboard dell'elemento dell'insieme di credenziali](backup-azure-manage-vms.md#open-a-vault-item-dashboard)fare clic su **Elimina dati di backup**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. È necessario digitare il nome dell'elemento per confermare l'eliminazione dei punti di ripristino.

    ![Arresta verifica](./media/backup-azure-manage-vms/item-verification-box.png)

4. Per eliminare i dati di backup per l'elemento corrente, fare clic sul pulsante ![Interrompi backup](./media/backup-azure-manage-vms/delete-button.png)

    Un messaggio di notifica informa che i dati di backup sono stati eliminati.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni](backup-azure-vms-first-look-arm.md) sul backup di macchine virtuali di Azure dalle impostazioni delle macchine virtuali.
- [Informazioni](backup-azure-arm-restore-vms.md) sul ripristino di macchine virtuali. 
- [Informazioni](backup-azure-monitor-vms.md) sul monitoraggio dei backup di macchine virtuali di Azure.
 
