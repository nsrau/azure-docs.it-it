---
title: Gestire e monitorare i backup delle macchine virtuali di AzureManage and monitor Azure VM backups
description: Informazioni su come gestire e monitorare i backup delle macchine virtuali di Azure usando il servizio Backup di Azure.Learn how to manage and monitor Azure VM backups by using the Azure Backup service.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248151"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Gestire i backup delle macchine virtuali di Azure con il servizio Backup di AzureManage Azure VM backups with Azure Backup service

In questo articolo viene descritto come gestire le macchine virtuali di Azure di cui viene eseguito il backup tramite il servizio Backup di [Azure.](backup-overview.md) L'articolo riepiloga anche le informazioni di backup che è possibile trovare nel dashboard dell'insieme di credenziali.

Nel portale di Azure, il dashboard dell'insieme di credenziali di Servizi di ripristino consente di accedere alle informazioni sull'insieme di credenziali, tra cui:In the Azure portal, the Recovery Services vault dashboard provides access to vault information, including:

* Il backup più recente, che corrisponde anche al punto di ripristino più recente
* Criterio di backup.
* Dimensioni totali di tutti gli snapshot di backup.
* Numero di macchine virtuali abilitate per i backup.

È possibile gestire i backup usando il dashboard ed eseguendo il drill-down alle singole macchine virtuali. Per avviare i backup della macchina, aprire il vault sul cruscotto.

