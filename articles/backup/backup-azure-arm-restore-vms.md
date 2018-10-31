---
title: 'Backup di Azure: ripristinare macchine virtuali tramite il portale di Azure'
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino con il portale di Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: ripristinare il backup; come ripristinare; punto di ripristino.
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: 450314dddd49825bae689701b694f9a26758835e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377640"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Usare il portale di Azure per ripristinare macchine virtuali
È possibile proteggere i dati mediante la creazione di snapshot dei dati a intervalli definiti. Questi snapshot sono noti come punti di ripristino e vengono archiviati negli insiemi di credenziali dei servizi di ripristino. Se è necessario ripristinare o ricreare una macchina virtuale (VM), è possibile ripristinare la macchina virtuale da qualsiasi punto di ripristino salvato. Quando si ripristina un punto di ripristino, è possibile:

* Creare una nuova VM come rappresentazione temporizzata della VM sottoposta a backup.
* Ripristinare i dischi e usare il modello integrato nel processo per personalizzare la VM ripristinata oppure eseguire un ripristino di singoli file.

In questo articolo viene illustrato come ripristinare una macchina virtuale in una nuova macchina virtuale o ripristinare tutti i dischi su cui è stato eseguito il backup. Per il ripristino dei singoli file, vedere [Ripristinare i file da backup di macchine virtuali di Azure](backup-azure-restore-files-from-vm.md).

