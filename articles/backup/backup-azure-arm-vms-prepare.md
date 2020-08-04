---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino
description: Informazioni su come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino tramite Backup di Azure
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 91fca2eef21a817c0f78b826e507901d94156dcd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533598"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino

Questo articolo descrive come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino tramite il servizio [Backup di Azure](backup-overview.md).

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Preparare le macchine virtuali di Azure.
> * Creare un insieme di credenziali.
> * Individuare le macchine virtuali e configurare un criterio di backup.
> * Abilitare il backup per le macchine virtuali di Azure.
> * Eseguire il backup iniziale.

> [!NOTE]
> Questo articolo descrive come configurare un insieme di credenziali e selezionare le macchine virtuali di cui eseguire il backup. È utile per eseguire il backup di più macchine virtuali. In alternativa, è possibile [eseguire il backup di una singola macchina virtuale di Azure](backup-azure-vms-first-look-arm.md) direttamente dalle impostazioni della macchina virtuale.

## <a name="before-you-start"></a>Prima di iniziare

* [Verificare](backup-architecture.md#architecture-built-in-azure-vm-backup) l'architettura del backup delle macchine virtuali di Azure.
* [Informazioni su](backup-azure-vms-introduction.md) backup delle macchine virtuali di Azure ed estensione di backup.
* [Rivedere la matrice di supporto](backup-support-matrix-iaas.md) prima di configurare il backup.

In alcune circostanze può anche essere necessario eseguire alcune operazioni:

* **Installare l'agente di macchine virtuali nella macchina virtuale**: Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina virtuale locale, può essere necessario [installare l'agente manualmente](#install-the-vm-agent).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md).

* Se l'insieme di credenziali rappresenta il meccanismo principale di backup, è consigliabile usare l'archiviazione con ridondanza geografica.
* Per un'opzione più economica, è possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](../storage/common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) .

Modificare il tipo di replica di archiviazione come segue:

1. Nel nuovo insieme di credenziali selezionare **Proprietà** nella sezione **Impostazioni** .
2. In **Proprietà**, in **configurazione backup**, selezionare **Aggiorna**.
3. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario ricreare l'insieme di credenziali.

## <a name="apply-a-backup-policy"></a>Applicare un criterio di backup

Configurare un criterio di backup per l'insieme di credenziali.

1. Nell'insieme di credenziali selezionare **+ backup** nella sezione **Panoramica** .

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. In **Obiettivo del backup** > **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale** >  **OK**. In questo modo viene registrata l'estensione delle macchine virtuali nell'insieme di credenziali.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali.
    * Il criterio predefinito esegue il backup della macchina virtuale una volta al giorno. I backup giornalieri vengono conservati per 30 giorni. Gli snapshot di ripristino istantaneo vengono conservati per due giorni.

      ![Criterio di backup predefinito](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Se non si vuole usare il criterio predefinito, selezionare **Crea nuovo** e creare un criterio personalizzato come descritto nella prossima procedura.

1. In **macchine virtuali**selezionare **Aggiungi**.

      ![Aggiungi macchine virtuali](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. Viene visualizzato il riquadro **Seleziona macchine virtuali** . Selezionare le macchine virtuali di cui si vuole eseguire il backup usando i criteri. Selezionare **OK**.

   * Le macchine virtuali selezionate vengono convalidate.
   * È possibile selezionare solo macchine virtuali presenti nella stessa area dell'insieme di credenziali.
   * Il backup delle macchine virtuali può essere eseguito in un solo insieme di credenziali.

     ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Tutte le macchine virtuali nella stessa area e nella stessa sottoscrizione dell'insieme di credenziali sono disponibili per la configurazione del backup. Quando si configura il backup, è possibile passare al nome della macchina virtuale e al relativo gruppo di risorse, anche se non si dispone dell'autorizzazione necessaria per tali macchine virtuali. Se la macchina virtuale è in stato di eliminazione temporanea, non sarà visibile nell'elenco. Se è necessario proteggere di nuovo la macchina virtuale, è necessario attendere la scadenza del periodo di eliminazione temporanea o annullare l'eliminazione della VM dall'elenco degli eliminati temporaneamente. Per altre informazioni, vedere [l'articolo relativo all'eliminazione temporanea per le macchine virtuali](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. In **Backup** selezionare **Abilita backup**. Questa operazione consente di distribuire i criteri nell'insieme di credenziali e nelle macchine virtuali e installare l'estensione di backup nell'agente di macchine virtuali in esecuzione nella macchina virtuale di Azure.

Dopo l'abilitazione del backup:

* Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.
* Verrà eseguito un backup iniziale in base alla pianificazione del backup.
* Quando vengono eseguiti i backup, tenere presente quanto segue:
  * Una macchina virtuale in esecuzione ha la massima probabilità di acquisire un punto di ripristino coerente con l'applicazione.
  * Tuttavia, anche se la macchina virtuale è spenta, viene eseguito il backup. Tale macchina virtuale è nota come macchina virtuale offline. In questo caso, il punto di ripristino sarà coerente con l'arresto anomalo del sistema.
* La connettività in uscita esplicita non è necessaria per consentire il backup delle VM di Azure.

### <a name="create-a-custom-policy"></a>Creare criteri personalizzati

Se si è scelto di creare un nuovo criterio di backup, specificare le impostazioni del criterio.

1. In **Nome criterio** specificare un nome significativo.
2. In **Pianificazione backup** specificare quando devono essere eseguiti i backup. Per le macchine virtuali di Azure è possibile eseguire backup giornalieri o settimanali.
3. In **Ripristino istantaneo** specificare per quanto tempo si vogliono mantenere in locale gli snapshot per il ripristino istantaneo.
    * Quando si esegue il ripristino, i dischi delle macchine virtuali di cui è stato eseguito il backup vengono copiati dalla risorsa di archiviazione attraverso la rete nella posizione di archiviazione di ripristino. Con il ripristino istantaneo è possibile sfruttare gli snapshot archiviati localmente eseguiti durante un processo di backup, senza attendere il trasferimento dei dati di backup nell'insieme di credenziali.
    * È possibile conservare gli snapshot per il ripristino istantaneo per un periodo compreso tra uno e cinque giorni. L'impostazione predefinita è due giorni.
4. In **Intervallo di conservazione** specificare per quanto tempo si vogliono conservare i punti di backup giornalieri o settimanali.
5. In **conservazione del punto** di backup mensile e **della conservazione del punto di backup annuale**specificare se si vuole mantenere un backup mensile o annuale dei backup giornalieri o settimanali.
6. Selezionare **OK** per salvare il criterio.

    ![Nuovo criterio di backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Man mano che si verificano modifiche temporali, modificare manualmente i criteri di backup in base alle necessità.

## <a name="trigger-the-initial-backup"></a>Attivare il backup iniziale

Il backup iniziale verrà eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come segue:

1. Nel menu dell'insieme di credenziali selezionare **elementi di backup**.
2. In **elementi di backup**selezionare **macchina virtuale di Azure**.
3. Nell'elenco **elementi di backup selezionare i puntini** di sospensione (...).
4. Selezionare **Esegui backup ora**.
5. In **Esegui backup** usare il comando del calendario per selezionare l'ultimo giorno di conservazione del punto di ripristino. Selezionare **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="verify-backup-job-status"></a>Verificare lo stato dei processi di backup

I dettagli del processo di backup per ogni backup di macchine virtuali sono costituiti da due fasi, la fase **Snapshot** seguita dalla fase **Trasferire i dati nell'insieme di credenziali**.<br/>
La fase Snapshot garantisce la disponibilità di un punto di ripristino memorizzato insieme ai dischi per **ripristini istantanei**, disponibili per un massimo di cinque giorni, a seconda del periodo di conservazione degli snapshot configurato dall'utente. La fase di trasferimento dei dati nell'insieme di credenziali crea un punto di ripristino nell'insieme di credenziali per la conservazione a lungo termine. Il trasferimento dei dati nell'insieme di credenziali viene avviato solo dopo il completamento della fase Snapshot.

  ![Stato dei processi di backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Sono presenti due **sottoattività** in esecuzione nel back-end, una per il processo di backup front-end che può essere controllata dal riquadro dei dettagli del **processo di backup** , come indicato di seguito:

  ![Stato dei processi di backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Il completamento della fase **Trasferire i dati nell'insieme di credenziali** può richiedere più giorni, a seconda delle dimensioni dei dischi, della varianza di ogni disco e di diversi altri fattori.

Lo stato dei processi può variare a seconda degli scenari seguenti:

**Snapshot** | **Trasferire i dati nell'insieme di credenziali** | **Stato processo**
--- | --- | ---
Completato | In corso | In corso
Completed | Operazione ignorata | Completi
Completi | Completi | Completi
Completi | Non riuscito | Completato con avviso
Non riuscito | Operazione non riuscita | Operazione non riuscita

Con questa funzionalità, per la stessa macchina virtuale è ora possibile eseguire due backup in parallelo, ma in entrambe le fasi (snapshot e trasferimento di dati nell'insieme di credenziali) si può eseguire una sola sottoattività. Negli scenari in cui un processo di backup in corso ha comportato la mancata riuscita del backup del giorno successivo, questo verrà evitato con questa funzionalità di separazione. Nei giorni successivi è possibile completare lo snapshot, mentre i **dati trasferiti nell'insieme di** credenziali vengono ignorati se lo stato del processo di backup del giorno precedente è in corso.
Il punto di ripristino incrementale creato nell'insieme di credenziali acquisirà tutta la varianza dall'ultimo punto di ripristino creato nell'insieme di credenziali. L'utente non ha alcun impatto sui costi.

## <a name="optional-steps"></a>Passaggi facoltativi

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina virtuale locale, può essere necessario installare l'agente manualmente, come riepilogato nella tabella.

**VM** | **Dettagli**
--- | ---
**Windows** | 1. [Scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) il file MSI per l'agente.<br/><br/> 2. Eseguire l'installazione sul computer con autorizzazioni di amministratore.<br/><br/> 3. Verificare l'installazione. In *C:\WindowsAzure\Packages* nella macchina virtuale fare clic con il pulsante destro del mouse su **WaAppAgent.exe** > **Proprietà**. Nella scheda **Dettagli** **Versione prodotto** deve corrispondere alla versione 2.6.1198.718 o successiva.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non sia in esecuzione alcuna operazione di backup e [reinstallare l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installare usando un pacchetto RPM o DEB dal repository dei pacchetti della distribuzione. Questo è il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure. Tutti i [provider di distribuzione supportati](../virtual-machines/linux/endorsed-distros.md) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non sia in esecuzione alcuna operazione di backup e aggiornare i file binari.

## <a name="next-steps"></a>Passaggi successivi

* Risolvere i problemi degli [agenti di macchine virtuali di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) o del [backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md).
* [Ripristinare](backup-azure-arm-restore-vms.md) macchine virtuali di Azure.
