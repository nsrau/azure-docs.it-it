---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino
description: Informazioni su come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino tramite Backup di Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: cba042efb08f121d4cd9fa5693edd69c827f1465
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727013"
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

## <a name="create-a-vault"></a>Creare un insieme di credenziali

 Un insieme di credenziali consente di archiviare i backup e i punti di ripristino creati nel corso del tempo, oltre ai criteri di backup associati ai computer sottoposti a backup. Creare un insieme di credenziali nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In Cerca digitare **Servizi di ripristino**. In **Servizi** fare clic su **Insiemi di credenziali dei servizi di ripristino**.

     ![Ricerca di insiemi di credenziali di Servizi di ripristino](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. Nel menu **Insiemi di credenziali dei servizi di ripristino** fare clic su **+Aggiungi**.

     ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. In **Insieme di credenziali di Servizi di ripristino** digitare un nome descrittivo per identificare l'insieme di credenziali.
    * Il nome deve essere univoco per la sottoscrizione di Azure.
    * Può contenere da 2 a 50 caratteri.
    * Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Selezionare la sottoscrizione di Azure, il gruppo di risorse e l'area geografica in cui creare l'insieme di credenziali. Fare quindi clic su **Crea**.
    * La creazione dell'insieme di credenziali può richiedere alcuni minuti.
    * Monitorare le notifiche di stato nell'area superiore destra del portale.

Dopo la creazione, l'insieme di credenziali viene visualizzato nell'elenco degli insiemi di credenziali di Servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Backup di Azure consente ora la personalizzazione del nome del gruppo di risorse creato dal servizio Backup di Azure. Per altre informazioni, vedere [Gruppo di risorse di backup di Azure per le macchine virtuali](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l'[archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali rappresenta il meccanismo principale di backup, è consigliabile usare l'archiviazione con ridondanza geografica.
* Per un'opzione più economica, è possibile usare l'[archiviazione con ridondanza locale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Modificare il tipo di replica di archiviazione nel modo seguente:

1. Nel nuovo insieme di credenziali fare clic su **Proprietà** nella sezione **Impostazioni**.
2. In**Proprietà** fare clic su **Aggiornamento** in **Configurazione di backup**.
3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario ricreare l'insieme di credenziali.

## <a name="apply-a-backup-policy"></a>Applicare un criterio di backup

Configurare un criterio di backup per l'insieme di credenziali.

1. Nell'insieme di credenziali fare clic su **+Backup** nella sezione **Panoramica**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. In **Obiettivo del backup** > **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale** >  **OK**. In questo modo viene registrata l'estensione delle macchine virtuali nell'insieme di credenziali.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali.
    * Il criterio predefinito esegue il backup della macchina virtuale una volta al giorno. I backup giornalieri vengono conservati per 30 giorni. Gli snapshot di ripristino istantaneo vengono conservati per due giorni.
    * Se non si vuole usare il criterio predefinito, selezionare **Crea nuovo** e creare un criterio personalizzato come descritto nella prossima procedura.

      ![Criterio di backup predefinito](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. In **Seleziona macchine virtuali** selezionare le macchine virtuali di cui si vuole eseguire il backup usando il criterio. Fare quindi clic su **OK**.

   * Le macchine virtuali selezionate vengono convalidate.
   * È possibile selezionare solo macchine virtuali presenti nella stessa area dell'insieme di credenziali.
   * Il backup delle macchine virtuali può essere eseguito in un solo insieme di credenziali.

     ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Solo le macchine virtuali nella stessa area e nella stessa sottoscrizione dell'insieme di credenziali sono disponibili per la configurazione del backup.

5. In **Backup** fare clic su **Abilita backup**. Questa operazione consente di distribuire i criteri nell'insieme di credenziali e nelle macchine virtuali e installare l'estensione di backup nell'agente di macchine virtuali in esecuzione nella macchina virtuale di Azure.

     ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo l'abilitazione del backup:

* Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.
* Verrà eseguito un backup iniziale in base alla pianificazione del backup.
* Quando vengono eseguiti i backup, tenere presente quanto segue:
  * Una macchina virtuale in esecuzione ha la massima probabilità di acquisire un punto di ripristino coerente con l'applicazione.
  * Anche se la macchina virtuale è disattivata, tuttavia, il backup viene eseguito. Tale macchina virtuale è nota come macchina virtuale offline. In questo caso, il punto di ripristino sarà coerente con l'arresto anomalo del sistema.
* La connettività esplicita in uscita non è necessaria per consentire il backup delle macchine virtuali di Azure.

### <a name="create-a-custom-policy"></a>Creare criteri personalizzati

Se si è scelto di creare un nuovo criterio di backup, specificare le impostazioni del criterio.

1. In **Nome criterio** specificare un nome significativo.
2. In **Pianificazione backup** specificare quando devono essere eseguiti i backup. Per le macchine virtuali di Azure è possibile eseguire backup giornalieri o settimanali.
3. In **Ripristino istantaneo** specificare per quanto tempo si vogliono mantenere in locale gli snapshot per il ripristino istantaneo.
    * Quando si esegue il ripristino, i dischi delle macchine virtuali di cui è stato eseguito il backup vengono copiati dalla risorsa di archiviazione attraverso la rete nella posizione di archiviazione di ripristino. Con il ripristino istantaneo è possibile usare gli snapshot archiviati localmente eseguiti durante un processo di backup, senza attendere il trasferimento dei dati di backup nell'insieme di credenziali.
    * È possibile conservare gli snapshot per il ripristino istantaneo per un periodo compreso tra uno e cinque giorni. L'impostazione predefinita è due giorni.
4. In **Intervallo di conservazione** specificare per quanto tempo si vogliono conservare i punti di backup giornalieri o settimanali.
5. In **Conservazione del punto di backup mensile** specificare se si vuole mantenere un backup mensile dei backup giornalieri o settimanali.
6. Fare clic su **OK** per salvare il criterio.

    ![Nuovo criterio di backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Man mano che si verificano modifiche temporali, modificare manualmente i criteri di backup in base alle necessità.

## <a name="trigger-the-initial-backup"></a>Attivare il backup iniziale

Il backup iniziale verrà eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come segue:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup** fare clic su **Macchina virtuale di Azure**.
3. In **Elementi di backup** fare clic sui puntini di sospensione (...).
4. Fare clic su **Esegui backup ora**.
5. In **Esegui backup** usare il comando del calendario per selezionare l'ultimo giorno di conservazione del punto di ripristino. Fare quindi clic su **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="verify-backup-job-status"></a>Verificare lo stato dei processi di backup

I dettagli del processo di backup per ogni backup di macchine virtuali sono costituiti da due fasi, la fase **Snapshot** seguita dalla fase **Trasferire i dati nell'insieme di credenziali**.<br/>
La fase Snapshot garantisce la disponibilità di un punto di ripristino memorizzato insieme ai dischi per **ripristini istantanei**, disponibili per un massimo di cinque giorni, a seconda del periodo di conservazione degli snapshot configurato dall'utente. La fase di trasferimento dei dati nell'insieme di credenziali crea un punto di ripristino nell'insieme di credenziali per la conservazione a lungo termine. Il trasferimento dei dati nell'insieme di credenziali viene avviato solo dopo il completamento della fase Snapshot.

  ![Stato dei processi di backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Due **sottoattività** sono in esecuzione nel back-end, una per il processo di backup front-end che può essere controllato dal pannello dei dettagli del **processo di backup**, come illustrato di seguito:

  ![Stato dei processi di backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Il completamento della fase **Trasferire i dati nell'insieme di credenziali** può richiedere più giorni, a seconda delle dimensioni dei dischi, della varianza di ogni disco e di diversi altri fattori.

Lo stato dei processi può variare a seconda degli scenari seguenti:

**Snapshot** | **Trasferire i dati nell'insieme di credenziali** | **Stato processo**
--- | --- | ---
Completi | In corso | In corso
Completi | Operazione ignorata | Completi
Completi | Completi | Completi
Completi | Operazione non riuscita | Completato con avviso
Operazione non riuscita | Operazione non riuscita | Operazione non riuscita

Con questa funzionalità, per la stessa macchina virtuale è ora possibile eseguire due backup in parallelo, ma in entrambe le fasi (snapshot e trasferimento di dati nell'insieme di credenziali) si può eseguire una sola sottoattività. Con questa funzionalità di separazione, pertanto, sarà possibile evitare che a causa di un processo di backup in corso il backup del giorno successivo abbia esito negativo. È possibile completare lo snapshot del giorno successivo mentre la fase **Trasferire i dati nell'insieme di credenziali** viene ignorata se è in corso il processo di backup del giorno precedente.
Il punto di ripristino incrementale creato nell'insieme di credenziali acquisirà tutta la varianza dall'ultimo punto di ripristino creato nell'insieme di credenziali. Questa funzionalità non ha alcun impatto sull'utente dal punto di vista dei costi.

## <a name="optional-steps"></a>Passaggi facoltativi

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina virtuale locale, può essere necessario installare l'agente manualmente, come riepilogato nella tabella.

**VM** | **Dettagli**
--- | ---
**Windows** | 1. [Scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) il file MSI per l'agente.<br/><br/> 2. Eseguire l'installazione sul computer con autorizzazioni di amministratore.<br/><br/> 3. Verificare l'installazione. In *C:\WindowsAzure\Packages* nella macchina virtuale fare clic con il pulsante destro del mouse su **WaAppAgent.exe** > **Proprietà**. Nella scheda **Dettagli** **Versione prodotto** deve corrispondere alla versione 2.6.1198.718 o successiva.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non sia in esecuzione alcuna operazione di backup e [reinstallare l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installare usando un pacchetto RPM o DEB dal repository dei pacchetti della distribuzione. Questo è il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non sia in esecuzione alcuna operazione di backup e aggiornare i file binari.

>[!NOTE]
> **Backup di Azure ora supporta il backup e il ripristino selettivi dei dischi tramite la soluzione di backup delle macchine virtuali di Azure.**
>
>Backup di Azure attualmente supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale contemporaneamente tramite la soluzione di backup delle macchine virtuali. Con la funzionalità di esclusione disco è possibile scegliere di eseguire il backup di uno o più dischi dati tra quelli disponibili in una macchina virtuale. Questa è una soluzione efficiente ed economica per soddisfare le esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup. Questo consente anche di avere un subset di dischi ripristinati dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dall'insieme di credenziali.
>
>**Per registrarsi per l'anteprima, inviare un messaggio all'indirizzo AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Passaggi successivi

* Risolvere i problemi degli [agenti di macchine virtuali di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) o del [backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md).
* [Ripristinare](backup-azure-arm-restore-vms.md) macchine virtuali di Azure.