![Tre modi per eseguire il ripristino dal backup delle VM](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

Il ripristino di una macchina virtuale o di tutti i dischi dal backup della macchina virtuale prevede due passaggi:

* Selezionare un punto di ripristino.
* Selezionare il tipo di ripristino, creare una nuova VM o ripristinare i dischi e specificare i parametri necessari.

## <a name="select-a-restore-point-for-restore"></a>Selezionare un punto di ripristino
1. Accedere al [portale di Azure](http://portal.azure.com/).

2. Nel menu di Azure selezionare **Tutti i servizi**. Nell'elenco dei servizi digitare **Servizi di ripristino** oppure passare ad **ARCHIVIAZIONE** e selezionare **Insiemi di credenziali di Servizi di ripristino**.

    ![Insieme di credenziali dei servizi di ripristino](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. Viene visualizzato l'elenco di insiemi di credenziali della sottoscrizione.

    ![Elenco di insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Nell'elenco di insiemi di credenziali di Servizi di ripristino selezionare l'insieme di credenziali associato alla macchina virtuale da ripristinare. Quando si seleziona l'insieme di credenziali, viene aperto il dashboard corrispondente.

    ![Insieme di credenziali dei servizi di ripristino selezionato](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. Nel riquadro **Elementi di backup** del dashboard dell'insieme di credenziali selezionare **Macchina virtuale di Azure**.

    ![Dashboard dell'insieme di credenziali](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. Viene aperto il pannello **Elementi di backup** con l'elenco di macchine virtuali di Azure.

    ![Elenco di VM nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Dall'elenco selezionare una VM per aprire il dashboard. Nel dashboard della macchina virtuale viene visualizzata l'area di monitoraggio, che include il riquadro **Punti di ripristino**. Da questo pannello possono essere eseguite tutte le operazioni a livello di macchina virtuale, come **Esegui backup ora**, **Ripristino file** e **Interrompi backup**.
Il ripristino può essere eseguito in diversi modi da questo pannello. Si noti che in questo pannello sono elencati solo i punti di ripristino degli ultimi 30 giorni.

    a) Fare clic con il pulsante destro del mouse sul punto di ripristino in questo pannello (meno di 30 giorni) e avviare **Ripristina macchina virtuale**.

    b) Per recuperare punti di ripristino oltre i 30 giorni è possibile usare il collegamento "Fare clic qui" disponibile nel pannello.

    c) Il comando **Ripristina macchina virtuale** nella barra dei menu offre un'opzione che consente di elencare e filtrare le macchine virtuali in base a date personalizzate.

    Usare il filtro per modificare l'intervallo di tempo dei punti di ripristino visualizzati. Per impostazione predefinita, vengono visualizzati i punti di ripristino di ogni coerenza. Modificare il filtro Tutti i punti di ripristino per selezionare una coerenza specifica dei punti di ripristino. Per altre informazioni sui tipi di punti di ripristino, vedere [Coerenza dei dati](backup-azure-vms-introduction.md#data-consistency).

    Opzioni di coerenza dei punti di ripristino:
    - Punti di ripristino coerenti con l'arresto anomalo del sistema
    - Punti di ripristino coerenti con l'applicazione
    - Punti di ripristino coerenti con il file system
    - Tutti i punti di ripristino

  ![Punti di ripristino](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > Tipo di ripristino indica se il punto di ripristino è nell'account di archiviazione del cliente, nell'insieme di credenziali o in entrambi. Altre informazioni sul [punto di ripristino istantaneo](https://azure.microsoft.com/blog/large-disk-support/).

8. Nel pannello **Ripristina** selezionare **Punto di ripristino**.

    ![Seleziona punto di ripristino](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    Il pannello **Ripristina** indica che il punto di ripristino viene impostato quando si fa clic su **OK**.
9. Se non è già aperto, passare al pannello **Ripristina**. Verificare che [sia selezionato un punto di ripristino](#select-a-restore-point-for-restore) e selezionare **Configurazione di ripristino**. Viene visualizzato il pannello **Configurazione di ripristino**.

## <a name="choose-a-vm-restore-configuration"></a>Scegliere una configurazione di ripristino per la macchina virtuale
Dopo aver selezionato il punto di ripristino, scegliere una configurazione di ripristino della VM. Per configurare la VM ripristinata, è possibile usare il portale di Azure o PowerShell.

1. Se non è già aperto, passare al pannello **Ripristina**. Verificare che [sia selezionato un punto di ripristino](#select-a-restore-point-for-restore) e selezionare **Configurazione di ripristino**. Viene visualizzato il pannello **Configurazione di ripristino**.
2. Questo pannello attualmente ha due opzioni: **Crea nuova**, che corrisponde all'impostazione predefinita, e **Sostituisci esistente**, che consente di eseguire un ripristino sul posto sostituendo solo i dischi e mantenendo le configurazioni e le estensioni esistenti.

> [!NOTE]
> Nei prossimi mesi si prevede che sarà possibile sostituire l'intera macchina virtuale con i dischi, le impostazioni di rete, le configurazioni e le estensioni.
>
>

 Nella sezione **Crea nuova**, che consente di ripristinare i dati in una nuova macchina virtuale o in nuovi dischi, sono disponibili due opzioni:

 ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **Creare la macchina virtuale**
 - **Ripristinare i dischi**

![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

Il portale offre un'opzione di **creazione rapida** per una VM ripristinata. Per personalizzare la configurazione della VM o i nomi delle risorse create come parte della creazione di una nuova opzione di VM, usare PowerShell o il portale per ripristinare i dischi di backup. Usare i comandi di PowerShell per associarli alla scelta della configurazione della VM. In alternativa, è possibile usare il modello integrato nei dischi ripristinati per personalizzare la VM ripristinata. Per informazioni su come ripristinare una VM che dispone di più schede di interfaccia di rete o nel bilanciamento del carico, vedere [Restore a VM with special network configurations](#restore-vms-with-special-network-configurations) (Ripristino di una VM con configurazioni di rete speciali). Se una VM Windows usa le [licenze HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), ripristinare i dischi e usare PowerShell o il modello come specificato in questo articolo per creare la VM. Assicurarsi di specificare **Tipo di licenza** come "Windows_Server" durante la creazione della VM per poter accedere ai vantaggi HUB nella VM ripristinata. Questa operazione può essere eseguita anche in seguito, dopo la creazione della macchina virtuale.

## <a name="create-a-new-vm-from-a-restore-point"></a>Creare una nuova VM da un punto di ripristino
1. Nel pannello **Configurazione di ripristino** menzionato nella sezione precedente specificare o selezionare i valori per ciascuno dei campi seguenti:

    a. **Tipo ripristino**. Creare una macchina virtuale.

    b. **Nome macchina virtuale**. Immettere il nome della macchina virtuale, che non esiste nella sottoscrizione.

    c. **Gruppo di risorse**. Usare un gruppo di risorse esistente oppure crearne uno nuovo. Se si ripristina una VM classica, usare questo campo per specificare il nome di un nuovo servizio cloud. Quando si crea un nuovo gruppo di risorse o un servizio cloud, è necessario che il nome sia univoco a livello globale. Il nome del servizio cloud è in genere associato a un URL pubblico, ad esempio: [serviziocloud].cloudapp.net. Se si prova a usare un nome già usato per il gruppo di risorse cloud/servizio cloud, Azure assegna al gruppo di risorse/servizio cloud lo stesso nome della VM. Azure visualizza i gruppi di risorse/servizi cloud e le VM non associate ai gruppi di affinità. Per altre informazioni, vedere [Come eseguire la migrazione da gruppi di affinità a una rete virtuale (VNet) regionale](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Rete virtuale**. Quando si crea la macchina virtuale, selezionare la rete virtuale. Questo campo indica tutte le reti virtuali associate alla sottoscrizione. Il gruppo di risorse della VM è visualizzato tra parentesi.

    e. **Subnet**. Se la rete virtuale include subnet, la prima subnet viene selezionata per impostazione predefinita. Se sono presenti altre subnet, selezionare quella desiderata.

    f. **Posizione di archiviazione**. Gli account di archiviazione sono nel percorso di gestione temporanea. Questo menu elenca gli account di archiviazione nella stessa posizione dell'insieme di credenziali dei servizi di ripristino. Gli account di archiviazione con ridondanza della zona non sono supportati. Se non sono disponibili account di archiviazione con la stessa posizione dell'insieme di credenziali dei servizi di ripristino, è necessario crearne uno prima di iniziare l'operazione di ripristino. Il tipo di replica dell'account di archiviazione viene visualizzato tra parentesi.

    ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * Una rete virtuale è facoltativa per una macchina virtuale classica e obbligatoria per una macchina virtuale distribuita con Resource Manager.

    > * Il tipo di archiviazione fornito nell'account di archiviazione (Premium o Standard) nel percorso di gestione temporanea stabilisce il tipo di archiviazione del ripristino dei dischi. Attualmente non sono supportati dischi in modalità mista durante il ripristino.
    >
    >

2. Nel pannello **Configurazione di ripristino** selezionare **OK** per portare a termine la configurazione di ripristino. Nel pannello **Ripristino** selezionare **Ripristina** per attivare l'operazione di ripristino.

## <a name="restore-backed-up-disks"></a>Ripristinare i dischi di cui è stato eseguito il backup
Il valore del tipo di ripristino **Ripristino del disco** nel pannello **Configurazione di ripristino** consente di creare una macchina virtuale con configurazioni personalizzate. Durante il ripristino dei dischi, l'account di archiviazione da selezionare deve trovarsi nella stessa posizione dell'insieme di credenziali di Servizi di ripristino. È obbligatorio creare un account di archiviazione, se non ne è disponibile alcuno con la stessa posizione dell'insieme di credenziali dei Servizi di ripristino. Gli account di archiviazione ZRS non sono supportati. Il tipo di replica dell'account di archiviazione viene visualizzato tra parentesi.

Dopo l'operazione di ripristino:
* [Usare il modello per personalizzare la VM ripristinata](#use-templates-to-customize-restore-vm)
* [Usare i dischi ripristinati da collegare a una VM esistente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Creare una nuova VM mediante PowerShell dai dischi ripristinati](./backup-azure-vms-automation.md#restore-an-azure-vm)

Nel pannello **Configurazione di ripristino** selezionare **OK** per portare a termine la configurazione di ripristino. Nel pannello **Ripristino** selezionare **Ripristina** per attivare l'operazione di ripristino.

![Configurazione di ripristino completata](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

Il **ripristino sul posto** viene eseguito tramite la scheda **Sostituisci esistente**.

## <a name="replace-existing-disks-from-a-restore-point"></a>Sostituire i dischi esistenti da un punto di ripristino
L'opzione **Sostituisci esistente** consente di sostituire i dischi esistenti nella macchina virtuale corrente con il punto di ripristino selezionato. Questa operazione può essere eseguita solo se la macchina virtuale corrente esiste. Se è stata eliminata per qualsiasi motivo, questa operazione non può essere eseguita. In alternativa, è consigliabile scegliere **Crea nuova** per creare una nuova macchina virtuale o nuovi dischi per continuare le operazioni di ripristino. Come misura precauzionale, prima di iniziare le operazioni di sostituzione dei dischi esistenti viene eseguito il backup dei dati. Se il punto di ripristino ha più o meno dischi della macchina virtuale corrente, nella macchina virtuale verrà riportato solo il numero di dischi nel punto di ripristino. L'opzione Sostituisci esistente è attualmente supportata solo per i dischi gestiti e le macchine virtuali non crittografate.  

 L'unico input che deve essere selezionato nel pannello **Configurazione di ripristino**è **Percorso di gestione temporanea**.

   ![Configurazione guidata del ripristino - Sostituisci esistente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **Tipo ripristino**. L'opzione Replace Disk(s) (Sostituisci dischi) indica che il punto di ripristino selezionato sostituirà i dischi nella macchina virtuale esistente.

 b. **Percorso di gestione temporanea**. Gli account di archiviazione sono il percorso di gestione temporanea dei dischi gestiti. Questo menu elenca gli account di archiviazione nella stessa posizione dell'insieme di credenziali dei servizi di ripristino. Gli account di archiviazione con ridondanza della zona non sono supportati. Se non sono disponibili account di archiviazione con la stessa posizione dell'insieme di credenziali dei servizi di ripristino, è necessario crearne uno prima di iniziare l'operazione di ripristino. Il tipo di replica dell'account di archiviazione viene visualizzato tra parentesi.

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino
Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenere traccia dell'operazione di ripristino. Il servizio di backup crea anche la notifica e la visualizza temporaneamente nell'area **Notifiche** del portale. Se non viene visualizzata la notifica, selezionare il simbolo **Notifiche** per visualizzare le notifiche.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Fare clic sul collegamento ipertestuale delle notifiche per passare all'elenco **BackupJobs**. Tutti i dettagli delle operazioni relative a un determinato processo sono disponibili negli elenchi **BackupJobs**. È possibile passare a **BackupJobs** dal dashboard dell'insieme di credenziali facendo clic sul riquadro Processi di backup e selezionando **Macchina virtuale di Azure** per visualizzare i processi associati all'insieme di credenziali.

Vengono visualizzati il pannello **Processi di backup** e l'elenco dei processi.

![Elenco di VM in un insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-completed.png)

## <a name="use-templates-to-customize-a-restored-vm"></a>Usare i modelli per personalizzare una VM ripristinata
Al termine dell'[operazione di ripristino dei dischi](#Track-the-restore-operation), usare il modello generato nell'ambito dell'operazione di ripristino per creare una nuova VM con una configurazione diversa dalla configurazione di backup. È anche possibile usarlo per personalizzare i nomi delle risorse create durante il processo di creazione di una nuova VM da un punto di ripristino.

![Drill-down del processo di ripristino](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Per ottenere il modello generato come parte dell'opzione dei dischi di ripristino:

1. Visualizzare i **dettagli del processo di ripristino** corrispondenti al processo.

2. Nella schermata **Ripristino - Dettagli del processo** selezionare **Modello di distribuzione** per avviare la distribuzione del modello.

3. Nel pannello **Modello di distribuzione** per la distribuzione personalizzata usare la distribuzione del modello per [modificare e distribuire il modello](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) o aggiungere altre personalizzazioni [creando un modello](../azure-resource-manager/resource-group-authoring-templates.md) prima della distribuzione.

  ![Caricare la distribuzione del modello](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Dopo aver immesso i valori richiesti, accettare le **Condizioni** e selezionare **Acquista**.

  ![Inviare la distribuzione del modello](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Operazioni successive al ripristino
* Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza la password verrà bloccata dopo il ripristino. Per [reimpostare la password](../virtual-machines/linux/reset-password.md) nella VM ripristinata, usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni per evitare la reimpostazione della password dopo il ripristino.
* Le estensioni presenti durante la configurazione di backup verranno installate, ma non abilitate. Se si verifica un problema, reinstallare le estensioni.
* Se la VM di backup dispone di indirizzo IP statico dopo il ripristino, la VM ripristinata avrà un indirizzo IP dinamico per evitare conflitti durante la creazione di una VM ripristinata. Altre informazioni su come [aggiungere un indirizzo IP statico alla VM ripristinata](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Una VM ripristinata non avrà set di valori di disponibilità. È consigliabile usare l'opzione di ripristino dei dischi per [aggiungere un set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) durante la creazione di una VM da PowerShell o dei modelli con i dischi ripristinati.


## <a name="backup-for-restored-vms"></a>Backup per le macchine virtuali ripristinate
Se una macchina virtuale è stata ripristinata nello stesso gruppo di risorse con lo stesso nome della VM di cui è stato originariamente eseguito il backup, l'operazione di backup continuerà nella VM dopo il ripristino. Se la VM è stata ripristinata in un gruppo di risorse diverso o si è specificato un nome alternativo per la VM ripristinata, la VM viene considerata come nuova. È necessario configurare il backup per la VM ripristinata.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Ripristinare una VM durante un'emergenza nel data center di Azure
Backup di Azure consente di ripristinare le VM di cui è stato eseguito il backup nel data center associato nel caso in cui nel data center primario in cui le VM sono in esecuzione si verifichi un'emergenza e sia stato configurato l'insieme di credenziali di backup con ridondanza geografica. In presenza di questi scenari, selezionare un account di archiviazione, presente in un data center accoppiato. Le fasi rimanenti del processo di ripristino restano invariate. Il backup usa il servizio di calcolo dell'area geografica accoppiata per creare la VM ripristinata. Per altre informazioni, vedere [Azure datacenter resiliency](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) (Resilienza del data center di Azure).

## <a name="restore-domain-controller-vms"></a>Ripristinare le VM del controller di dominio
L'esecuzione del backup delle VM del controller di dominio (DC) è uno scenario supportato dal servizio di backup. Tuttavia è necessario prestare attenzione durante il processo di ripristino. Il processo di ripristino corretto dipende dalla struttura del dominio. Nel caso più semplice, è presente un solo controller di dominio per un singolo dominio. Più comunemente per i carichi di produzione, sono presenti più controller di dominio per ogni dominio, ad esempio alcuni controller locali. Infine, si potrebbe avere una foresta con più domini.

Dalla prospettiva di Active Directory, la VM di Azure è come qualsiasi altra VM in un hypervisor moderno supportato. La differenza principale con gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per VM. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali di backup è una sostituzione completa per il ripristino bare metal. È anche disponibile Modalità ripristino servizi directory (Directory Services Restore Mode, DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. Per altre informazioni, vedere [Backup and restore considerations for virtualized Domain Controllers](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) (Considerazioni sul backup e sul ripristino per i controller di dominio virtualizzati) e [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx) (Pianificazione del ripristino delle foreste di Active Directory).

### <a name="single-dc-in-a-single-domain"></a>Controller di dominio unico in un singolo dominio
La VM può essere ripristinata (come qualsiasi altra VM) dal portale di Azure o con PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Controller di dominio multipli in un singolo dominio
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller nel dominio o viene eseguito un ripristino in una rete isolata, è necessario seguire la procedura di ripristino della foresta.

### <a name="multiple-domains-in-one-forest"></a>Domini multipli in una foresta
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. In tutti gli altri casi è consigliabile il ripristino della foresta.

## <a name="restore-vms-with-special-network-configurations"></a>Ripristinare le VM con configurazioni di rete speciali
È possibile eseguire il backup e ripristinare le VM con le configurazioni di rete speciali seguenti. Queste configurazioni richiedono tuttavia alcune considerazioni speciali nel corso del processo di ripristino:

* VM nei servizi di bilanciamento del carico (interni ed esterni)
* Macchine virtuali con più indirizzi IP riservati
* Macchine virtuali con più NIC

> [!IMPORTANT]
> Quando si crea la configurazione di rete speciale per VM, è necessario usare PowerShell per il ripristino da disco.
>
>

Per ricreare completamente le VM dopo il ripristino su disco, seguire questa procedura:

1. Ripristinare i dischi da un insieme di credenziali dei servizi di ripristino con [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Creare la configurazione della VM necessaria per il servizio di bilanciamento del carico/per più NIC/per più indirizzi IP riservati con i cmdlet di PowerShell. Usarla per creare la VM con la configurazione desiderata:

   a. Creare una VM nel servizio cloud con [bilanciamento del carico interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Creare una VM connessa a un [servizio di bilanciamento del carico con connessione Internet](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Creare una VM [con più NIC](../virtual-machines/windows/multiple-nics.md).

   d. Creare una VM con [più indirizzi IP riservati](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come ripristinare le VM, è possibile vedere l'articolo sulla risoluzione dei problemi per ottenere informazioni sugli errori comuni con le macchine virtuali. Vedere anche l'articolo sulla gestione di attività con le macchine virtuali.

* [Risoluzione dei problemi](backup-azure-vms-troubleshoot.md#restore)
* [Gestire le macchine virtuali](backup-azure-manage-vms.md)