![Visualizzazione dashboard completa con dispositivo di scorrimento](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Visualizzare le macchine virtuali nel dashboardView VMs on the dashboard

Per visualizzare le macchine virtuali nel dashboard dell'insieme di credenziali:

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Nel menu Hub, selezionare **Sfoglia**. Nell'elenco di risorse digitare **servizi di ripristino**. Durante la digitazione, l'elenco viene filtrato in base all'input. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali di Servizi di ripristino](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Per facilitare l'utilizzo, fare clic con il pulsante destro del mouse sul vault e selezionare **Aggiungi al dashboard**.
4. Aprire il dashboard dell'insieme di credenziali.

    ![Aprire il dashboard del vault e il riquadro Impostazioni](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Nel riquadro **Elementi di backup** selezionare Macchine virtuali di **Azure**.

    ![Aprire il riquadro Elementi di backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Nel riquadro **Elementi di backup** è possibile visualizzare l'elenco delle macchine virtuali protette. In questo esempio, l'insieme di credenziali protegge una macchina virtuale: demobackup.  

    ![Visualizzare il riquadro Elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Dal dashboard dell'elemento dell'insieme di credenziali, modificare i criteri di backup, eseguire un backup su richiesta, arrestare o riprendere la protezione delle macchine virtuali, eliminare i dati di backup, visualizzare i punti di ripristino ed eseguire un ripristino.

    ![Il dashboard Elementi di backup e il riquadro Impostazioni](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gestire i criteri di backup per una macchina virtualeManage backup policy for a VM

Per gestire un criterio di backup:

1. Accedere al [portale](https://portal.azure.com/)di Azure . Aprire il dashboard dell'insieme di credenziali.
2. Nel riquadro **Elementi di backup** selezionare Macchine virtuali di **Azure**.

    ![Aprire il riquadro Elementi di backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Nel riquadro **Elementi di backup** è possibile visualizzare l'elenco delle macchine virtuali protette e lo stato dell'ultimo backup con l'ora dei punti di ripristino più recenti.

    ![Visualizzare il riquadro Elementi di backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Dal dashboard dell'elemento dell'insieme di credenziali, è possibile selezionare un criterio di backup.

   * Per cambiare criterio, selezionare un criterio diverso e quindi selezionare **Salva**. Il nuovo criterio verrà immediatamente applicato all'insieme di credenziali.

     ![Scegliere i criteri di backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire un backup su richiesta di una macchina virtuale dopo averne configurato la protezione. Tieni presente questi dettagli:

* Se il backup iniziale è in sospeso, il backup su richiesta crea una copia completa della macchina virtuale nell'insieme di credenziali di Servizi di ripristino.
* Al termine del backup iniziale, un backup su richiesta invierà solo le modifiche dello snapshot precedente all'insieme di credenziali di Servizi di ripristino. Ovvero, i backup successivi sono sempre incrementali.
* L'intervallo di conservazione per un backup su richiesta è il valore di conservazione specificato quando si attiva il backup.

Per attivare un backup su richiesta:

1. Nel [dashboard dell'elemento del vault,](#view-vms-on-the-dashboard)in **Elemento protetto,** selezionare **Elemento di backup.**

    ![Opzione Esegui backup ora](./media/backup-azure-manage-vms/backup-now-button.png)

2. In **Tipo di gestione backup**selezionare Macchina virtuale di **Azure**. Verrà visualizzato il riquadro **Elemento di backup (macchina virtuale di Azure).**
3. Selezionare una macchina virtuale e selezionare **Esegui backup** ora per creare un backup su richiesta. Viene visualizzato il riquadro **Backup ora.**
4. Nel campo **Mantieni fino al** backup specificare una data per la conservazione del backup.

    ![Il calendario Backup ora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selezionare **OK** per eseguire il processo di backup.

Per tenere traccia dello stato di avanzamento del processo, nel dashboard dell'insieme di credenziali selezionare il riquadro Processi di **backup.**

## <a name="stop-protecting-a-vm"></a>Interrompere la protezione di una macchina virtuale

Esistono due modi per interrompere la protezione di una macchina virtuale:There are two ways to stop protecting a VM:

* **Arrestare la protezione e conservare i dati**di backup . Questa opzione impedirà a tutti i processi di backup futuri di proteggere la macchina virtuale. Tuttavia, il servizio Backup di Azure manterrà i punti di ripristino di cui è stato eseguito il backup.  È necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali (per informazioni dettagliate, vedere Prezzi di Backup di [Azure).](https://azure.microsoft.com/pricing/details/backup/) Sarà possibile ripristinare la macchina virtuale, se necessario. Se si decide di riprendere la protezione della macchina virtuale, è possibile usare l'opzione *Riprendi backup.*
* **Interrompere la protezione ed eliminare i dati di backup**. Questa opzione impedirà a tutti i processi di backup futuri di proteggere la macchina virtuale ed eliminare tutti i punti di ripristino. Non sarà possibile ripristinare la macchina virtuale né usare l'opzione *di backup Riprendi.*

>[!NOTE]
>Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo. I punti di ripristino precedenti scadranno in base ai criteri, ma un ultimo punto di ripristino verrà sempre mantenuto fino a quando non si interrompono i backup e si eliminano i dati.
>

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione e conservare i dati di backup

Per arrestare la protezione e conservare i dati di una macchina virtuale:To stop protection and retain data of a VM:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard)selezionare **Interrompi backup**.
2. Scegliere **Mantieni dati di backup**e confermare la selezione in base alle esigenze. Se lo si desidera, aggiungere un commento. Se non si è sicuri del nome dell'elemento, passare il puntatore del mouse sul punto esclamativo per visualizzare il nome.

    ![Mantieni dati di backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Una notifica indica che i processi di backup sono stati interrotti.

### <a name="stop-protection-and-delete-backup-data"></a>Interrompere la protezione ed eliminare i dati di backup

Per arrestare la protezione ed eliminare i dati di una macchina virtuale:To stop protection and delete data of a VM:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard)selezionare **Interrompi backup**.
2. Scegliere **Elimina dati di backup**e confermare la selezione in base alle esigenze. Se lo si desidera, immettere il nome dell'elemento di backup e aggiungere un commento.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Riprendere la protezione di una macchina virtuale

Se si sceglie l'opzione [Arresta protezione e mantieni dati](#stop-protection-and-retain-backup-data) di backup durante l'arresto della protezione delle macchine virtuali, è possibile usare l'opzione **Riprendi backup**. Questa opzione non è disponibile se si sceglie l'opzione [Arresta protezione ed elimina dati di backup](#stop-protection-and-delete-backup-data) o Elimina dati di [backup](#delete-backup-data).

Per riprendere la protezione per una macchina virtuale:To resume protection for a VM:

1. Nel [dashboard dell'elemento dell'insieme di credenziali](#view-vms-on-the-dashboard)selezionare **Riprendi backup**.

2. Seguire i passaggi descritti in Gestire i [criteri di backup](#manage-backup-policy-for-a-vm) per assegnare i criteri per la macchina virtuale. Non è necessario scegliere i criteri di protezione iniziali della macchina virtuale.
3. Dopo aver applicato il criterio di backup alla macchina virtuale, viene visualizzato il messaggio seguente:

    ![Messaggio che indica una macchina virtuale protetta correttamenteMessage indicating a successfully protected VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

Esistono due modi per eliminare i dati di backup di una macchina virtuale:There are two ways to delete a VM's backup data:

* Dal dashboard dell'elemento del vault, selezionare Interrompi backup e seguire le istruzioni per l'opzione [Interrompi protezione ed elimina dati di backup.](#stop-protection-and-delete-backup-data)

  ![Selezionare Interrompi backup](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Dal dashboard dell'elemento del vault, selezionare Elimina dati di backup. Questa opzione è abilitata se si è scelto di [arrestare](#stop-protection-and-retain-backup-data) la protezione e mantenere l'opzione dei dati di backup durante l'arresto della protezione delle macchine virtuali

  ![Selezionare Elimina backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * Nel [dashboard degli elementi](#view-vms-on-the-dashboard)del vault, selezionare **Elimina dati di backup.**
  * Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica indica che i dati di backup sono stati eliminati.

Per proteggere i dati, Backup di Azure include la funzionalità di eliminazione temporanea. Con l'eliminazione temporanea, anche dopo l'eliminazione del backup (tutti i punti di ripristino) di una macchina virtuale, i dati di backup vengono conservati per altri 14 giorni. Per ulteriori informazioni, vedere [la documentazione relativa all'eliminazione temporanea](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > Quando si eliminano i dati di backup si eliminano tutti i punti di ripristino associati. Non è possibile scegliere punti di ripristino specifici da eliminare.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Elemento di backup in cui l'origine dati primaria non esiste più

* Se le macchine virtuali di Azure configurate per il backup di Azure vengono eliminate o spostate senza arrestare la protezione, i processi di backup pianificati e i processi di backup su richiesta (ad hoc) avranno esito negativo con l'errore UserErrorVmNotFoundV2. Il controllo preliminare del backup verrà visualizzato come critico solo per i processi di backup su richiesta non riusciti (non vengono visualizzati i processi pianificati non riusciti).
* Questi elementi di backup rimangono attivi nel sistema aderente ai criteri di backup e conservazione impostati dall'utente. I dati di cui è stato eseguito il backup per queste macchine virtuali di Azure verranno conservati in base ai criteri di conservazione. I punti di ripristino scaduti (ad eccezione dell'ultimo punto di ripristino) vengono puliti in base all'intervallo di conservazione impostato nei criteri di backup.
* Si consiglia agli utenti di eliminare gli elementi di backup in cui l'origine dati primaria non esiste più per evitare costi aggiuntivi, se l'elemento o i dati di backup per le risorse di eliminazione non sono più necessari in quanto l'ultimo punto di ripristino viene mantenuto per sempre e l'utente viene addebitato in base ai prezzi di backup applicabili.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il backup delle macchine virtuali di [Azure dalle impostazioni della macchina virtuale.](backup-azure-vms-first-look-arm.md)
* Informazioni su come ripristinare le [macchine virtuali.](backup-azure-arm-restore-vms.md)
* Informazioni su come [monitorare i backup delle macchine virtuali](backup-azure-monitor-vms.md)di Azure.
