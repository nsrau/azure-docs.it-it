---
title: Ripristinare le macchine virtuali tramite il portale di AzureRestore VMs by using the Azure portal
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino con il portale di Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b9cdb187aa3b8750bead8e81ad6d0ee50dcb3d6c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254921"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Come ripristinare i dati della macchina virtuale di Azure nel portale di AzureHow to restore Azure VM data in Azure portal

Questo articolo descrive come ripristinare i dati delle macchine virtuali di Azure dai punti di ripristino archiviati negli insiemi di credenziali di Servizi di ripristino di [Backup di Azure](backup-overview.md).

## <a name="restore-options"></a>Opzioni di ripristino

Backup di Azure offre diversi modi per ripristinare una macchina virtuale.

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la macchina virtuale, selezionare il gruppo di risorse e la rete virtuale (VNet) in cui verrà inserita e specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova macchina virtuale deve essere creata nella stessa area della macchina virtuale di origine.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, questa opzione non può essere utilizzata.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente prima di sostituire il disco e lo archivia nel percorso di gestione temporanea specificato. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e mantenuto in base ai criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene mantenuto nel gruppo di risorse. È possibile scegliere di eliminare manualmente i dischi originali se non sono necessari. <br/><br/>La sostituzione esistente è supportata per le macchine virtuali gestite non crittografate, incluse le macchine virtuali [create tramite immagini personalizzate.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Non è supportato per le macchine virtuali classiche.<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> La sostituzione esistente non è supportata per le macchine virtuali con risorse collegate (ad esempio [l'identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) o [l'insieme](https://docs.microsoft.com/azure/key-vault/key-vault-overview)di credenziali delle chiavi) perché l'app client di backup non dispone delle autorizzazioni per queste risorse durante l'esecuzione del ripristino.
**Regione trasversale (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare le macchine virtuali di Azure nell'area secondaria, ovvero un'area associata ad Azure.Cross Region restore can be used to restore Azure VMs in the secondary region, which is an [Azure paired region.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> È possibile ripristinare tutte le macchine virtuali di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Questa funzione è disponibile per le seguenti opzioni:<br> * [Creare una macchina virtualeCreate a VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Ripristina dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Al momento non è supportata l'opzione [Sostituisci dischi esistenti.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita dagli amministratori di backup e dagli amministratori dell'app.

> [!NOTE]
> È anche possibile ripristinare file e cartelle specifici in una macchina virtuale di Azure. [Altre informazioni](backup-azure-restore-files-from-vm.md)

## <a name="storage-accounts"></a>Account di archiviazione

Alcuni dettagli sugli account di archiviazione:Some details about storage accounts:

- **Creare una macchina virtuale:** quando si crea una nuova macchina virtuale, la macchina virtuale verrà inserita nell'account di archiviazione specificato.
- **Ripristina disco**: Quando si ripristina un disco, il disco viene copiato nell'account di archiviazione specificato. Il processo di ripristino genera un modello che è possibile scaricare e usare per specificare le impostazioni personalizzate della macchina virtuale. Questo modello viene inserito nell'account di archiviazione specificato.
- **Sostituisci disco:** quando si sostituisce un disco in una macchina virtuale esistente, Backup di Azure crea uno snapshot della macchina virtuale esistente prima di sostituire il disco. Lo snapshot viene archiviato nel percorso di gestione temporanea (account di archiviazione) specificato. Questo account di archiviazione viene usato per archiviare temporaneamente lo snapshot durante il processo di ripristino ed è consigliabile creare un nuovo account per eseguire questa operazione, che può essere facilmente rimosso in un secondo momento.
- **Percorso account di archiviazione:** l'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali. Vengono visualizzati solo questi account. Se non sono presenti account di archiviazione nel percorso, è necessario crearne uno.
- **Tipo di archiviazione:** l'archiviazione BLOB non è supportata.
- **Ridondanza dello storage**: l'archiviazione ridondante dell'area non è supportata. Le informazioni di replica e ridondanza per l'account vengono visualizzate tra parentesi dopo il nome dell'account.
- **Archiviazione Premium**:
  - Quando si ripristinano macchine virtuali non Premium, gli account di archiviazione Premium non sono supportati.
  - Quando si ripristinano macchine virtuali gestite, gli account di archiviazione Premium configurati con regole di rete non sono supportati.

## <a name="before-you-start"></a>Prima di iniziare

Per ripristinare una macchina virtuale (creare una nuova macchina virtuale), assicurarsi di disporre delle [autorizzazioni](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) corrette per il controllo degli accessi in base al ruolo per l'operazione Ripristina macchina virtuale.

Se non si dispone delle autorizzazioni, è possibile [ripristinare un disco](#restore-disks)e quindi, dopo il ripristino del disco, è possibile utilizzare il [modello](#use-templates-to-customize-a-restored-vm) generato come parte dell'operazione di ripristino per creare una nuova macchina virtuale.

## <a name="select-a-restore-point"></a>Selezionare un punto di ripristino

1. Nell'insieme di credenziali associato alla macchina virtuale da ripristinare fare clic su **Elementi** > di backup**Macchina virtuale di Azure.**
2. Fare clic su una macchina virtuale. Per impostazione predefinita, nel dashboard della macchina virtuale verranno visualizzati i punti di ripristino degli ultimi 30 giorni. È possibile visualizzare punti di ripristino antecedenti a 30 giorni o applicare un filtro per trovare punti di ripristino in base a date, intervalli di tempo e tipi diversi di coerenza degli snapshot.
3. Per ripristinare la macchina virtuale, fare clic su **Ripristina macchina virtuale**.

    ![Punto di ripristino](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selezionare un punto di ripristino da usare per il ripristino.

## <a name="choose-a-vm-restore-configuration"></a>Scegliere una configurazione di ripristino per la macchina virtuale

1. In **Configurazione di ripristino** selezionare un'opzione di ripristino:
    - **Crea nuovo:** usare questa opzione se si vuole creare una nuova macchina virtuale. È possibile creare una macchina virtuale con impostazioni semplici o ripristinare un disco e creare una macchina virtuale personalizzata.
    - **Sostituisci esistente**: usare questa opzione se si desidera sostituire i dischi in una macchina virtuale esistente.

        ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Specificare le impostazioni per l'opzione di ripristino selezionata.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Tra le [opzioni di ripristino](#restore-options) è possibile creare rapidamente una macchina virtuale con le impostazioni di base da un punto di ripristino.

1. In **Configurazione** > ripristino**Crea nuovo** > **tipo di ripristino**selezionare Crea una macchina **virtuale**.
2. In **Nome macchina virtuale**specificare una macchina virtuale che non esiste nella sottoscrizione.
3. In **Gruppo di risorse** selezionare un gruppo di risorse esistente per la nuova macchina virtuale o crearne uno nuovo con un nome univoco globale. Se si assegna un nome già esistente, Azure assegnerà al gruppo lo stesso nome della macchina virtuale.
4. In **Rete virtuale** selezionare la rete virtuale in cui verrà inserita la macchina virtuale. Vengono visualizzate tutte le reti virtuali associate alla sottoscrizione. Selezionare la subnet. La prima subnet è selezionata per impostazione predefinita.
5. In **Percorso di archiviazione**specificare l'account di archiviazione per la macchina virtuale. [Altre informazioni](#storage-accounts)

    ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

## <a name="restore-disks"></a>Ripristinare i dischi

Tra le [opzioni di ripristino](#restore-options) è possibile creare un disco da un punto di ripristino. Con il disco è quindi possibile eseguire una di queste operazioni:

- Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni e attivare la distribuzione della macchina virtuale. Modificare le impostazioni predefinite del modello e inviare il modello per la distribuzione della macchina virtuale.
- [Collegare i dischi ripristinati](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a una macchina virtuale esistente.
- [Creare una nuova macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) dai dischi ripristinati usando PowerShell.Create a new VM from the restored disks using PowerShell.

1. In **Configurazione ripristino** > **Crea nuovo** > **tipo di ripristino**selezionare **Ripristina dischi**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse per i dischi ripristinati o crearne uno nuovo con un nome univoco globale.
3. In **Account di archiviazione** specificare l'account in cui copiare i dischi rigidi virtuali. [Altre informazioni](#storage-accounts)

    ![Configurazione di ripristino completata](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

Quando la macchina virtuale usa dischi gestiti e si seleziona l'opzione **Crea macchina virtuale,** Backup di Azure non usa l'account di archiviazione specificato. Nel caso di **Ripristina dischi** e **Ripristino immediato**, l'account di archiviazione viene utilizzato solo per l'archiviazione del modello. I dischi gestiti vengono creati nel gruppo di risorse specificato.
Quando la macchina virtuale usa dischi non gestiti, questi vengono ripristinati come BLOB nell'account di archiviazione.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usare i modelli per personalizzare una VM ripristinata

Dopo il ripristino del disco, usare il modello generato come parte dell'operazione di ripristino per personalizzare e creare una nuova macchina virtuale:

1. Aprire **Ripristina - Dettagli processo** per il processo pertinente.

2. In **Ripristino - Dettagli processo** selezionare **Deploy Template** (Distribuisci modello) per avviare la distribuzione del modello.

    ![Drill-down del processo di ripristino](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Per personalizzare l'impostazione della macchina virtuale inclusa nel modello, fare clic su **Modifica modello**. Se si vuole aggiungere altre personalizzazioni, fare clic su **Modifica parametri**.
    - [Vedere altre informazioni](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sulla distribuzione delle risorse da un modello personalizzato.
    - [Vedere altre informazioni](../azure-resource-manager/templates/template-syntax.md) sulla creazione di modelli.

   ![Caricare la distribuzione del modello](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Immettere i valori personalizzati per la macchina virtuale, accettare quanto dichiarato in **Condizioni** e fare clic su **Acquista**.

   ![Inviare la distribuzione del modello](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Sostituire i dischi esistenti

Tra le [opzioni di ripristino](#restore-options) è possibile sostituire un disco di macchina virtuale esistente con il punto di ripristino selezionato. [Rivedere](#restore-options) tutte le opzioni di ripristino.

1. In **Configurazione di ripristino** fare clic su **Sostituisci esistente**.
2. In **Tipo ripristino** selezionare **Replace disk/s** (Sostituisci dischi). Questo è il punto di ripristino che verrà usato per sostituire i dischi delle macchine virtuali esistenti.
3. In **Percorso di gestione temporanea**specificare la posizione in cui salvare gli snapshot dei dischi gestiti correnti durante il processo di ripristino. [Altre informazioni](#storage-accounts)

   ![Configurazione guidata del ripristino - Sostituisci esistente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Ripristino tra aree

Come una delle opzioni di [ripristino,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)il ripristino tra aree (CRR) consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un'area associata ad Azure.As one of the restore options , Cross Region Restore (CRR) allows you to restore Azure VMs in a secondary region, which is an Azure paired region.

Per eseguire l'onboarding della funzione durante l'anteprima, leggere la [sezione Prima di iniziare](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Per [verificare](backup-create-rs-vault.md#configure-cross-region-restore) se CRR è abilitato, seguire le istruzioni in Configure Cross Region Restore

### <a name="view-backup-items-in-secondary-region"></a>Visualizzare gli elementi di backup nell'area secondariaView backup items in secondary region

Se CRR è abilitato, è possibile visualizzare gli elementi di backup nell'area secondaria.

1. Dal portale, andare a Unità di **backup** > di Servizi di ripristino**Elementi di backup**
2. Fare clic su **Area secondaria** per visualizzare gli elementi nell'area secondaria.

![Macchine virtuali nell'area secondariaVirtual machines in secondary region](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Seleziona regione secondaria](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Ripristino nell'area secondariaRestore in secondary region

L'esperienza utente di ripristino dell'area secondaria sarà simile all'esperienza utente di ripristino dell'area primaria. Quando si configurano i dettagli nel pannello Configurazione ripristino per configurare il ripristino, verrà richiesto di specificare solo i parametri dell'area secondaria.

![Scegliere la macchina virtuale da ripristinareChoose VM to restore](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Seleziona punto di ripristino](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Ripristinare la configurazione](./media/backup-azure-arm-restore-vms/rest-config.png)

![Notifica di ripristino trigger in corsoTrigger restore in progress notification](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Per ripristinare e creare una macchina virtuale, vedere [Creare una macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Per eseguire il ripristino come disco, fare riferimento a [Ripristina dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Dopo l'attivazione del ripristino e durante la fase di trasferimento dei dati, il processo di ripristino non può essere annullato.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitoraggio dei processi di ripristino dell'area secondariaMonitoring secondary region restore jobs

1. Dal portale, vai a Processi di > **backup** dell'insieme **di credenziali di ripristino**
2. Fare clic su **Area secondaria** per visualizzare gli elementi nell'area secondaria.

![Processi di backup filtrati](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Ripristinare le macchine virtuali con configurazioni specialiRestore VMs with special configurations

Esistono diversi scenari comuni in cui potrebbe essere necessario ripristinare le macchine virtuali.

**Scenario** | **Materiale sussidiario**
--- | ---
**Ripristino di macchine virtuali con vantaggio Hybrid Use** | Se una macchina virtuale Windows usa la [licenza per il vantaggio Hybrid Use (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), ripristinare i dischi e creare una nuova macchina virtuale usando il modello fornito (con **Tipo di licenza** impostato su **Windows_Server**) o PowerShell.  È possibile applicare questa impostazione anche dopo aver creato la macchina virtuale.
**Ripristino di macchine virtuali durante un'emergenza nel data center di Azure** | Se l'insieme di credenziali usa l'archiviazione con ridondanza geografica e il data center principale per la macchina virtuale si arresta, Backup di Azure supporta il ripristino delle macchine virtuali sottoposte a backup nel data center associato. Selezionare un account di archiviazione nel data center associato e ripristinare come di consueto. Backup di Azure usa il servizio di calcolo nell'area abbinata per creare la macchina virtuale ripristinata. [Vedere altre informazioni](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sulla resilienza dei data center.
**Ripristino di una macchina virtuale con controller di dominio singolo in un dominio singolo** | Ripristinare la macchina virtuale come qualsiasi altra macchina virtuale. Tenere presente quanto segue:<br/><br/> Dal punto di vista di Active Directory, la macchina virtuale di Azure è simile a qualsiasi altra macchina virtuale.<br/><br/> È anche disponibile Modalità ripristino servizi directory (Directory Services Restore Mode, DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. [Vedere altre informazioni](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sul backup e il ripristino dei controller di dominio virtualizzati.
**Ripristino di macchine virtuali con più controller di dominio in un dominio singolo** | Se altri controller di dominio nello stesso dominio possono essere raggiunti in rete, il controller di dominio può essere ripristinato come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller di dominio restante nel dominio o viene eseguito un ripristino in una rete isolata, effettuare un [ripristino della foresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Ripristino di domini multipli in una foresta** | È consigliabile eseguire un [ripristino della foresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Ripristino bare metal** | La differenza principale tra le macchine virtuali di Azure e gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per macchine virtuali. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali è una sostituzione completa con il ripristino bare metal.
**Ripristinare le VM con configurazioni di rete speciali** | Configurazioni di rete speciali includono macchine virtuali con bilanciamento del carico interno o esterno, con più schede di interfaccia di rete o con più indirizzi IP riservati. Per ripristinare queste macchine virtuali, usare l'[opzione relativa al disco di ripristino](#restore-disks). Questa opzione consente di creare una copia dei dischi rigidi virtuali nell'account di archiviazione specificato ed è quindi possibile creare una macchina virtuale con un servizio di bilanciamento del carico [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) o [esterno,](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) [più NICS](../virtual-machines/windows/multiple-nics.md)o [più indirizzi IP riservati,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)in base alla configurazione.
**Gruppo di sicurezza di rete (NSG) su NIC/Subnet** | Il backup delle macchine virtuali di Azure supporta il backup e il ripristino delle informazioni del gruppo di sicurezza di rete a livello di rete virtuale, subnet e scheda di interfaccia di rete.
**Macchine virtuali bloccate** | Backup di Azure supporta il backup e il ripristino delle macchine virtuali aggiunte a zone. [Altre informazioni](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Se non sono visibili, selezionare il simbolo **Notifiche** e quindi selezionare **Visualizza tutti i processi** per visualizzare lo stato del processo di ripristino.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Tenere traccia del ripristino come segue:

1. Per visualizzare le operazioni relative al processo, fare clic sul collegamento ipertestuale relativo alle notifiche. In alternativa, nell'insieme di credenziali fare clic su **Processi di backup** e quindi sulla macchina virtuale pertinente.

    ![Elenco di VM in un insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Per monitorare lo stato di un ripristino, fare clic su un processo di ripristino con stato **In corso**. Viene visualizzata la barra di avanzamento, che visualizza informazioni sullo stato di avanzamento del ripristino:

    - **Tempo stimato di ripristino**: Inizialmente fornisce il tempo necessario per completare l'operazione di ripristino. Con il progressivo avanzamento dell'operazione, il tempo impiegato si riduce fino a raggiungere il valore 0 al termine dell'operazione di ripristino.
    - **Percentage of restore** (Percentuale di ripristino): mostra la percentuale di completamento dell'operazione di ripristino.
    - **Numero di byte trasferiti**: se si esegue il ripristino creando una nuova macchina virtuale, vengono visualizzati i byte trasferiti rispetto al numero totale di byte da trasferire.

## <a name="post-restore-steps"></a>Operazioni successive al ripristino

Esistono diversi aspetti di cui tenere conto dopo il ripristino di una macchina virtuale:

- Le estensioni presenti durante la configurazione del backup vengono installate, ma non abilitate. Se si verifica un problema, reinstallare le estensioni.
- Se la macchina virtuale sottoposta a backup era associata a un indirizzo IP statico, la macchina virtuale ripristinata avrà un indirizzo IP dinamico per evitare conflitti. È possibile [aggiungere un indirizzo IP statico alla macchina virtuale ripristinata](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Una macchina virtuale ripristinata non ha un set di disponibilità. Se si usa l'opzione di ripristino del disco, è possibile [specificare un set](../virtual-machines/windows/tutorial-availability-sets.md) di disponibilità quando si crea una macchina virtuale dal disco usando il modello fornito o PowerShell.If you use the restore disk option, then you can specify an availability set when you create a VM from the disk using the provided template or PowerShell.
- Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza la password verrà bloccata dopo il ripristino. Per [reimpostare la password](../virtual-machines/linux/reset-password.md) nella VM ripristinata, usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni in modo da non dover reimpostare la password dopo il ripristino.
- Se non è possibile accedere alla macchina virtuale dopo il ripristino a causa di una relazione interrotta dalla macchina virtuale con il controller di dominio, eseguire la procedura seguente per visualizzare la macchina virtuale:If you are unable to access VM once restored due to VM having broken relationship with domain controller, then follow the steps below to bring up the VM:
  - Collegare il disco del sistema operativo come disco dati a una macchina virtuale ripristinata.
  - Installare manualmente l'agente di macchine virtuali se si ritiene che l'agente di Azure non risponda seguendo questo [collegamento.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)
  - Abilitare l'accesso alla console seriale nella macchina virtuale per consentire l'accesso dalla riga di comando alla macchina virtualeEnable Serial Console access on VM to allow command-line access to VM

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando la macchina virtuale viene ricompilata, usare il portale di Azure per reimpostare l'account amministratore locale e la passwordWhen the VM is rebuilt use Azure portal to reset local administrator account and password
  - Usare l'accesso alla console seriale e il comando CMD per disconnettere la macchina virtuale dal dominioUse Serial console access and CMD to disjoin VM from domain

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Dopo aver disgiunto e riavviato la macchina virtuale, sarà possibile eseguire correttamente RDP a VM con credenziali di amministratore locale e aggiungere nuovamente la macchina virtuale al dominio.

## <a name="backing-up-restored-vms"></a>Backup di macchine virtuali ripristinate

- Se una macchina virtuale è stata ripristinata nello stesso gruppo di risorse con lo stesso nome della macchina virtuale di cui è stato originariamente eseguito il backup, l'operazione di backup continuerà nella macchina virtuale dopo il ripristino.
- Se la macchina virtuale è stata ripristinata in un gruppo di risorse diverso o si è specificato un nome diverso per la macchina virtuale ripristinata, sarà necessario configurare il backup per la macchina virtuale ripristinata.

## <a name="next-steps"></a>Passaggi successivi

- In caso di difficoltà durante il processo di ripristino, [esaminare](backup-azure-vms-troubleshoot.md#restore) i problemi e gli errori comuni.
- Dopo il ripristino della macchina virtuale, vedere altre informazioni sulla [gestione delle macchine virtuali](backup-azure-manage-vms.md).
