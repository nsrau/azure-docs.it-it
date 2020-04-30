---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare la portale di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: da2c7fa4cc5c3b7b948604a6f6d3999671cb3697
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101311"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Backup di condivisioni file di Azure in un insieme di credenziali di servizi di ripristino

Questo articolo illustra come usare la portale di Azure per eseguire il backup delle [condivisioni file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In questo articolo si apprenderà come:

* Creare un insieme di credenziali dei servizi di ripristino.
* Individuare le condivisioni file e configurare i backup.
* Eseguire un processo di backup su richiesta per creare un punto di ripristino.

## <a name="prerequisites"></a>Prerequisiti

* Identificare o creare un insieme di credenziali di [servizi di ripristino](#create-a-recovery-services-vault) nella stessa area dell'account di archiviazione che ospita la condivisione file.
* Verificare che la condivisione file sia presente in uno dei [tipi di account di archiviazione supportati](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l' [archiviazione con ridondanza geografica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di usare GRS.
* È possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) come opzione a basso costo.

Per modificare il tipo di replica di archiviazione:

1. Nel nuovo insieme di credenziali selezionare **Proprietà** nella sezione **Impostazioni** .

1. Nella pagina **Proprietà** , in **configurazione backup**, selezionare **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

    ![Aggiorna configurazione backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario creare nuovamente l'insieme di credenziali.
>

## <a name="discover-file-shares-and-configure-backup"></a>Individuare le condivisioni file e configurare il backup

1. Nella [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di servizi di ripristino che si vuole usare per eseguire il backup della condivisione file.

1. Nel dashboard dell'insieme di credenziali di **servizi di ripristino** selezionare **+ backup**.

   ![Insieme di credenziali dei servizi di ripristino](./media/backup-afs/recovery-services-vault.png)

    a. In **obiettivo di backup**impostare **il percorso in cui è in esecuzione il carico di lavoro?** in **Azure**.

    ![Scegliere la condivisione file di Azure come obiettivo del backup](./media/backup-afs/backup-goal.png)

    b.  In **che cosa si vuole eseguire il backup?** selezionare **condivisione file di Azure** dall'elenco a discesa.

    c.  Selezionare **backup** per registrare l'estensione di condivisione file di Azure nell'insieme di credenziali.

    ![Selezionare Backup per associare la condivisione file di Azure all'insieme di credenziali](./media/backup-afs/register-extension.png)

1. Dopo aver selezionato **backup**, viene aperto il riquadro **backup** con la richiesta di selezionare un account di archiviazione da un elenco di account di archiviazione supportati individuati. Sono associati a questo insieme di credenziali o presenti nella stessa area dell'insieme di credenziali, ma non sono ancora associati ad alcun insieme di credenziali di servizi di ripristino.

1. Dall'elenco degli account di archiviazione individuati selezionare un account e fare clic su **OK**. Azure Cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non vengono visualizzate nell'elenco, attendere il tempo necessario per visualizzare le condivisioni file.

    ![Individuazione delle condivisioni file](./media/backup-afs/discovering-file-shares.png)

1. Dall'elenco **condivisioni file** selezionare una o più condivisioni file di cui si vuole eseguire il backup. Selezionare **OK**.

   ![Selezionare le condivisioni file](./media/backup-afs/select-file-shares.png)

1. Dopo aver scelto le condivisioni file, il menu **backup** passa a **criteri di backup**. Da questo menu selezionare un criterio di backup esistente o crearne uno nuovo. Selezionare quindi **Abilita backup**.

    ![Selezionare il criterio di backup](./media/backup-afs/select-backup-policy.png)

Dopo aver impostato un criterio di backup, viene eseguito uno snapshot delle condivisioni file all'orario pianificato. Il punto di ripristino viene inoltre mantenuto per il periodo scelto.

>[!NOTE]
>Backup di Azure supporta ora i criteri con conservazione giornaliera/settimanale/mensile/annuale per il backup della condivisione file di Azure.

## <a name="create-an-on-demand-backup"></a>Creare un backup su richiesta

Occasionalmente, potrebbe essere necessario generare uno snapshot di backup o un punto di ripristino, al di fuori delle ore pianificate nel criterio di backup. Un motivo comune per generare un backup su richiesta è subito dopo aver configurato i criteri di backup. In base alla pianificazione del criterio di backup, potrebbero essere presenti ore o giorni fino a quando non viene effettuato uno snapshot. Per proteggere i dati fino a quando non si attiva il criterio di backup, avviare un backup su richiesta. La creazione di un backup su richiesta è spesso necessaria prima di apportare modifiche pianificate alle condivisioni file.

### <a name="create-a-backup-job-on-demand"></a>Creazione di un processo di backup su richiesta

1. Aprire l'insieme di credenziali di servizi di ripristino usato per eseguire il backup della condivisione file. Nel riquadro **Panoramica** selezionare elementi di **backup** nella sezione **elementi protetti** .

   ![Seleziona elementi di backup](./media/backup-afs/backup-items.png)

1. Dopo aver selezionato **gli elementi di backup**, accanto al riquadro **Panoramica** verrà visualizzato un nuovo riquadro che elenca tutti i tipi di **gestione di backup** .

   ![Elenco dei tipi di gestione di backup](./media/backup-afs/backup-management-types.png)

1. Dall'elenco **tipo di gestione di backup** selezionare **archiviazione di Azure (file di Azure)**. Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup con questo insieme di credenziali.

   ![Elementi di backup di archiviazione di Azure (File di Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Verranno visualizzati i dettagli dell' **elemento di backup** . Dal menu **elemento di backup** selezionare **Esegui backup ora**. Poiché questo processo di backup è su richiesta, non è presente alcun criterio di conservazione associato al punto di ripristino.

   ![Selezionare Esegui backup ora](./media/backup-afs/backup-now.png)

1. Viene visualizzato il riquadro **Backup Now** . Specificare l'ultimo giorno di conservazione del punto di ripristino. Per un backup su richiesta, è possibile ottenere un periodo di conservazione massimo di 10 anni.

   ![Scegliere la data di conservazione](./media/backup-afs/retention-date.png)

1. Selezionare **OK** per confermare il processo di backup su richiesta che esegue.

1. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare lo stato di avanzamento del processo nel dashboard dell'insieme di credenziali. Selezionare i **processi** > **di backup in corso**.

>[!NOTE]
>Backup di Azure consente di bloccare l'account di archiviazione quando si configura la protezione per qualsiasi condivisione file nell'account corrispondente. In questo modo si garantisce la protezione dall'eliminazione accidentale di un account di archiviazione con condivisioni file sottoposte a backup.

## <a name="best-practices"></a>Procedure consigliate

* Non eliminare gli snapshot creati da backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.

* Non rimuovere il blocco effettuato nell'account di archiviazione da backup di Azure. Se si elimina il blocco, l'account di archiviazione sarà soggetto a un'eliminazione accidentale e se viene eliminato, si perderanno gli snapshot o i backup.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

* [Ripristinare le condivisioni file di Azure](restore-afs.md)
* [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
