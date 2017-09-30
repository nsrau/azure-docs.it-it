---
title: 'Backup di Azure: Ripristinare le macchine virtuali utilizzando il portale di Azure | Documentazione Microsoft'
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino con il portale di Azure
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: ripristinare il backup; come ripristinare; punto di ripristino.
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: d234d9c97064e22a0ea882f199da326b0aae0674
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Usare il portale di Azure per ripristinare macchine virtuali
> [!div class="op_single_selector"]
> * [Ripristinare VM nel portale classico](backup-azure-restore-vms.md)
> * [Ripristinare VM nel portale di Azure](backup-azure-arm-restore-vms.md)
>
>

È possibile proteggere i dati mediante la creazione di snapshot dei dati a intervalli definiti. Questi snapshot sono noti come punti di ripristino e vengono archiviati negli insiemi di credenziali dei servizi di ripristino. Se o quando è necessario ripristinare o ricreare una VM, è possibile ripristinare la macchina virtuale da qualsiasi punto di ripristino salvato. Quando si ripristina un punto di ripristino, è possibile creare una nuova macchina virtuale che funge da rappresentazione temporizzata della macchina virtuale su cui è stato eseguito il backup, oppure è possibile ripristinare i dischi e usare il modello fornito insieme al punto di ripristino per personalizzare la macchina virtuale ripristinata o eseguire un ripristino dei singoli file. In questo articolo viene illustrato come ripristinare una macchina virtuale in una nuova macchina virtuale o ripristinare tutti i dischi su cui è stato eseguito il backup. Per il ripristino dei singoli file, fare riferimento a [Ripristinare i file da backup di macchine virtuali di Azure](backup-azure-restore-files-from-vm.md)

