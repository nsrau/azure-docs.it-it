---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare la portale di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294513"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Backup di condivisioni file di Azure in un insieme di credenziali di servizi di ripristino

Questo articolo illustra come usare la portale di Azure per eseguire il backup delle [condivisioni file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Questa guida illustra come eseguire queste operazioni:

* Creare un insieme di credenziali di Servizi di ripristino
* Individuare le condivisioni file e configurare i backup
* Eseguire un processo di backup su richiesta per creare un punto di ripristino

## <a name="prerequisites"></a>Prerequisiti

* Identificare o creare un insieme di credenziali di [servizi di ripristino](#create-a-recovery-services-vault) nella stessa area dell'account di archiviazione che ospita la condivisione file.

* Verificare che la condivisione file sia presente in uno dei [tipi di account di archiviazione supportati](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitazioni per il backup delle condivisioni file di Azure durante l'anteprima

Il backup per le condivisioni file di Azure è disponibile in anteprima. Le condivisioni file di Azure sono supportate negli account di archiviazione per utilizzo generico sia v1 che v2. Queste sono le limitazioni per il backup delle condivisioni file di Azure:

* Il supporto per il backup di condivisioni file di Azure negli account di archiviazione con la replica di [archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) è attualmente limitato a [queste aree](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Backup di Azure supporta attualmente la configurazione di backup pianificati una volta al giorno per le condivisioni file di Azure.
* Il numero massimo di backup pianificati al giorno è uno.
* Il numero massimo di backup su richiesta al giorno è quattro.
* Usare i [blocchi delle risorse](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) nell'account di archiviazione per impedire l'eliminazione accidentale dei backup nell'insieme di credenziali di Servizi di ripristino.
* Non eliminare gli snapshot creati da Backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.
* Non eliminare le condivisioni file protette mediante Backup di Azure. La soluzione corrente eliminerà tutti gli snapshot eseguiti da backup di Azure dopo l'eliminazione della condivisione file e quindi tutti i punti di ripristino andranno persi.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l' [archiviazione con ridondanza geografica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di usare GRS.

* È possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) come opzione a basso costo.

Modificare il tipo di replica di archiviazione come segue:

1. Nel nuovo insieme di credenziali fare clic su **Proprietà** nella sezione **Impostazioni** .

2. In **Proprietà**, in **configurazione backup**, fare clic su **Aggiorna**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

    ![Aggiorna configurazione backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario ricreare l'insieme di credenziali.
>

## <a name="discover-file-shares-and-configure-backup"></a>Individuare le condivisioni file e configurare il backup

1. Nella [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di servizi di ripristino che si vuole usare per eseguire il backup della condivisione file.

2. Nel dashboard dell'insieme di credenziali di **servizi di ripristino** selezionare **+ backup**.

   ![Insieme di credenziali dei servizi di ripristino](./media/backup-afs/recovery-services-vault.png)

   a. In **obiettivo di backup**impostare **il percorso in cui è in esecuzione il carico di lavoro?** in **Azure**.

    ![Scegliere la condivisione file di Azure come obiettivo del backup](./media/backup-afs/backup-goal.png)

    b.    In **elementi di cui si vuole**eseguire il backup selezionare **condivisione file di Azure** dal menu a discesa.

    c.    Fare clic su **backup** per registrare l'estensione di condivisione file di Azure nell'insieme di credenziali.

      ![Fare clic su backup per associare la condivisione file di Azure all'insieme di credenziali](./media/backup-afs/register-extension.png)

3. Quando si fa clic su **backup**, viene aperto il pannello backup con la richiesta di selezionare un account di archiviazione da un elenco di account di archiviazione supportati individuati. Sono associati a questo insieme di credenziali o presenti nella stessa area dell'insieme di credenziali, ma non sono ancora associati ad alcun insieme di credenziali di servizi di ripristino.

   ![Selezionare l'account di archiviazione](./media/backup-afs/select-storage-account.png)

4. Dall'elenco degli account di archiviazione individuati selezionare un account e fare clic su **OK**. Azure cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non vengono visualizzate nell'elenco, concedere tempo per visualizzare le condivisioni file.

    ![Individuazione delle condivisioni file](./media/backup-afs/discovering-file-shares.png)

5. Dall'elenco **Condivisioni file** selezionare una o più condivisioni file delle quali eseguire il backup e fare clic su **OK**.

6. Dopo aver scelto le condivisioni file, il menu **backup** passa a **criteri di backup**. Da questo menu selezionare un criterio di backup esistente o crearne uno nuovo, quindi fare clic su **Abilita backup**.

    ![Selezionare il criterio di backup](./media/backup-afs/select-backup-policy.png)

Dopo aver stabilito un criterio di backup verrà creato uno snapshot delle condivisioni file all'orario pianificato e il punto di ripristino verrà conservato per il periodo scelto.

## <a name="create-an-on-demand-backup"></a>Creare un backup su richiesta

In alcuni casi può essere necessario generare uno snapshot di backup o un punto di ripristino, al di fuori delle ore pianificate nel criterio di backup. Un motivo comune per generare un backup su richiesta è subito dopo aver configurato i criteri di backup. A seconda della pianificazione indicata nel criterio di backup, possono volerci ore o giorni prima che venga creato uno snapshot. Per proteggere i dati fino a quando non si attiva il criterio di backup, avviare un backup su richiesta. La creazione di un backup su richiesta è spesso necessaria prima di apportare modifiche pianificate alle condivisioni file.

### <a name="to-create-an-on-demand-backup"></a>Per creare un backup su richiesta

1. Aprire l'insieme di credenziali di servizi di ripristino usato per eseguire il backup della condivisione file e fare clic su **elementi di backup** nella sezione **elementi protetti** del pannello **Panoramica** .

   ![Fare clic su elementi di backup](./media/backup-afs/backup-items.png)

2. Quando si fa clic su **elementi di backup**, viene visualizzato un nuovo pannello che elenca tutti i tipi di **gestione di backup** accanto al pannello **Panoramica** come indicato di seguito:

   ![Elenco dei tipi di gestione di backup](./media/backup-afs/backup-management-types.png)

3. Dall'elenco dei **tipi di gestione di backup**selezionare **archiviazione di Azure (file di Azure)** . Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup con questo insieme di credenziali.

   ![Elementi di backup di archiviazione di Azure (File di Azure)](./media/backup-afs/azure-files-backup-items.png)

4. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Verranno visualizzati i dettagli dell' **elemento di backup** . Nel menu **Elemento di backup** fare clic su **Esegui backup ora**. Trattandosi di un processo di backup su richiesta, non sono presenti criteri di conservazione associati al punto di ripristino.

   ![Fare clic su Esegui backup ora](./media/backup-afs/backup-now.png)

5. Si apre il pannello **Backup Now** . Specificare l'ultimo giorno di conservazione del punto di ripristino. Per un backup su richiesta, è possibile ottenere un periodo di conservazione massimo di 10 anni.

   ![Scegliere la data di conservazione](./media/backup-afs/retention-date.png)

6. Fare clic su **OK** per confermare l'esecuzione del processo di backup su richiesta.

7. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare le condivisioni file di Azure](restore-afs.md)

* Informazioni su come [gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
