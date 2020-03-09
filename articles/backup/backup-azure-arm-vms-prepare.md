---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino
description: Viene descritto come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino con backup di Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363876"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino

Questo articolo descrive come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino tramite il servizio [backup di Azure](backup-overview.md) .

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Preparare le macchine virtuali di Azure.
> * Creare un insieme di credenziali.
> * Individuare le VM e configurare un criterio di backup.
> * Abilitare il backup per le macchine virtuali di Azure.
> * Eseguire il backup iniziale.

> [!NOTE]
> Questo articolo descrive come configurare un insieme di credenziali e selezionare le macchine virtuali di cui eseguire il backup. È utile per eseguire il backup di più macchine virtuali. In alternativa, è possibile [eseguire il backup di una singola macchina virtuale di Azure](backup-azure-vms-first-look-arm.md) direttamente dalle impostazioni della macchina virtuale.

## <a name="before-you-start"></a>Prima di iniziare

* [Esaminare](backup-architecture.md#architecture-built-in-azure-vm-backup) l'architettura di backup delle VM di Azure.
* [Informazioni su](backup-azure-vms-introduction.md) backup delle macchine virtuali di Azure ed estensione di backup.
* [Esaminare la matrice di supporto prima di configurare il](backup-support-matrix-iaas.md) backup.

Inoltre, in alcune circostanze potrebbe essere necessario eseguire alcune operazioni:

* **Installare l'agente di macchine virtuali nella VM**: backup di Azure esegue il backup delle VM di Azure installando un'estensione nell'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una VM personalizzata o si esegue la migrazione di un computer locale, potrebbe essere necessario [installare l'agente manualmente](#install-the-vm-agent).

## <a name="create-a-vault"></a>Creare un insieme di credenziali

 Un insieme di credenziali consente di archiviare i backup e i punti di ripristino creati nel corso del tempo, oltre ai criteri di backup associati ai computer sottoposti a backup. Creare un insieme di credenziali nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In Cerca digitare **servizi di ripristino**. In **Servizi**fare clic su insiemi di credenziali **dei servizi di ripristino**.

     ![Cercare gli insiemi di credenziali dei servizi di ripristino](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. Nel menu insiemi di credenziali **dei servizi di ripristino** fare clic su **+ Aggiungi**.

     ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Nell'insieme di credenziali di **servizi di ripristino**Digitare un nome descrittivo per identificare l'insieme di credenziali.
    * Il nome deve essere univoco per la sottoscrizione di Azure.
    * Può contenere da 2 a 50 caratteri.
    * Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Selezionare la sottoscrizione di Azure, il gruppo di risorse e l'area geografica in cui creare l'insieme di credenziali. Fare quindi clic su **Crea**.
    * La creazione dell'insieme di credenziali può richiedere alcuni minuti.
    * Monitorare le notifiche di stato nell'area superiore destra del portale.

Una volta creato, l'insieme di credenziali viene visualizzato nell'elenco insiemi di credenziali dei servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Backup di Azure consente ora la personalizzazione del nome del gruppo di risorse creato dal servizio backup di Azure. Per altre informazioni, vedere [gruppo di risorse di backup di Azure per le macchine virtuali](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l' [archiviazione con ridondanza geografica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di usare GRS.
* Per un'opzione più economica, è possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Modificare il tipo di replica di archiviazione come segue:

1. Nel nuovo insieme di credenziali fare clic su **Proprietà** nella sezione **Impostazioni** .
2. In **Proprietà**, in **configurazione backup**, fare clic su **Aggiorna**.
3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario ricreare l'insieme di credenziali.

## <a name="apply-a-backup-policy"></a>Applicare un criterio di backup

Configurare un criterio di backup per l'insieme di credenziali.

1. Nell'insieme di credenziali fare clic su **+ backup** nella sezione **Panoramica** .

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. In **obiettivo di Backup** > in **cui è in esecuzione il carico di lavoro?** Selezionare **Azure**. In **che cosa si vuole eseguire il backup?** selezionare **macchina virtuale** >  **OK**. In questo modo viene registrata l'estensione delle macchine virtuali nell'insieme di credenziali.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali.
    * Il criterio predefinito esegue il backup della VM una volta al giorno. I backup giornalieri vengono conservati per 30 giorni. Gli snapshot di ripristino istantaneo vengono conservati per due giorni.
    * Se non si vuole usare i criteri predefiniti, selezionare **Crea nuovo**e creare un criterio personalizzato come descritto nella procedura seguente.

      ![Criteri di backup predefiniti](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. In **Seleziona macchine virtuali**selezionare le VM di cui si vuole eseguire il backup usando il criterio. Fare quindi clic su **OK**.

   * Le macchine virtuali selezionate vengono convalidate.
   * È possibile selezionare solo macchine virtuali presenti nella stessa area dell'insieme di credenziali.
   * Il backup delle macchine virtuali può essere eseguito in un solo insieme di credenziali.

     ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. In **backup**fare clic su **Abilita backup**. Questa operazione consente di distribuire i criteri nell'insieme di credenziali e nelle macchine virtuali e installare l'estensione di backup nell'agente di macchine virtuali in esecuzione nella macchina virtuale di Azure.

     ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo l'abilitazione del backup:

* Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.
* Un backup iniziale viene eseguito in base alla pianificazione del backup.
* Quando vengono eseguiti i backup, tenere presente quanto segue:
  * Una macchina virtuale in esecuzione ha la massima probabilità di acquisire un punto di ripristino coerente con l'applicazione.
  * Tuttavia, anche se la macchina virtuale è spenta, viene eseguito il backup. Tale macchina virtuale è nota come macchina virtuale offline. In questo caso, il punto di ripristino sarà coerente con l'arresto anomalo del sistema.
* La connettività in uscita esplicita non è necessaria per consentire il backup delle VM di Azure.

### <a name="create-a-custom-policy"></a>Creare criteri personalizzati

Se si è scelto di creare un nuovo criterio di backup, specificare le impostazioni dei criteri.

1. In **Nome criterio**specificare un nome significativo.
2. In **pianificazione backup**specificare quando devono essere eseguiti i backup. È possibile eseguire backup giornalieri o settimanali per le macchine virtuali di Azure.
3. In **ripristino istantaneo**specificare per quanto tempo si vogliono mantenere gli snapshot localmente per il ripristino immediato.
    * Quando si esegue il ripristino, il backup dei dischi delle macchine virtuali viene copiato dalla risorsa di archiviazione attraverso la rete al percorso di archiviazione di ripristino. Con il ripristino istantaneo è possibile sfruttare gli snapshot archiviati localmente eseguiti durante un processo di backup, senza attendere il trasferimento dei dati di backup nell'insieme di credenziali.
    * È possibile mantenere gli snapshot per il ripristino immediato per un periodo compreso tra uno e cinque giorni. L'impostazione predefinita è due giorni.
4. In periodo di **mantenimento**dati specificare per quanto tempo si vogliono mantenere i punti di backup giornalieri o settimanali.
5. In **conservazione del punto di backup mensile**specificare se si vuole mantenere un backup mensile dei backup giornalieri o settimanali.
6. Fare clic su **OK** per salvare il criterio.

    ![Nuovi criteri di backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Quando si verificano modifiche temporali, modificare manualmente i criteri di backup come richiesto.

## <a name="trigger-the-initial-backup"></a>Attiva il backup iniziale

Il backup iniziale viene eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come indicato di seguito:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **elementi di backup**fare clic su **macchina virtuale di Azure**.
3. Nell'elenco elementi di backup fare clic sui **puntini** di sospensione (...).
4. Fare clic su **Esegui backup ora**.
5. In **backup ora**usare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="verify-backup-job-status"></a>Verificare lo stato del processo di backup

I dettagli del processo di backup per ogni backup della macchina virtuale sono costituiti da due fasi, la fase di **snapshot** seguita dalla fase di **trasferimento dei dati all'** insieme di credenziali.<br/>
La fase di snapshot garantisce la disponibilità di un punto di ripristino memorizzato insieme ai dischi per i **ripristini istantanei** e sono disponibili per un massimo di cinque giorni, a seconda del periodo di memorizzazione dello snapshot configurato dall'utente. Il trasferimento dei dati nell'insieme di credenziali crea un punto di ripristino nell'insieme di credenziali per la conservazione a lungo termine. Il trasferimento dei dati nell'insieme di credenziali viene avviato solo dopo il completamento della fase di snapshot.

  ![Stato del processo di backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Sono presenti due **sottoattività** in esecuzione nel back-end, una per il processo di backup front-end che può essere controllata dal pannello dei dettagli del **processo di backup** , come indicato di seguito:

  ![Stato del processo di backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Il completamento della fase di **trasferimento dei dati in** un insieme di credenziali può richiedere più giorni, a seconda delle dimensioni dei dischi, della varianza per ogni disco e di diversi altri fattori.

Lo stato del processo può variare a seconda dei seguenti scenari:

**Snapshot** | **Trasferire i dati nell'insieme di credenziali** | **Stato processo**
--- | --- | ---
Completi | In corso | In corso
Completi | Operazione ignorata | Completi
Completi | Completi | Completi
Completi | Operazione non riuscita | Completato con avviso
Operazione non riuscita | Operazione non riuscita | Operazione non riuscita

Ora con questa funzionalità, per la stessa macchina virtuale, due backup possono essere eseguiti in parallelo, ma in entrambe le fasi (snapshot, trasferimento di dati nell'insieme di credenziali) è possibile eseguire una sola attività secondaria. Quindi, in scenari in cui un processo di backup è stato eseguito, il backup del giorno successivo avrà esito negativo con questa funzionalità di separazione. Per i backup del giorno successivo è possibile completare lo snapshot mentre **i dati trasferiti** nell'insieme di credenziali sono stati ignorati se il processo di backup di un giorno precedente è in corso.
Il punto di ripristino incrementale creato nell'insieme di credenziali acquisirà tutta la varianza dall'ultimo punto di ripristino creato nell'insieme di credenziali. L'utente non ha alcun impatto sui costi.

## <a name="optional-steps"></a>Passaggi facoltativi

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una VM personalizzata o si esegue la migrazione di un computer locale, potrebbe essere necessario installare l'agente manualmente, come riepilogato nella tabella.

**VM** | **Dettagli**
--- | ---
**Windows** | 1. [scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) il file MSI dell'agente.<br/><br/> 2. installare con le autorizzazioni di amministratore nel computer.<br/><br/> 3. verificare l'installazione. In *C:\WindowsAzure\Packages* nella macchina virtuale fare clic con il pulsante destro del mouse su **WaAppAgent. exe** > **proprietà**. Nella scheda **Dettagli** la **versione del prodotto** deve essere 2.6.1198.718 o successiva.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non siano in esecuzione operazioni di backup e [reinstallare l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installare usando un pacchetto RPM o un pacchetto DEB dal repository del pacchetto della distribuzione. Questo è il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non siano in esecuzione operazioni di backup e aggiornare i file binari.

>[!NOTE]
> **Backup di Azure supporta ora il backup e il ripristino dei dischi selettivi con la soluzione di backup della macchina virtuale di Azure.**
>
>Attualmente, backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale con la soluzione di backup della macchina virtuale. Con la funzionalità Escludi disco è possibile scegliere di eseguire il backup di uno o più dischi dati in una macchina virtuale. Questo offre una soluzione efficiente ed economica per le esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup, che consente di avere un subset di dischi ripristinati dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dall'insieme di credenziali.
>
>**Per iscriverti all'anteprima, scrivici all'indirizzo AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Passaggi successivi

* Risolvere i problemi relativi agli [agenti VM di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) o al [backup di macchine virtuali di Azure](backup-azure-vms-troubleshoot.md).
* [Ripristino](backup-azure-arm-restore-vms.md) di Macchine virtuali di Azure.
