---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare la portale di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938060"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Backup di condivisioni file di Azure in un insieme di credenziali di servizi di ripristino

Questo articolo illustra come usare la portale di Azure per eseguire il backup delle [condivisioni file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In questo articolo si apprenderà come:

* Creare un insieme di credenziali dei servizi di ripristino.
* Individuare le condivisioni file e configurare i backup.
* Eseguire un processo di backup su richiesta per creare un punto di ripristino.

## <a name="prerequisites"></a>Prerequisiti

* Identificare o creare un insieme di credenziali di [servizi di ripristino](#create-a-recovery-services-vault) nella stessa area dell'account di archiviazione che ospita la condivisione file.
* Verificare che la condivisione file sia presente in uno dei [tipi di account di archiviazione supportati](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitazioni per il backup delle condivisioni file di Azure durante l'anteprima

Il backup per le condivisioni file di Azure è disponibile in anteprima. Le condivisioni file di Azure sono supportate negli account di archiviazione per utilizzo generico sia v1 che v2. Ecco le limitazioni per il backup delle condivisioni file di Azure:

* Il supporto per il backup di condivisioni file di Azure negli account di archiviazione con replica di [archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) è attualmente limitato a [queste aree](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Backup di Azure supporta attualmente la configurazione di backup pianificati una volta al giorno per le condivisioni file di Azure.
* Il numero massimo di backup pianificati al giorno è uno.
* Il numero massimo di backup su richiesta al giorno è quattro.
* Usare i [blocchi delle risorse](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) nell'account di archiviazione per impedire l'eliminazione accidentale dei backup nell'insieme di credenziali di Servizi di ripristino.
* Non eliminare gli snapshot creati da backup di Azure. L'eliminazione degli snapshot può causare la perdita di punti di ripristino o errori di ripristino.
* Non eliminare le condivisioni file protette da backup di Azure. La soluzione corrente Elimina tutti gli snapshot eseguiti da backup di Azure dopo che la condivisione file è stata eliminata, pertanto tutti i punti di ripristino andranno perduti.

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

   ![Selezionare l'account di archiviazione](./media/backup-afs/select-storage-account.png)

1. Dall'elenco degli account di archiviazione individuati selezionare un account e fare clic su **OK**. Azure Cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non vengono visualizzate nell'elenco, attendere il tempo necessario per visualizzare le condivisioni file.

    ![Individuazione delle condivisioni file](./media/backup-afs/discovering-file-shares.png)

1. Dall'elenco **condivisioni file** selezionare una o più condivisioni file di cui si vuole eseguire il backup. Selezionare **OK**.

1. Dopo aver scelto le condivisioni file, il menu **backup** passa a **criteri di backup**. Da questo menu selezionare un criterio di backup esistente o crearne uno nuovo. Selezionare quindi **Abilita backup**.

    ![Selezionare il criterio di backup](./media/backup-afs/select-backup-policy.png)

Dopo aver impostato un criterio di backup, viene eseguito uno snapshot delle condivisioni file all'orario pianificato. Il punto di ripristino viene inoltre mantenuto per il periodo scelto.

## <a name="create-an-on-demand-backup"></a>Creare un backup su richiesta

Occasionalmente, potrebbe essere necessario generare uno snapshot di backup o un punto di ripristino, al di fuori delle ore pianificate nel criterio di backup. Un motivo comune per generare un backup su richiesta è subito dopo aver configurato i criteri di backup. In base alla pianificazione del criterio di backup, potrebbero essere presenti ore o giorni fino a quando non viene effettuato uno snapshot. Per proteggere i dati fino a quando non si attiva il criterio di backup, avviare un backup su richiesta. La creazione di un backup su richiesta è spesso necessaria prima di apportare modifiche pianificate alle condivisioni file.

### <a name="create-a-backup-job-on-demand"></a>Creazione di un processo di backup su richiesta

1. Aprire l'insieme di credenziali di servizi di ripristino usato per eseguire il backup della condivisione file. Nel riquadro **Panoramica** selezionare elementi di **backup** nella sezione **elementi protetti** .

   ![Seleziona elementi di backup](./media/backup-afs/backup-items.png)

1. Dopo aver selezionato **gli elementi di backup**, accanto al riquadro **Panoramica** verrà visualizzato un nuovo riquadro che elenca tutti i tipi di **gestione di backup** .

   ![Elenco dei tipi di gestione di backup](./media/backup-afs/backup-management-types.png)

1. Dall'elenco **tipo di gestione di backup** selezionare **archiviazione di Azure (file di Azure)** . Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup con questo insieme di credenziali.

   ![Elementi di backup di archiviazione di Azure (File di Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Verranno visualizzati i dettagli dell' **elemento di backup** . Dal menu **elemento di backup** selezionare **Esegui backup ora**. Poiché questo processo di backup è su richiesta, non è presente alcun criterio di conservazione associato al punto di ripristino.

   ![Selezionare Esegui backup ora](./media/backup-afs/backup-now.png)

1. Viene visualizzato il riquadro **Backup Now** . Specificare l'ultimo giorno di conservazione del punto di ripristino. Per un backup su richiesta, è possibile ottenere un periodo di conservazione massimo di 10 anni.

   ![Scegliere la data di conservazione](./media/backup-afs/retention-date.png)

1. Selezionare **OK** per confermare il processo di backup su richiesta che esegue.

1. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare lo stato di avanzamento del processo nel dashboard dell'insieme di credenziali. Selezionare i **processi di Backup** > **in corso**.

## <a name="next-steps"></a>Passaggi successivi

Scopri come:
* [Ripristinare le condivisioni file di Azure](restore-afs.md)
* [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