![3-ways-restore-from-vm-backup](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

Il ripristino di una macchina virtuale o di tutti i dischi dal backup della macchina virtuale prevede due passaggi:

1. Selezionare un punto di ripristino
2. Selezionare il tipo di ripristino: creare una nuova macchina virtuale o ripristinare i dischi e specificare i parametri necessari. 

## <a name="select-restore-point-for-restore"></a>Selezionare un punto di ripristino
1. Accedere al [portale di Azure](http://portal.azure.com/)
2. Scegliere **Sfoglia** nel menu di Azure e nell'elenco dei servizi digitare **Servizi di ripristino**. L'elenco di servizi viene modificato in base a quanto digitato. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

    ![Aprire l'insieme di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Viene visualizzato l'elenco di insiemi di credenziali della sottoscrizione.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Dall'elenco selezionare l'insieme di credenziali associato alla VM da ripristinare. Quando si fa clic sull'insieme di credenziali, viene aperto il dashboard corrispondente.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Nel dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure** nel riquadro **Elementi di backup** per visualizzare le macchine virtuali associate all'insieme di credenziali.

    ![Dashboard dell'insieme di credenziali](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Viene aperto il pannello **Elementi di backup** , che include l'elenco di macchine virtuali di Azure.

    ![Elenco di VM nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Dall'elenco selezionare una VM per aprire il dashboard. Nel dashboard della VM viene visualizzata l'area Monitoraggio, che include il riquadro Punti di ripristino.

    ![Elenco di VM nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. Scegliere **Ripristina**

    ![Elenco di VM nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Viene aperto il pannello Ripristina.

    ![Pannello Ripristina](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Nel pannello **Ripristina** fare clic su **Punto di ripristino** per aprire il pannello **Seleziona punto di ripristino**.

    ![Pannello Ripristina](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Per impostazione predefinita, la finestra di dialogo visualizza tutti i punti di ripristino degli ultimi 30 giorni. Usare il **filtro** per modificare l'intervallo di tempo dei punti di ripristino visualizzati. Per impostazione predefinita, vengono visualizzati i punti di ripristino di ogni coerenza. Modificare il filtro **Tutti i punti di ripristino** per selezionare una coerenza specifica dei punti di ripristino. Per altre informazioni sui tipi di punto di ripristino, vedere la spiegazione relativa alla [Coerenza dei dati](backup-azure-vms-introduction.md#data-consistency).  

   * **Coerenza dei punti di ripristino** scegliere:
     * Punti di ripristino coerenti con l'arresto anomalo del sistema
     * Punti di ripristino coerenti con l'applicazione
     * Punti di ripristino coerenti con il file system
     * Tutti i punti di ripristino  
8. Scegliere un punto di ripristino e fare clic su **OK**.

    ![Scegliere il punto di ripristino ](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Il pannello **Ripristina** indica che il punto di ripristino è stato impostato.

    ![Punto di ripristino impostato](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. Nel pannello **Ripristino** si apre automaticamente la schermata **Configurazione di ripristino** dopo aver impostato il punto di ripristino.

## <a name="choosing-a-vm-restore-configuration"></a>Scelta di una configurazione di ripristino per la macchina virtuale
Dopo aver selezionato il punto di ripristino, scegliere una configurazione per la macchina virtuale di ripristino. È possibile configurare la macchina virtuale ripristinata tramite il portale di Azure o PowerShell.

1. Se non è già aperto, passare al pannello **Ripristina** . Assicurarsi di aver selezionato un [punto di ripristino](#select-restore-point-for-restore), quindi fare clic su **Configurazione di ripristino** per aprire il pannello **Configurazione recupero**.

    ![Configurazione guidata del ripristino configurata](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Nel pannello **Restore configuration** (Configurazione di ripristino) sono possibili due opzioni:
   * Ripristinare la macchina virtuale completa
   * Ripristinare i dischi di cui è stato eseguito il backup

Il portale offre un'opzione di creazione rapida per la macchina virtuale ripristinata. Se si desidera personalizzare la configurazione della macchina virtuale o i nomi delle risorse create come parte dell'opzione di creazione di una nuova macchina virtuale, usare PowerShell o il portale per ripristinare i dischi su cui è stato eseguito il backup e i comandi di PowerShell per collegarli alla scelta della configurazione della macchina virtuale. Oppure usare il modello di cui sono dotati i dischi di ripristino per personalizzare la macchina virtuale ripristinata. Vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](#restoring-vms-with-special-network-configurations) per informazioni dettagliate su come ripristinare una macchina virtuale che dispone di più schede di interfaccia di rete o nel bilanciamento del carico. Se la macchina virtuale Windows usa le [licenze HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), è necessario ripristinare i dischi e usare PowerShell o il modello come specificato di seguito per creare la macchina virtuale e assicurarsi di specificare LicenseType come "Windows_Server" durante la creazione della macchina virtuale per sfruttare i vantaggi HUB sulla macchina virtuale ripristinata. 
 
## <a name="create-a-new-vm-from-restore-point"></a>Creare una nuova macchina virtuale dal punto di ripristino
Se non lo si è già fatto, [selezionare un punto di ripristino](#restoring-vms-with-special-network-configurations) prima di passare alla creazione di una nuova macchina virtuale dal punto di ripristino. Dopo aver selezionato il punto di ripristino, nel pannello **Configurazione di ripristino** specificare o selezionare i valori per ciascuno dei campi seguenti:

* **Restore Type** (Tipo ripristino): creare la macchina virtuale.
* **Nome macchina virtuale** : specificare un nome per la macchina virtuale. Il nome deve essere univoco per il gruppo di risorse (per una macchina virtuale distribuita con il modello di distribuzione Azure Resource Manager) oppure per il servizio cloud (per una macchina virtuale distribuita con il modello di distribuzione classica). Non è possibile sostituire la macchina virtuale se esiste già nella sottoscrizione.
* **Gruppo di risorse** : usare un gruppo di risorse esistente oppure crearne uno nuovo. Se si ripristina una VM classica, usare questo campo per specificare il nome del nuovo servizio cloud. Quando si crea un nuovo gruppo di risorse o un servizio cloud, è necessario che il nome sia univoco a livello globale. Il nome del servizio cloud è in genere associato a un URL pubblico, ad esempio: [serviziocloud].cloudapp.net. Se si prova a usare un nome già usato per il gruppo di risorse cloud/servizio cloud, Azure assegna al gruppo di risorse/servizio cloud lo stesso nome della VM. Azure visualizza i gruppi di risorse/servizi cloud e le VM non associate ai gruppi di affinità. Per altre informazioni, vedere [Come eseguire la migrazione da gruppi di affinità a una rete virtuale (VNet) regionale](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
* **Rete virtuale** : selezionare la rete virtuale (VNET) quando si crea la macchina virtuale. Questo campo indica tutte le VNET associate alla sottoscrizione. Il gruppo di risorse della macchina virtuale è visualizzato tra parentesi.
* **Subnet** : se la VNET ha subnet, la prima subnet viene selezionata per impostazione predefinita. Se sono presenti altre subnet, selezionare quella desiderata.
* **Account di archiviazione** : questo menu elenca gli account di archiviazione nella stessa località dell'insieme di credenziali dei servizi di ripristino. Gli account di archiviazione con ridondanza della zona non sono supportati. Se non sono disponibili account di archiviazione con la stessa posizione dell'insieme di credenziali dei Servizi di ripristino, è necessario crearne uno prima di iniziare l'operazione di ripristino. Il tipo di replica dell'account di archiviazione viene visualizzato tra parentesi.

![Configurazione guidata del ripristino configurata](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

> [!NOTE]
> 1. Se si vuole ripristinare una macchina virtuale distribuita con il modello di distribuzione Resource Manager, è necessario identificare una rete virtuale. Una rete virtuale è facoltativa per una macchina virtuale distribuita con il modello di distribuzione classica.
> 2. Se si desidera ripristinare le macchine virtuali con dischi gestiti, assicurarsi che l'account di archiviazione selezionato non sia stato abilitato per la crittografia del servizio di archiviazione (Storage Service Encryption - SSE) nel corso del suo ciclo di vita.
> 3. Tutti i dischi ripristinati saranno di tipo premium o standard a seconda del tipo di archiviazione dell'account di archiviazione selezionato (standard o premium). Attualmente non sono supportati dischi in modalità mista durante il ripristino.  
>
>

Nel pannello **Configurazione di ripristino** fare clic su **OK** per portare a termine la configurazione di ripristino. Nel pannello **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

## <a name="restore-backed-up-disks"></a>Ripristinare i dischi di cui è stato eseguito il backup
Se si desidera personalizzare la macchina virtuale da creare dai dischi di cui è stato eseguito il backup anziché da quella presente nel pannello di configurazione di ripristino, selezionare **Restore disks** (Ripristino dischi) come valore per **Restore Type** (Tipo ripristino). Questa opzione richiede un account di archiviazione in cui vengono copiati i dischi dal backup. Quando si sceglie un account di archiviazione, è necessario selezionarne uno che condivida la stessa località dell'insieme di credenziali dei servizi di ripristino. Gli account di archiviazione con ridondanza della zona non sono supportati. Se non sono disponibili account di archiviazione con la stessa posizione dell'insieme di credenziali dei Servizi di ripristino, è necessario crearne uno prima di iniziare l'operazione di ripristino. Il tipo di replica dell'account di archiviazione viene visualizzato tra parentesi.

Al termine dell'operazione di ripristino, è possibile:
* [Usare il modello per personalizzare la macchina virtuale ripristinata](#use-templates-to-customize-restore-vm)
* [Usare i dischi ripristinati per collegarlo a una macchina virtuale esistente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Creare una nuova macchina virtuale tramite PowerShell dai dischi ripristinati.](./backup-azure-vms-automation.md#restore-an-azure-vm)

Nel pannello **Configurazione di ripristino** fare clic su **OK** per portare a termine la configurazione di ripristino. Nel pannello **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

![Configurazione di ripristino completata](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino
Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenere traccia dell'operazione di ripristino. Il servizio di backup crea anche la notifica e la visualizza temporaneamente nell'area di notifica del portale. Se la notifica non viene visualizzata, è sempre possibile fare clic sull'icona Notifiche per visualizzare tutte le notifiche.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification.png)

Per visualizzare l'operazione durante l'elaborazione oppure per visualizzarla dopo il completamento, aprire l'elenco Processi di backup.

1. Scegliere **Sfoglia** nel menu di Azure e nell'elenco dei servizi digitare **Servizi di ripristino**. L'elenco di servizi viene modificato in base a quanto digitato. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

    ![Aprire l'insieme di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Viene visualizzato l'elenco di insiemi di credenziali della sottoscrizione.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Dall'elenco selezionare l'insieme di credenziali associato alla VM ripristinata. Quando si fa clic sull'insieme di credenziali, viene aperto il dashboard corrispondente.
3. Nel riquadro **Processi di backup** del dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure** per visualizzare i processi associati all'insieme di credenziali.

    ![Dashboard dell'insieme di credenziali](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Viene aperto il pannello **Processi di backup** e viene visualizzato l'elenco dei processi.

    ![Elenco di VM nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-restore-vm"></a>Usare i modelli per personalizzare una macchina virtuale di ripristino
Una volta [completata l'operazione di ripristino dei dischi](#Track-the-restore-operation), è possibile usare il modello che viene generato come parte dell'operazione di ripristino per creare una nuova macchina virtuale con una configurazione diversa dalla configurazione del backup o per personalizzare i nomi delle risorse create al momento della creazione di una nuova macchina virtuale dal punto di ripristino. 

> [!NOTE]
> Verranno aggiunti i modelli come parte dell'operazione di ripristino dei dischi per i punti di ripristino creati dopo il 1° marzo 2017. Sono applicabili alle macchine virtuali con disco non gestito. Il supporto per le macchine virtuali con disco gestito verrà fornito nelle versioni future. 
>
>

Per ottenere il modello generato come parte dell'opzione dei dischi di ripristino

1. Visualizzare i dettagli del processo di ripristino corrispondenti al processo. 
2. Nella schermata dei dettagli del processo di ripristino fare clic sul pulsante *Deploy Template* (Distribuisci modello) per avviare la distribuzione del modello. 

     ![drill-down del processo di ripristino](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
Nel pannello Deploy Template (Distribuisci modello) per la distribuzione personalizzata usare la distribuzione del modello per [modificare e distribuire il modello](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) o aggiungere altre personalizzazioni [creando un modello](../azure-resource-manager/resource-group-authoring-templates.md) prima della distribuzione. 

   ![caricamento della distribuzione del modello](./media/backup-azure-arm-restore-vms/loading-template.png)
   
Dopo aver immesso i valori richiesti, accettare le *Condizioni per l'utilizzo* e fare clic su **Acquista**.

   ![invio della distribuzione del modello](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Operazioni successive al ripristino
* Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza la password viene bloccata dopo il ripristino. Per [reimpostare la password](../virtual-machines/linux/classic/reset-access.md)nella macchina virtuale ripristinata usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni per evitare la reimpostazione della password dopo il ripristino.
* Le estensioni presenti durante la configurazione di backup verranno installate, ma non attivate. In caso di problemi, reinstallare le estensioni. 
* Se la macchina virtuale di backup dispone di indirizzo IP statico, dopo il ripristino, la macchina virtuale ripristinata avrà un indirizzo IP dinamico per evitare conflitti durante la creazione della macchina virtuale ripristinata. Altre informazioni su come [aggiungere un indirizzo IP statico alla macchina virtuale ripristinata](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* La macchina virtuale ripristinata non avrà set di disponibilità. È consigliabile usare l'opzione di ripristino dei dischi e [aggiungere il set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) durante la creazione di una macchina virtuale da PowerShell o dei modelli tramite i dischi ripristinati. 


## <a name="backup-for-restored-vms"></a>Backup per le macchine virtuali ripristinate
Se la macchina virtuale è stata ripristinata nello stesso gruppo di risorse con lo stesso nome della macchina virtuale di cui è stato originariamente eseguito il backup, l'operazione di backup continua nella macchina virtuale dopo il ripristino. Se la macchina virtuale è stata ripristinata in un gruppo di risorse diverso o se è stato specificato un nome diverso per la macchina virtuale ripristinata, quest'ultima verrà considerata come nuova ed è necessario configurare il backup per la macchina virtuale ripristinata.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Ripristino di una macchina virtuale durante un'emergenza nel data center di Azure
Backup di Azure consente di ripristinare le macchine virtuali di cui è stato eseguito il backup nel data center associato nel caso in cui nel data center primario in cui le macchine virtuali sono in esecuzione si verifichi un'emergenza e sia stato configurato l'insieme di credenziali di Backup con ridondanza geografica. In tali situazioni è necessario selezionare un account di archiviazione presente nel data center associato. La parte rimanente del processo di ripristino rimane invariata. Backup di Azure usa un servizio di calcolo di un'area geografica per creare la macchina virtuale ripristinata. Altre informazioni sulla [resilienza dei centri dati di Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Ripristino delle macchine virtuali del controller di dominio
L'esecuzione del backup delle macchine virtuali del controller di dominio (DC) è uno scenario supportato da Backup di Azure. Tuttavia è necessario prestare attenzione durante il processo di ripristino. Il processo di ripristino corretto dipende dalla struttura del dominio. Nel caso più semplice, è presente un solo controller di dominio per un singolo dominio. Più comunemente per i carichi di produzione, saranno presenti più controller di dominio per ogni dominio, ad esempio alcuni controller locali. Infine, è possibile avere una foresta con più domini. 

Da una prospettiva Active Directory, la macchina virtuale di Azure è come qualsiasi altra macchina virtuale in un hypervisor moderno supportato. La differenza principale con gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per la macchina virtuale. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali di backup è una sostituzione completa per il ripristino bare metal. È anche disponibile Active Directory Restore Mode (DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. Per altre informazioni di base, vedere [Backup and Restore considerations for virtualized Domain Controllers](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) (Considerazioni sul backup e sul ripristino per i controller di dominio virtualizzati) e [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx) (Pianificazione del ripristino delle foreste di Active Directory).

### <a name="single-dc-in-a-single-domain"></a>Controller di dominio unico in un singolo dominio
La macchina virtuale può essere ripristinata (come qualsiasi altra macchina virtuale) dal portale di Azure o tramite PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Controller di dominio multipli in un singolo dominio
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller nel dominio o viene eseguito un ripristino in una rete isolata, è necessario seguire la procedura di ripristino della foresta.

### <a name="multiple-domains-in-one-forest"></a>Domini multipli in una foresta
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Tuttavia, in tutti gli altri casi è consigliabile il ripristino della foresta.

## <a name="restoring-vms-with-special-network-configurations"></a>Ripristino delle macchine virtuali con configurazioni di rete speciali
È possibile eseguire il backup e ripristinare le macchine virtuali con le configurazioni di rete speciali seguenti. Queste configurazioni richiedono tuttavia alcune considerazioni speciali nel corso del processo di ripristino.

* Macchine virtuali nel servizio di bilanciamento del carico (interno ed esterno)
* Macchine virtuali con più indirizzi IP riservati
* Macchine virtuali con più NIC

> [!IMPORTANT]
> Quando si crea la configurazione di rete speciale per macchine virtuali, è necessario usare PowerShell per il ripristino da disco.
>
>

Per ricreare completamente le macchine virtuali dopo il ripristino su disco, seguire questa procedura:

1. Ripristinare i dischi da un insieme di credenziali dei servizi di ripristino tramite [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)
2. Creare la configurazione della macchina virtuale necessaria per il servizio di bilanciamento del carico/per più NIC/per più indirizzi IP riservati tramite i cmdlet di PowerShell e usarla per creare la macchina virtuale con la configurazione desiderata.

   * Creare una macchina virtuale nel servizio cloud con [bilanciamento del carico interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Creare una macchina virtuale connessa al [servizio di bilanciamento del carico con connessione Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Creare una macchina virtuale con [più NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Creare una macchina virtuale con [più indirizzi IP riservati](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come ripristinare le VM, è possibile vedere l'articolo sulla risoluzione dei problemi per ottenere informazioni sugli errori comuni con le macchine virtuali. Vedere anche l'articolo sulla gestione di attività con le macchine virtuali.

* [Risoluzione dei problemi](backup-azure-vms-troubleshoot.md#restore)
* [Gestire le macchine virtuali](backup-azure-manage-vms.md)

