---
title: Ripristinare una macchina virtuale dal backup | Microsoft Docs
description: Informazioni su come ripristinare una macchina virtuale di Azure da un punto di ripristino
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: ripristinare il backup; come ripristinare; punto di ripristino.
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: 5f6e5dd9d4fb96376762300856b594d772d84af8
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>Ripristinare macchine virtuali in Azure
> [!div class="op_single_selector"]
> * [Ripristinare VM nel portale di Azure](backup-azure-arm-restore-vms.md)
> * [Ripristinare VM nel portale classico](backup-azure-restore-vms.md)
>
>

Ripristinare una macchina virtuale in una nuova dai backup archiviati in un insieme di credenziali di Backup di Azure, seguendo questa procedura.

> [!IMPORTANT]
> È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino. Dopo <br/> il **30 novembre 2017** non sarà più possibile usare PowerShell per creare insiemi di credenziali di backup. <br/> Entro il **30 novembre 2017**:
>- Tutti gli insiemi di credenziali di backup rimanenti verranno aggiornati automaticamente a insiemi di credenziali dei servizi di ripristino.
>- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.
>

## <a name="restore-workflow"></a>Ripristinare un flusso di lavoro
### <a name="step-1-choose-an-item-to-restore"></a>Passaggio 1: Scegliere un elemento da ripristinare
1. Passare alla scheda **Elementi protetti** e selezionare la macchina virtuale che si desidera ripristinare in una nuova macchina virtuale.

    ![Elementi protetti](./media/backup-azure-restore-vms/protected-items.png)

    Nella colonna **Punto di ripristino** della pagina **Elementi protetti** verrà visualizzato il numero di punti di ripristino per una macchina virtuale. Nella colonna **Punto di ripristino più recente** viene visualizzata l'ora del backup più recente da cui è possibile ripristinare una macchina virtuale.
