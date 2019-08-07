---
title: Gestire e monitorare i backup delle macchine virtuali di Azure usando il servizio backup di Azure
description: Informazioni su come gestire e monitorare i backup delle macchine virtuali di Azure usando il servizio backup di Azure.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dacurwin
ms.openlocfilehash: 025052a5005702232eb9eaf255e6aac2c58fec98
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68735579"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Gestire i backup delle macchine virtuali di Azure con il servizio backup di Azure

Questo articolo descrive come gestire le macchine virtuali di Azure di cui viene eseguito il backup con il [servizio backup di Azure](backup-overview.md). Questo articolo riepiloga anche le informazioni di backup che è possibile trovare nel dashboard dell'insieme di credenziali.


Nel portale di Azure il dashboard dell'insieme di credenziali dei servizi di ripristino fornisce l'accesso alle informazioni sull'insieme di credenziali, tra cui:

* Il backup più recente, che corrisponde anche al punto di ripristino più recente
* Criteri di backup.
* Dimensioni totali di tutti gli snapshot di backup.
* Il numero di macchine virtuali abilitate per i backup.

È possibile gestire i backup usando il dashboard ed eseguendo il drill-down nelle singole macchine virtuali. Per avviare i backup del computer, aprire l'insieme di credenziali nel dashboard.

