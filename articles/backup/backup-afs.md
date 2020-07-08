---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare la portale di Azure per eseguire il backup delle condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 76bf8e00dede5f227cb862f9c9474844e349e298
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391155"
---
# <a name="back-up-azure-file-shares"></a>Eseguire il backup di condivisioni file di Azure

Questo articolo illustra come usare la portale di Azure per eseguire il backup delle [condivisioni file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In questo articolo si apprenderà come:

* Creare un insieme di credenziali dei servizi di ripristino.
* Individuare le condivisioni file e configurare i backup.
* Eseguire un processo di backup su richiesta per creare un punto di ripristino.

## <a name="prerequisites"></a>Prerequisiti

* Identificare o creare un insieme di credenziali di [servizi di ripristino](#create-a-recovery-services-vault) nella stessa area dell'account di archiviazione che ospita la condivisione file.
* Verificare che la condivisione file sia presente in uno dei [tipi di account di archiviazione supportati](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-file-shares-and-configure-backup"></a>Individuare le condivisioni file e configurare il backup

1. Nella [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di servizi di ripristino che si vuole usare per la configurazione del backup per la condivisione file.

1. Nel riquadro dell'insieme di credenziali di **servizi di ripristino** selezionare **+ backup** dal menu in alto.

   ![Insieme di credenziali dei servizi di ripristino](./media/backup-afs/recovery-services-vault.png)

    1. Nel riquadro **obiettivo del backup** impostare il percorso in **cui è in esecuzione il carico di lavoro?** in **Azure** selezionando l'opzione **Azure** dall'elenco a discesa.

          ![Scegliere Azure come carico di lavoro](./media/backup-afs/backup-goal.png)

    2. In **che cosa si vuole eseguire il backup?** selezionare **condivisione file di Azure** dall'elenco a discesa.

          ![Selezionare la condivisione file di Azure](./media/backup-afs/select-azure-file-share.png)

    3. Selezionare **backup** per registrare l'estensione di condivisione file di Azure nell'insieme di credenziali.

          ![Selezionare Backup per associare la condivisione file di Azure all'insieme di credenziali](./media/backup-afs/register-extension.png)

1. Dopo aver selezionato **backup**, viene aperto il riquadro **backup** . Per selezionare l'account di archiviazione che ospita la condivisione file che si vuole proteggere, fare clic sul testo del collegamento **selezionare** sotto la casella di testo **account di archiviazione** .

   ![Scegliere il collegamento Seleziona](./media/backup-afs/choose-select-link.png)

1. Viene visualizzato il **riquadro Seleziona account di archiviazione** a destra, che elenca un set di account di archiviazione supportati individuati. Sono associati a questo insieme di credenziali o presenti nella stessa area dell'insieme di credenziali, ma non sono ancora associati ad alcun insieme di credenziali di servizi di ripristino.

1. Dall'elenco degli account di archiviazione individuati selezionare un account e fare clic su **OK**.

   ![Consente di selezionare gli account di archiviazione individuati](./media/backup-afs/select-discovered-storage-account.png)

1. Il passaggio successivo consiste nel selezionare le condivisioni file di cui si vuole eseguire il backup. Fare clic sul pulsante **Aggiungi** nella sezione **fileshares to backup** .

   ![Selezionare le condivisioni file di cui eseguire il backup](./media/backup-afs/select-file-shares-to-back-up.png)

1. Si apre il riquadro del contesto **Seleziona condivisioni file** a destra. Azure Cerca nell'account di archiviazione le condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non vengono visualizzate nell'elenco, attendere il tempo necessario per visualizzare le condivisioni file.

1. Dall'elenco **Seleziona condivisioni file** selezionare una o più condivisioni file di cui si vuole eseguire il backup. Selezionare **OK**.

   ![Selezionare le condivisioni file](./media/backup-afs/select-file-shares.png)

1. Per scegliere un criterio di backup per la condivisione file, sono disponibili tre opzioni:

   * Scegliere i criteri predefiniti.<br>
   Questa opzione consente di abilitare il backup giornaliero che verrà mantenuto per 30 giorni. Se non si dispone di un criterio di backup esistente nell'insieme di credenziali, viene aperto il riquadro backup con le impostazioni dei criteri predefinite. Se si desidera scegliere le impostazioni predefinite, è possibile fare clic direttamente su **Abilita backup**.

   * Creare nuovi criteri <br>

      1. Per creare un nuovo criterio di backup per la condivisione file, fare clic sul testo del collegamento sotto l'elenco a discesa nella sezione **criteri di backup** .<br>

         ![Creare un nuovo criterio](./media/backup-afs/create-new-policy.png)

      1. Si apre il riquadro del contesto dei **criteri di backup** a destra. Specificare un nome per i criteri nella casella di testo e scegliere il periodo di memorizzazione in base alle esigenze. Per impostazione predefinita, è abilitata solo l'opzione di conservazione giornaliera. Se si desidera disporre di un periodo di conservazione settimanale, mensile o annuale, selezionare la casella di controllo corrispondente e specificare il valore di memorizzazione desiderato.

      1. Dopo aver specificato i valori di conservazione e un nome di criterio valido, fare clic su OK.<br>

         ![Assegnare il nome dei criteri e i valori di conservazione](./media/backup-afs/policy-name.png)

   * Scegliere uno dei criteri di backup esistenti <br>

   Per scegliere uno dei criteri di backup esistenti per la configurazione della protezione, selezionare i criteri desiderati dall'elenco a discesa **criteri di backup** .<br>

   ![Scegliere i criteri esistenti](./media/backup-afs/choose-existing-policy.png)

1. Fare clic su **Abilita backup** per avviare la protezione della condivisione file.

   ![Scegliere Abilita backup](./media/backup-afs/enable-backup.png)

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

1. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare lo stato di avanzamento del processo nel dashboard dell'insieme di credenziali. Selezionare i **processi**  >  **di backup in corso**.

>[!NOTE]
>Backup di Azure consente di bloccare l'account di archiviazione quando si configura la protezione per qualsiasi condivisione file nell'account corrispondente. In questo modo si garantisce la protezione dall'eliminazione accidentale di un account di archiviazione con condivisioni file sottoposte a backup.

## <a name="best-practices"></a>Procedure consigliate

* Non eliminare gli snapshot creati da backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.

* Non rimuovere il blocco effettuato nell'account di archiviazione da backup di Azure. Se si elimina il blocco, l'account di archiviazione sarà soggetto a un'eliminazione accidentale e se viene eliminato, si perderanno gli snapshot o i backup.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

* [Ripristinare le condivisioni file di Azure](restore-afs.md)
* [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