2. Fare clic su **Ripristina** per aprire la procedura guidata **Ripristinare un elemento**.

    ![Ripristina elemento](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>Passaggio 2: Scegliere un punto di ripristino
1. Nella schermata per la **selezione di un punto di ripristino** è possibile eseguire il ripristino dal punto più recente o da un punto precedente. L'opzione predefinita selezionata all'avvio della procedura guidata è *Punto di ripristino più recente*.

    ![selezione di un punto di ripristino](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Per selezionare un punto precedente, scegliere l'opzione **Seleziona data** nell'elenco a discesa e selezionare una data nel controllo calendario facendo clic sull'**icona del calendario**. Nel controllo tutte le date in cui sono presenti punti di ripristino hanno un'ombreggiatura di colore grigio chiaro e sono selezionabili dall'utente.

    ![Selezionare una data](./media/backup-azure-restore-vms/select-date.png)

    Quando si fa clic su una data nel controllo calendario, i punti di ripristino disponibili in quella data verranno visualizzati nella tabella dei punti di ripristino riportata di seguito. Nella colonna **Ora** viene visualizzata l'ora in cui è stato creato lo snapshot. Nella colonna **Tipo** viene visualizzata la [coerenza](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto di ripristino. L'intestazione della tabella mostra il numero di punti di ripristino disponibili per il giorno in parentesi.

    ![Punti di ripristino](./media/backup-azure-restore-vms/recovery-points.png)
3. Selezionare il punto di ripristino dalla tabella **Punti di ripristino** e fare clic sulla freccia Avanti per passare alla schermata successiva.

### <a name="step-3-specify-a-destination-location"></a>Passaggio 3: Specificare un percorso di destinazione
1. Nella schermata per la **selezione di un'istanza di ripristino** specificare i dettagli del percorso in cui ripristinare la macchina virtuale.

   * Specificare il nome della macchina virtuale: in un determinato servizio cloud il nome della macchina virtuale deve essere univoco. Non è supportata la sovrascrittura di macchine virtuali esistenti.
   * Selezionare un servizio cloud per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile scegliere di usare un servizio cloud esistente o di crearne uno nuovo.

        Qualunque sia il nome del servizio cloud scelto, deve essere universalmente univoco. Il nome del servizio cloud è in genere associato a un URL pubblico nella forma di: [serviziocloud].cloudapp.net. Azure non consente di creare un nuovo servizio cloud se il nome è già in uso. Se si sceglie di creare un nuovo servizio cloud, a tale servizio viene assegnato lo stesso nome della macchina virtuale. Il nome della macchina virtuale scelto deve quindi essere sufficientemente univoco per essere applicato al servizio cloud associato.

        Vengono visualizzati soltanto i servizi cloud e le reti virtuali che non sono associati a gruppi di affinità nei dettagli dell'istanza di ripristino. [Altre informazioni](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Selezionare un account di archiviazione per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile selezionare uno degli account di archiviazione esistenti nella stessa area geografica dell'insieme di credenziali di Backup di Azure. Gli account di archiviazione con ridondanza della zona o con archiviazione di tipo Premium non sono supportati.

    Se non è presente alcun account di archiviazione con una configurazione supportata, crearne uno prima di avviare l'operazione di ripristino.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-sa.png)
3. Selezionare una rete virtuale: la rete virtuale (VNET) per la macchina virtuale dovrebbe essere selezionata al momento della creazione della macchina virtuale. L'interfaccia utente di ripristino mostra tutte le reti virtuali all'interno della sottoscrizione che possono essere usate. Non è obbligatorio selezionare una rete virtuale per la macchina virtuale ripristinata: sarà possibile connettersi alla macchina virtuale ripristinata tramite Internet, anche se non viene applicata la rete virtuale.

    Se il servizio cloud selezionato è associato a una rete virtuale, non è possibile modificare la rete virtuale.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Selezionare una subnet: se la rete virtuale dispone di più subnet, per impostazione predefinita verrà selezionata la prima subnet. Scegliere la subnet desiderata tra le opzioni nell'elenco a discesa. Per visualizzare informazioni dettagliate sulla subnet, passare all'estensione Reti nella [home page del portale](https://manage.windowsazure.com/), passare a **Reti virtuali** , selezionare la rete virtuale ed eseguire il drill-down in Configura.

    ![Selezionare una subnet](./media/backup-azure-restore-vms/select-subnet.png)
5. Fare clic sull'icona **Invia** nella procedura guidata per inviare i dettagli e creare un processo di ripristino.

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino
Dopo aver immesso tutte le informazioni nella procedura guidata di ripristino e averle inviate, Backup di Azure tenterà di creare un processo per tenere traccia dell'operazione di ripristino.

![Creazione di un processo di ripristino](./media/backup-azure-restore-vms/create-restore-job.png)

Se la creazione del processo ha esito positivo, verrà visualizzata una notifica di tipo avviso popup di conferma della creazione del processo. È possibile ottenere altri dettagli facendo clic sul pulsante **Visualizza processo** che apre la scheda **Processi**.

![Processo di ripristino creato](./media/backup-azure-restore-vms/restore-job-created.png)

Al termine dell'operazione di ripristino, l'operazione verrà contrassegnata come completata nella scheda **Processi** .

![Processo di ripristino completato](./media/backup-azure-restore-vms/restore-job-complete.png)

Dopo aver ripristinato la macchina virtuale, può essere necessario reinstallare le estensioni esistenti nella macchina virtuale originale e [modificare gli endpoint](../virtual-machines/windows/classic/setup-endpoints.md) per la macchina virtuale nel portale di Azure.

## <a name="post-restore-steps"></a>Operazioni successive al ripristino
Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza, la password verrà bloccata dopo il ripristino. Per [reimpostare la password](../virtual-machines/linux/classic/reset-access.md)nella macchina virtuale ripristinata usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni per evitare la reimpostazione della password dopo il ripristino.

## <a name="backup-for-restored-vms"></a>Backup per le macchine virtuali ripristinate
Se è stata ripristinata una macchina virtuale nello stesso servizio cloud con lo stesso nome usato originariamente per eseguire il backup della VM, il backup continuerà nel post-ripristino della VM. Se è stata ripristinata una macchina virtuale in un servizio cloud diverso o è stato specificato un nome diverso per la VM ripristinata, la VM verrà considerata come nuova ed è necessario configurare il backup per la VM ripristinata.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Ripristino di una macchina virtuale durante un'emergenza del data center di Azure
Backup di Azure consente di ripristinare le macchine virtuali di cui è stato eseguito il backup nel data center associato nel caso in cui nel data center primario in cui le macchine virtuali sono in esecuzione si verifichi un'emergenza e sia stato configurato l'insieme di credenziali di Backup con ridondanza geografica. In tali situazioni, è necessario selezionare un account di archiviazione presente nel data center associato; il resto del processo di ripristino rimane lo stesso. Backup di Azure usa un servizio di calcolo di un'area geografica per creare la macchina virtuale ripristinata. Altre informazioni sulla [resilienza dei centri dati di Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Ripristino delle macchine virtuali del controller di dominio
L'esecuzione del backup delle macchine virtuali del controller di dominio (DC) è uno scenario supportato da Backup di Azure. Tuttavia è necessario prestare attenzione durante il processo di ripristino. Il processo di ripristino corretto dipende dalla struttura del dominio. Nel caso più semplice, è presente un solo controller di dominio per un singolo dominio. Più comunemente per i carichi di produzione, saranno presenti più controller di dominio per ogni dominio, ad esempio alcuni controller locali. Infine, è possibile avere una foresta con più domini.

Da una prospettiva Active Directory, la macchina virtuale di Azure è come qualsiasi altra macchina virtuale in un hypervisor moderno supportato. La differenza principale con gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per la macchina virtuale. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali di backup è una sostituzione completa per il ripristino bare metal. È anche disponibile Active Directory Restore Mode (DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. Per altre informazioni di base, vedere [Backup and Restore considerations for virtualized Domain Controllers](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) (Considerazioni sul backup e sul ripristino per i controller di dominio virtualizzati) e [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx) (Pianificazione del ripristino delle foreste di Active Directory).

### <a name="single-dc-in-a-single-domain"></a>Controller di dominio unico in un singolo dominio
La macchina virtuale può essere ripristinata (come qualsiasi altra macchina virtuale) dal portale di Azure o tramite PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Controller di dominio multipli in un singolo dominio
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller nel dominio o viene eseguito un ripristino in una rete isolata, è necessario seguire la procedura di ripristino della foresta.

### <a name="multiple-domains-in-one-forest"></a>Domini multipli in una foresta
Nel caso in cui sia possibile raggiungere altri controller di dominio dello stesso dominio sulla rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Tuttavia, in tutti gli altri casi è consigliabile il ripristino della foresta.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>Ripristino delle macchine virtuali con configurazioni di rete speciali
Backup di Azure supporta il backup per le configurazioni di rete speciali seguenti delle macchine virtuali.

* Macchine virtuali nel servizio di bilanciamento del carico (interno ed esterno)
* Macchine virtuali con più indirizzi IP riservati
* Macchine virtuali con più NIC

Queste configurazioni rendono necessarie le considerazioni seguenti durante il ripristino.

> [!TIP]
> Usare il flusso di ripristino basato su PowerShell per ricreare la configurazione di rete speciale delle macchine virtuali dopo il ripristino.
>
>

### <a name="restoring-from-the-ui"></a>Ripristino dall'interfaccia utente:
Durante il ripristino dall'interfaccia utente, **scegliere sempre un nuovo servizio cloud**. Poiché il portale accetta solo parametri obbligatori durante il flusso di ripristino, tenere presente che le macchine virtuali ripristinate tramite l'interfaccia utente perderanno la configurazione di rete speciale di cui dispongono. In altre parole, le macchine virtuali ripristinate saranno macchine virtuali normali senza la configurazione del bilanciamento del carico o di più NIC o di più indirizzi IP riservati.

### <a name="restoring-from-powershell"></a>Ripristino da PowerShell:
PowerShell offre la possibilità di ripristinare solo i dischi della macchina virtuale da un backup, senza creare la macchina virtuale. Questa soluzione è utile durante il ripristino di macchine virtuali che richiedono le configurazioni di rete speciali descritte in precedenza.

Per poter ricreare completamente i dischi della macchina virtuale dopo il ripristino, seguire questa procedura:

1. Ripristinare i dischi dall'insieme di credenziali di backup tramite [PowerShell per il Backup di Azure](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Creare la configurazione della macchina virtuale necessaria per il bilanciamento del carico/per più NIC/per più indirizzi IP riservati tramite i cmdlet di PowerShell e usarla per creare la macchina virtuale con la configurazione desiderata.

   * Creare una macchina virtuale nel servizio cloud con [bilanciamento del carico interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Creare una macchina virtuale connessa al [servizio di bilanciamento del carico con connessione Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Creare una macchina virtuale con [più NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Creare una macchina virtuale con [più indirizzi IP riservati](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Passaggi successivi
* [Risoluzione dei problemi](backup-azure-vms-troubleshoot.md#restore)
* [Gestire le macchine virtuali](backup-azure-manage-vms.md)