![Visualizzazione dashboard completa con dispositivo di scorrimento](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Visualizzare le macchine virtuali nel dashboard

Per visualizzare le macchine virtuali nel dashboard dell'insieme di credenziali:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Sfoglia**dal menu hub. Nell'elenco di risorse digitare **servizi di ripristino**. Durante la digitazione, l'elenco viene filtrato in base all'input. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali di Servizi di ripristino](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Per semplicità d'uso, fare clic con il pulsante destro del mouse sull'insieme di credenziali e selezionare **Aggiungi al dashboard**.
4. Aprire il dashboard dell'insieme di credenziali.

    ![Aprire il dashboard dell'insieme di credenziali e le impostazioni](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Nel riquadro **elementi di backup** selezionare **macchine virtuali di Azure**.

    ![Aprire il riquadro elementi di backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Nel pannello **elementi** di backup è possibile visualizzare l'elenco delle macchine virtuali protette. In questo esempio, l'insieme di credenziali protegge una macchina virtuale: demobackup.  

    ![Visualizzare il pannello elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Dal dashboard dell'elemento dell'insieme di credenziali modificare i criteri di backup, eseguire un backup su richiesta, arrestare o riprendere la protezione delle macchine virtuali, eliminare i dati di backup, visualizzare i punti di ripristino ed eseguire un ripristino.

    ![Il dashboard degli elementi di backup e il pannello impostazioni](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gestire i criteri di backup per una macchina virtuale

Per gestire i criteri di backup:

1. Accedere al [portale di Azure](https://portal.azure.com/). Aprire il dashboard dell'insieme di credenziali.
2. Nel riquadro **elementi di backup** selezionare **macchine virtuali di Azure**.

    ![Aprire il riquadro elementi di backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Nel pannello **elementi** di backup è possibile visualizzare l'elenco delle macchine virtuali protette e l'ultimo stato del backup con i punti di ripristino più recenti.

    ![Visualizzare il pannello elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Dal dashboard dell'elemento dell'insieme di credenziali è possibile selezionare un criterio di backup.

   * Per modificare i criteri, selezionare un criterio diverso e quindi fare clic su **Salva**. Il nuovo criterio verrà immediatamente applicato all'insieme di credenziali.

     ![Scegliere i criteri di backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta
È possibile eseguire un backup su richiesta di una macchina virtuale dopo aver impostato la protezione. Tenere presente i seguenti dettagli:

- Se il backup iniziale è in sospeso, il backup su richiesta crea una copia completa della macchina virtuale nell'insieme di credenziali di servizi di ripristino.
- Se il backup iniziale è completo, un backup su richiesta invierà solo le modifiche dallo snapshot precedente all'insieme di credenziali dei servizi di ripristino. Ovvero, i backup successivi sono sempre incrementali.
- Il periodo di mantenimento dati per un backup su richiesta è il valore di conservazione specificato quando si attiva il backup.

Per attivare un backup su richiesta:

1. Nel [Dashboard dell'elemento](#view-vms-on-the-dashboard)dell'insieme di credenziali, in **elemento protetto**selezionare **elemento di backup**.

    ![Opzione Backup Now](./media/backup-azure-manage-vms/backup-now-button.png)

2. Da **Backup Management Type**selezionare **macchina virtuale di Azure**. Viene visualizzato il pannello **elemento di backup (macchina virtuale di Azure)** .
3. Selezionare una macchina virtuale e selezionare **Esegui backup adesso** per creare un backup su richiesta. Viene visualizzato il pannello **Backup Now** .
4. Nel campo **Mantieni backup fino** a specificare una data per il backup da conservare.

    ![Calendario Backup Now](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selezionare **OK** per eseguire il processo di backup.

Per tenere traccia dello stato del processo, nel dashboard dell'insieme di credenziali selezionare il riquadro **processi di backup** .

## <a name="stop-protecting-a-vm"></a>Interrompere la protezione di una macchina virtuale

Esistono due modi per arrestare la protezione di una macchina virtuale:

* **Arrestare la protezione e conservare i dati di backup**. Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione della macchina virtuale. Tuttavia, il servizio backup di Azure manterrà i punti di ripristino di cui è stato eseguito il backup.  È necessario pagare per i punti di ripristino nell'insieme di credenziali. per informazioni dettagliate, vedere [prezzi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/) . Se necessario, sarà possibile ripristinare la macchina virtuale. Se si decide di riprendere la protezione della macchina virtuale, è possibile usare l'opzione *Riprendi backup* .
* **Arrestare la protezione ed eliminare i dati di backup**. Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione della macchina virtuale ed eliminare tutti i punti di ripristino. Non sarà possibile ripristinare la macchina virtuale né usare l'opzione *Riprendi backup* .

>[!NOTE]
>Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo. I punti di ripristino precedenti scadranno in base ai criteri, ma un ultimo punto di ripristino verrà sempre mantenuto fino a quando non si arrestano i backup ed eliminano i dati.
>

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione e conservare i dati di backup

Per arrestare la protezione e conservare i dati di una macchina virtuale:

1. Nel [Dashboard dell'elemento](#view-vms-on-the-dashboard)dell'insieme di credenziali selezionare **Arresta backup**.
2. Scegliere **Mantieni dati di backup**e confermare la selezione in base alle esigenze. Se lo si desidera, aggiungere un commento. Se non si è certi del nome dell'elemento, passare il puntatore del mouse sul punto esclamativo per visualizzare il nome.

    ![Mantieni dati di backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Una notifica informa che i processi di backup sono stati arrestati.

### <a name="stop-protection-and-delete-backup-data"></a>Arrestare la protezione ed eliminare i dati di backup

Per arrestare la protezione ed eliminare i dati di una macchina virtuale:

1. Nel [Dashboard dell'elemento](#view-vms-on-the-dashboard)dell'insieme di credenziali selezionare **Arresta backup**.
2. Scegliere **Elimina dati di backup**e confermare la selezione in base alle esigenze. Immettere il nome dell'elemento di backup e aggiungere un commento, se lo si desidera.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Riprendere la protezione di una macchina virtuale

Se è stata scelta l'opzione [Interrompi protezione e Mantieni dati di backup](#stop-protection-and-retain-backup-data) durante l'arresto della protezione della macchina virtuale, è possibile usare Riprendi **backup**. Questa opzione non è disponibile se si sceglie [Interrompi protezione ed Elimina dati di backup](#stop-protection-and-delete-backup-data) o [Elimina dati di backup](#delete-backup-data).

Per riprendere la protezione per una macchina virtuale:

1. Nel [Dashboard dell'elemento](#view-vms-on-the-dashboard)dell'insieme di credenziali fare clic su Riprendi **backup**.

2. Seguire i passaggi in [gestire i criteri di backup](#manage-backup-policy-for-a-vm) per assegnare i criteri per la macchina virtuale. Non è necessario scegliere i criteri di protezione iniziale della macchina virtuale.
3. Dopo aver applicato i criteri di backup alla macchina virtuale, viene visualizzato il messaggio seguente:

    ![Messaggio che indica una macchina virtuale correttamente protetta](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

Esistono due modi per eliminare i dati di backup di una macchina virtuale:

- Nel dashboard dell'elemento dell'insieme di credenziali selezionare Interrompi backup e seguire le istruzioni per l'opzione [Interrompi protezione ed Elimina dati di backup](#stop-protection-and-delete-backup-data) .

  ![Selezionare Interrompi backup](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- Nel dashboard dell'elemento dell'insieme di credenziali selezionare Elimina dati di backup. Questa opzione è abilitata se si è scelto di [arrestare la protezione e di mantenere l'opzione backup data](#stop-protection-and-retain-backup-data) durante l'arresto della protezione delle macchine virtuali

  ![Selezionare Elimina backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - Nel [Dashboard dell'elemento](#view-vms-on-the-dashboard)dell'insieme di credenziali selezionare **Elimina dati di backup**.
  - Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica informa che i dati di backup sono stati eliminati.

  > [!NOTE]
  > Quando si eliminano i dati di backup, vengono eliminati tutti i punti di ripristino associati. Non è possibile scegliere punti di ripristino specifici da eliminare.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [eseguire il backup di macchine virtuali di Azure dalle impostazioni della macchina virtuale](backup-azure-vms-first-look-arm.md).
- Informazioni su come [ripristinare le macchine virtuali](backup-azure-arm-restore-vms.md).
- Informazioni su come [monitorare i backup delle macchine virtuali di Azure](backup-azure-monitor-vms.md).
