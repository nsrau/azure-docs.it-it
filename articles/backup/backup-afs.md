---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare il portale di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali dei servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938060"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Eseguire il backup delle condivisioni file di Azure in un insieme di credenziali di Servizi di ripristinoBack up Azure file shares in a Recovery Services vault

Questo articolo illustra come usare il portale di Azure per eseguire il backup delle [condivisioni file](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)di Azure.This article explains how to use the Azure portal to back up Azure file shares .

In questo articolo si apprenderà come:

* Creare un insieme di credenziali dei servizi di ripristino.
* Individuare le condivisioni file e configurare i backup.
* Eseguire un processo di backup su richiesta per creare un punto di ripristino.

## <a name="prerequisites"></a>Prerequisiti

* Identificare o creare un insieme di [credenziali di Servizi di ripristino](#create-a-recovery-services-vault) nella stessa area dell'account di archiviazione che ospita la condivisione file.
* Verificare che la condivisione file sia presente in uno dei [tipi di account di archiviazione supportati.](#limitations-for-azure-file-share-backup-during-preview)

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitazioni per il backup delle condivisioni file di Azure durante l'anteprima

Il backup per le condivisioni file di Azure è disponibile in anteprima. Le condivisioni file di Azure sono supportate negli account di archiviazione per utilizzo generico sia v1 che v2. Ecco le limitazioni per il backup delle condivisioni file di Azure:Here are the limitations for backing up Azure file shares:

* Il supporto per il backup delle condivisioni file di Azure negli account di archiviazione con la replica di [archiviazione con ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) di zona è attualmente limitato a [queste aree.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)
* Backup di Azure supporta attualmente la configurazione di backup pianificati una volta al giorno delle condivisioni file di Azure.Azure Backup currently supports configuring scheduled-daily backups of Azure file shares.
* Il numero massimo di backup pianificati al giorno è uno.
* Il numero massimo di backup su richiesta al giorno è quattro.
* Usare [i blocchi delle risorse](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) nell'account di archiviazione per impedire l'eliminazione accidentale dei backup nell'insieme di credenziali di Servizi di ripristino.
* Non eliminare gli snapshot creati da Backup di Azure.Don't delete snapshots created by Azure Backup. L'eliminazione di snapshot può comportare la perdita di punti di ripristino o errori di ripristino.
* Non eliminare condivisioni file protette da Backup di Azure.Don't delete file shares that are protected by Azure Backup. La soluzione corrente elimina tutti gli snapshot acquisiti da Backup di Azure dopo l'eliminazione della condivisione file, pertanto tutti i punti di ripristino andranno persi.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazioneModify storage replication

Per impostazione predefinita, gli insiemi di credenziali utilizzano [l'archiviazione con ridondanza geografica (GRS, Geoperidere)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di utilizzare GRS.
* È possibile utilizzare [l'archiviazione con ridondanza locale (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) come opzione a basso costo.

Per modificare il tipo di replica di archiviazione:

1. Nel nuovo vault, selezionare **Proprietà** nella sezione **Impostazioni.**

1. Nella pagina **Proprietà,** in **Configurazione backup,** selezionare **Aggiorna.**

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

    ![Aggiorna configurazione backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo l'impostazione dell'insieme di credenziali e contiene elementi di backup. Se si desidera eseguire questa operazione, è necessario ricreare il vault.
>

## <a name="discover-file-shares-and-configure-backup"></a>Individuare le condivisioni file e configurare il backup

1. Nel [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di Servizi di ripristino che si vuole usare per eseguire il backup della condivisione file.

1. Nel dashboard dell'insieme di credenziali dei servizi di **ripristino,** selezionare **Backup**.

   ![Insieme di credenziali dei servizi di ripristino](./media/backup-afs/recovery-services-vault.png)

    a. In **Obiettivo di backup**impostare Dove è in esecuzione il carico di lavoro **su** **Azure**.

    ![Scegliere Condivisione file di Azure come obiettivo di backupChoose Azure File Share as backup goal](./media/backup-afs/backup-goal.png)

    b.  In **Cosa si vuole eseguire il backup?** selezionare **Condivisione file** di Azure dall'elenco a discesa.

    c.  Selezionare **Backup** per registrare l'estensione di condivisione file di Azure nel vault.

    ![Selezionare Backup per associare la condivisione file di Azure al vault](./media/backup-afs/register-extension.png)

1. Dopo aver selezionato **Backup**, viene visualizzato il riquadro **Backup** in cui viene richiesto di selezionare un account di archiviazione da un elenco di account di archiviazione supportati individuati. Sono associati a questo insieme di credenziali o sono presenti nella stessa area dell'insieme di credenziali, ma non ancora associati ad alcun insieme di credenziali di Servizi di ripristino.

   ![Selezionare l'account di archiviazione](./media/backup-afs/select-storage-account.png)

1. Nell'elenco degli account di archiviazione individuati selezionare un account e quindi **scegliere OK**. Azure cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non sono visualizzate nell'elenco, attendere un po' di tempo per la visualizzazione delle condivisioni file.

    ![Individuazione delle condivisioni file](./media/backup-afs/discovering-file-shares.png)

1. Nell'elenco **Condivisioni file** selezionare una o più condivisioni file di cui si desidera eseguire il backup. Selezionare **OK**.

1. Dopo aver scelto le condivisioni file, il menu **Backup** passa a **Criterio di backup**. Da questo menu, selezionare un criterio di backup esistente o crearne uno nuovo. Selezionare **quindi Abilita backup**.

    ![Selezionare il criterio di backup](./media/backup-afs/select-backup-policy.png)

Dopo aver impostato un criterio di backup, viene creato uno snapshot delle condivisioni file all'ora pianificata. Il punto di ripristino viene mantenuto anche per il periodo scelto.

## <a name="create-an-on-demand-backup"></a>Creare un backup su richiesta

In alcuni casi, potrebbe essere necessario generare uno snapshot di backup, o punto di ripristino, al di fuori degli orari pianificati nei criteri di backup. Un motivo comune per generare un backup su richiesta è subito dopo aver configurato il criterio di backup. In base alla pianificazione nei criteri di backup, potrebbero essere previste ore o giorni prima che venga creato uno snapshot. Per proteggere i dati fino a quando non si attiva il criterio di backup, avviare un backup su richiesta. La creazione di un backup su richiesta è spesso necessaria prima di apportare modifiche pianificate alle condivisioni file.

### <a name="create-a-backup-job-on-demand"></a>Creare un processo di backup su richiestaCreate a backup job on demand

1. Aprire il vault Servizi di ripristino utilizzato per eseguire il backup della condivisione file. Nel riquadro **Panoramica** selezionare **Elementi di backup** nella sezione Elementi **protetti.**

   ![Selezionare Elementi di backup](./media/backup-afs/backup-items.png)

1. Dopo aver selezionato Elementi di **backup**, accanto al riquadro **Panoramica** viene visualizzato un nuovo riquadro in cui sono elencati tutti i **tipi di gestione del backup.**

   ![Elenco dei tipi di gestione dei backup](./media/backup-afs/backup-management-types.png)

1. Nell'elenco **Tipo di gestione backup** selezionare Archiviazione di Azure (file di **Azure).** Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup usando questo insieme di credenziali.

   ![Elementi di backup di Archiviazione di Azure (File di Azure)Azure Storage (Azure Files) backup items](./media/backup-afs/azure-files-backup-items.png)

1. Nell'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Vengono visualizzati i dettagli **dell'elemento di backup.** Nel menu Elemento di **backup** selezionare **Backup ora**. Poiché questo processo di backup è su richiesta, non esiste alcun criterio di conservazione associato al punto di ripristino.

   ![Seleziona Backup ora](./media/backup-afs/backup-now.png)

1. Viene visualizzato il riquadro **Backup now (Backup Now).** Specificare l'ultimo giorno di conservazione del punto di ripristino. È possibile avere una conservazione massima di 10 anni per un backup su richiesta.

   ![Scegli la data di conservazione](./media/backup-afs/retention-date.png)

1. Selezionare **OK** per confermare il processo di backup su richiesta in esecuzione.

1. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali. Selezionare **Processi** > di backup**in corso**.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:
* [Ripristinare le condivisioni file di AzureRestore Azure file shares](restore-afs.md)
* [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
