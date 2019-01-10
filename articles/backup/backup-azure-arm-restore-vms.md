---
title: 'Backup di Azure: Ripristinare macchine virtuali tramite il portale di Azure'
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino con il portale di Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: ripristinare il backup; come ripristinare; punto di ripristino.
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793377"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Usare il portale di Azure per ripristinare macchine virtuali
È possibile proteggere i dati mediante la creazione di snapshot dei dati a intervalli definiti. Questi snapshot sono noti come punti di ripristino e vengono archiviati negli insiemi di credenziali dei servizi di ripristino. Se è necessario ripristinare o ricreare una macchina virtuale (VM), è possibile ripristinare la macchina virtuale da qualsiasi punto di ripristino salvato. Quando si ripristina un punto di ripristino, è possibile:

* Creare una nuova macchina virtuale: da un punto di recupero temporizzato della macchina virtuale sottoposta a backup.
* Ripristinare i dischi: usare il modello integrato nel processo per personalizzare la macchina virtuale ripristinata oppure eseguire il ripristino di singoli file.

In questo articolo viene illustrato come ripristinare una macchina virtuale in una nuova macchina virtuale o ripristinare tutti i dischi su cui è stato eseguito il backup. Per il ripristino dei singoli file, vedere [Ripristinare i file da backup di macchine virtuali di Azure](backup-azure-restore-files-from-vm.md).

![Tre modi per eseguire il ripristino dal backup delle macchine virtuali](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


Il ripristino di una macchina virtuale o di tutti i dischi dal backup della macchina virtuale prevede due passaggi:

* Selezionare un punto di ripristino.
* Selezionare il tipo di ripristino
    - Opzione 1: Creare una nuova macchina virtuale
    - Opzione 2: Ripristinare i dischi

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

    ![Elenco di macchine virtuali nell'insieme di credenziali](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Dall'elenco selezionare una macchina virtuale per aprire il dashboard. Nel dashboard della macchina virtuale viene visualizzata l'area di monitoraggio, che include il riquadro **Punti di ripristino**. Da questo pannello possono essere eseguite tutte le operazioni a livello di macchina virtuale, come **Esegui backup ora**, **Ripristino file** e **Interrompi backup**.
Il ripristino può essere eseguito in diversi modi da questo pannello. Si noti che in questo pannello sono elencati solo i punti di ripristino degli ultimi 30 giorni.

    - Per eseguire il ripristino con un punto di ripristino degli ultimi 30 giorni, fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Ripristina macchina virtuale**.
    - Per eseguire il ripristino con un punto di ripristino anteriore a 30 giorni, fare clic sul collegamento nell'area **Punti di ripristino**.
    - Per elencare e filtrare le macchine virtuali con date personalizzate, fare clic su **Ripristina macchina virtuale** nel menu. Usare il filtro per modificare l'intervallo di tempo per i punti di ripristino visualizzati. È anche possibile filtrare in base a diversi tipi di coerenza dei dati.
8. Rivedere le impostazioni del punto di ripristino:
    - La coerenza dei dati mostra il [tipo di coerenza](backup-azure-vms-introduction.md#consistency-types) nel punto di ripristino.
    - Il **tipo di ripristino** mostra dove è archiviato il punto, ovvero in un account di archiviazione, nell'insieme di credenziali o in entrambi. Vedere altre informazioni sui [punti di ripristino istantanei](https://azure.microsoft.com/blog/large-disk-support/).

  ![Punti di ripristino](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Selezionare un punto di ripristino.

10. Selezionare **Configurazione di ripristino**. Viene visualizzato il pannello **Configurazione di ripristino**.

## <a name="choose-a-vm-restore-configuration"></a>Scegliere una configurazione di ripristino per la macchina virtuale
Dopo aver selezionato il punto di ripristino, scegliere una configurazione di ripristino della VM. Per configurare la VM ripristinata, è possibile usare il portale di Azure o PowerShell.

### <a name="restore-options"></a>Opzioni di ripristino

**Opzione** | **Dettagli**
--- | ---
**Crea nuovo-Crea macchina virtuale** | Questa opzione è equivalente alla creazione rapida di una macchina virtuale. Rende operativa una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> Le impostazioni della macchina virtuale possono essere modificate come parte del processo di ripristino.
**Crea nuovo-Dischi di ripristino** | Crea una macchina virtuale che è possibile personalizzare come parte del processo di ripristino.<br/><br/> Questa opzione copia i dischi rigidi virtuali nell'account di archiviazione specificato.<br/><br/> L'account di archiviazione deve trovarsi nella stessa posizione dell'insieme di credenziali.<br/><br/> Se non si ha un account di archiviazione appropriato, è necessario crearne uno.<br/><br/> Il tipo di replica dell'account di archiviazione è riportato tra parentesi. L'archiviazione con ridondanza della zona non è supportata.<br/><br/> Dall'account di archiviazione è possibile collegare i dischi ripristinati a una macchina virtuale esistente o creare una nuova macchina virtuale dai dischi ripristinati tramite PowerShell.
**Sostituisci esistente** | Con questa opzione non è necessario creare una macchina virtuale.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata non è possibile usare questa opzione.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente e l'archivia nella posizione di gestione temporanea specificata. I dischi esistenti connessi alla macchina virtuale vengono quindi sostituiti con il punto di ripristino selezionato. Lo snapshot creato in precedenza viene copiato nell'insieme di credenziali e archiviato come punto di ripristino in conformità ai criteri di conservazione dei backup.<br/><br/> L'opzione Sostituisci esistente è supportata per le macchine virtuali gestite non crittografate. Non è supportata per i dischi non gestiti, le [macchine virtuali generalizzate](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) o le macchine virtuali [create usando immagini personalizzate](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà quello della macchina virtuale.

> [!NOTE]
> Se si vogliono ripristinare macchine virtuali con impostazioni di rete avanzate, ad esempio se sono gestite da un servizio di bilanciamento del carico interno o esterno o hanno più schede di rete o più indirizzi IP riservati, usare PowerShell per il ripristino. [Altre informazioni](#restore-vms-with-special-network-configurations)
> Se una macchina virtuale Windows usa le [licenze HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), usare l'opzione **Crea nuovo-Dischi di ripristino** e quindi usare PowerShell o il modello di ripristino per creare la macchina virtuale con **Tipo di licenza** impostato su **Windows_Server**. È possibile applicare questa impostazione anche dopo aver creato la macchina virtuale.


Specificare l'impostazione di ripristino nel modo seguente:

1. In **Ripristino** selezionare un [punto di ripristino](#select-a-restore-point-for-restore) > **Configurazione di ripristino**.
2. In **Configurazione di ripristino** selezionare come si vuole ripristinare la macchina virtuale in base alle impostazioni riportate nella tabella precedente.

    ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Crea nuovo-Crea macchina virtuale

1. In **Configurazione di ripristino** > **Crea nuovo** > **Tipo di ripristino** selezionare **Crea macchina virtuale**.
2. In **Nome della macchina virtuale** specificare una macchina virtuale che non è presente nella sottoscrizione.
3. In **Gruppo di risorse** selezionare un gruppo di risorse esistente per la nuova macchina virtuale o crearne uno nuovo con un nome univoco globale. Se si assegna un nome già esistente, Azure assegna al gruppo lo stesso nome della macchina virtuale.
4. In **Rete virtuale** selezionare la rete virtuale in cui verrà inserita la macchina virtuale. Vengono visualizzate tutte le reti virtuali associate alla sottoscrizione. Selezionare la subnet. La prima subnet è selezionata per impostazione predefinita.
5. In **Account di archiviazione** specificare il tipo di archiviazione usato per la macchina virtuale.

    ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.



## <a name="create-new-restore-disks"></a>Crea nuovo-Dischi di ripristino

1. In **Configurazione di ripristino** > **Crea nuovo** > **Tipo di ripristino** selezionare **Dischi di ripristino**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse per i dischi ripristinati o crearne uno nuovo con un nome univoco globale.
3. In **Account di archiviazione** specificare l'account in cui copiare i dischi rigidi virtuali. Verificare che l'account si trovi nella stessa area dell'insieme di credenziali.

    ![Configurazione di ripristino completata](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.
5. Dopo aver ripristinato il disco, è possibile eseguire una delle azioni seguenti per completare l'operazione di ripristino della macchina virtuale.

    - Usare il modello generato come parte dell'operazione di ripristino per personalizzare le impostazioni e attivare la distribuzione della macchina virtuale. Modificare le impostazioni predefinite del modello e inviare il modello per la distribuzione della macchina virtuale.
    - È possibile [collegare i dischi ripristinati](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) a una macchina virtuale esistente.
    - È possibile [creare una nuova macchina virtuale](backup-azure-vms-automation.md#restore-an-azure-vm) dai dischi ripristinati usando PowerShell.


## <a name="replace-existing-disks"></a>Sostituire i dischi esistenti

Usare questa opzione per sostituire i dischi esistenti nella macchina virtuale corrente con il punto di ripristino selezionato.

1. In **Configurazione di ripristino** fare clic su **Sostituisci esistente**.
2. In **Tipo di ripristino** selezionare **Sostituisci dischi** (il punto di ripristino che sostituirà uno o più dischi esistenti della macchina virtuale).
3. In **Posizione per la gestione temporanea** specificare dove salvare snapshot dei dischi gestiti correnti.

   ![Configurazione guidata del ripristino - Sostituisci esistente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino
Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenere traccia dell'operazione di ripristino. Il servizio di backup crea anche la notifica e la visualizza temporaneamente nell'area **Notifiche** del portale. Se non viene visualizzata la notifica, selezionare il simbolo **Notifiche** per visualizzare le notifiche.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Fare clic sul collegamento ipertestuale delle notifiche per passare all'elenco **BackupJobs**. Tutti i dettagli delle operazioni relative a un determinato processo sono disponibili negli elenchi **BackupJobs**. È possibile passare a **BackupJobs** dal dashboard dell'insieme di credenziali facendo clic sul riquadro Processi di backup e selezionando **Macchina virtuale di Azure** per visualizzare i processi associati all'insieme di credenziali.

Vengono visualizzati il pannello **Processi di backup** e l'elenco dei processi.

![Elenco di macchine virtuali in un insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

Nel pannello **Dettagli del ripristino** è ora disponibile l'**indicatore di stato**. Il pannello **Dettagli del ripristino** può essere aperto facendo clic su qualsiasi processo di ripristino con stato **In corso**. L'**indicatore di stato** è disponibile in tutte le varianti delle operazioni di ripristino, ad esempio **Crea nuovo**, **Dischi di ripristino** e **Sostituisci esistente**. Nell'indicatore di stato sono riportati i dettagli relativi al **tempo di ripristino stimato**, alla **percentuale di completamento del ripristino** e al **numero di byte trasferiti**.

Di seguito sono descritti i dettagli riportati nell'indicatore di stato dell'operazione di ripristino:

- Il **tempo di ripristino stimato** indica inizialmente il tempo impiegato per il completamento dell'operazione di ripristino. Con il progressivo avanzamento dell'operazione, il tempo impiegato si riduce fino a raggiungere il valore 0 quando l'operazione di ripristino viene completata.
- La **percentuale di ripristino** fornisce i dati sulla percentuale di completamento dell'operazione di ripristino.
- Il **numero di byte trasferiti** è disponibile nell'attività secondaria quando il ripristino viene eseguito tramite Crea macchina virtuale. Indica il numero di byte trasferiti rispetto al numero totale di byte da trasferire.


## <a name="use-templates-to-customize-a-restored-vm"></a>Usare i modelli per personalizzare una macchina virtuale ripristinata
Al termine dell'[operazione di ripristino dei dischi](#Track-the-restore-operation), usare il modello generato nell'ambito dell'operazione di ripristino per creare una nuova macchina virtuale con una configurazione diversa dalla configurazione di backup. È anche possibile usarlo per personalizzare i nomi delle risorse create durante il processo di creazione di una nuova VM da un punto di ripristino.

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
