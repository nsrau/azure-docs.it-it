---
title: Replicare le macchine vistuali Hyper-V in Azure | Microsoft Docs
description: Questo articolo descrive come orchestrare la replica, il failover e il ripristino di macchine virtuali locali Hyper-V in Azure.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: hyper-v-site-walkthrough-overview
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0ef782a7bb7a98da2ec63c91732b3d5ddd959848
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Replicare macchine virtuali Hyper-V (senza Virtual Machine Manager) in Azure usando Azure Site Recovery con il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure classico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Gestione risorse](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Questo articolo illustra come eseguire la replica di macchine virtuali Hyper-V locali in Azure usando [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure. In questa distribuzione le macchine virtuali Hyper-V non vengono gestite tramite la soluzione Virtual Machine Manager (VMM) di System Center.

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Se si desidera eseguire la migrazione di macchine in Azure (senza failback), leggere ulteriori informazioni in [questo articolo](site-recovery-migrate-to-azure.md).



## <a name="deployment-steps"></a>Passaggi di distribuzione

Seguire l'articolo per completare i passaggi di distribuzione seguenti:

1. Ottenere [altre informazioni ](site-recovery-components.md#hyper-v-to-azure) sull'architettura di questa distribuzione. Ottenere inoltre [altre informazioni](site-recovery-hyper-v-azure-architecture.md) sul funzionamento della replica Hyper-V in Site Recovery.
2. Verificare i prerequisiti e le limitazioni.
3. Configurare la rete e gli account di archiviazione di Azure.
4. Preparare gli host Hyper-V.
5. Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.
6. Specificare le impostazioni di origine. Creare un sito Hyper-V che contenga gli host Hyper-V e registrare il sito nell'insieme di credenziali. Installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Microsoft sugli host Hyper-V.
7. Specificare le impostazioni di replica e di destinazione.
8. Abilitare la replica per le VM.
9. Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.



## <a name="prerequisites"></a>Prerequisiti


**Requisito** | **Dettagli** |
--- | --- |
**Azure** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server locali** | Leggere altre informazioni sui requisiti per gli host Hyper-V locali [qui](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager).
**VM Hyper-V locali** | Le VM da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL di Azure** | Gli host Hyper-V devono accedere agli URL seguenti:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).



## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Per preparare la distribuzione è necessario:

1. [Configurare una rete di Azure](#set-up-an-azure-network) in cui verranno collocate le VM di Azure al momento della creazione, dopo il failover.
2. [Configurare un account di archiviazione di Azure](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare gli host Hyper-V](#prepare-the-hyper-v-hosts) e assicurarsi che possano accedere agli URL necessari.

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Configurare una rete di Azure. È necessario configurare una rete di Azure affinché le macchine virtuali di Azure siano connesse a una rete dopo il failover.

* La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
* A seconda del modello di risorsa da usare per le VM di Azure di cui si esegue il failover, la rete di Azure viene configurata in [modalità Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [modalità classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* È consigliabile configurare una rete prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery.
- Gli account di archiviazione usati per Site Recovery non possono essere [spostati](../azure-resource-manager/resource-group-move-resources.md) all'interno della stessa sottoscrizione o tra sottoscrizioni diverse.


### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

- Per contenere i dati replicati in Azure è necessario un account di archiviazione di Azure Standard/Premium. [Archiviazione Premium](../storage/storage-premium-storage.md) viene generalmente usata per le macchine virtuali che necessitano costantemente di prestazioni I/O elevate e bassa latenza per ospitare carichi di lavoro di I/O intensivi.
- Se si vuole usare un account Premium per archiviare i dati replicati, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
- A seconda del modello di risorsa da usare per le macchine virtuali di Azure di cui si esegue il failover, l'account deve essere configurato in [modalità Resource Manager](../storage/storage-create-storage-account.md) o in [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- È consigliabile configurare un account di archiviazione prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- Non è possibile spostare gli account di archiviazione usati da Site Recovery tra gruppi di risorse nella stessa sottoscrizione o in diverse sottoscrizioni.


### <a name="prepare-the-hyper-v-hosts"></a>Preparare gli host Hyper-V

* Assicurarsi che gli host Hyper-V soddisfino i [prerequisiti](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager).
- Assicurarsi che gli host possano accedere agli URL necessari.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Monitoraggio e gestione** > **Backup e Site Recovery (OMS)**.  

    ![Nuovo insieme di credenziali](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. In **Nome**specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionarne una.

4. [Creare un nuovo gruppo di risorse](../azure-resource-manager/resource-group-template-deploy-portal.md) o selezionarne uno esistente e specificare un'area di Azure. I computer verranno replicati in quest'area. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

5. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

    ![Nuovo insieme di credenziali](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Il nuovo insieme di credenziali verrà visualizzato nell'elenco **Dashboard** > **Tutte le risorse** e nel pannello **Insiemi di credenziali dei servizi di ripristino** principale.



## <a name="select-the-protection-goal"></a>Selezionare l'obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali.
2. In **Introduzione** fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con Hyper-V**. Scegliere **No** per confermare che non si sta usando VMM. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il sito Hyper-V, installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure negli host Hyper-V e registrare il sito nell'insieme di credenziali.

1. In **Preparare l'infrastruttura** fare clic su **Origine**. Per aggiungere un nuovo sito Hyper-V come contenitore per i cluster o gli host Hyper-V, fare clic su **+ Sito Hyper-V**.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. In **Crea il sito Hyper-V** specificare un nome per il sito. Fare quindi clic su **OK**. A questo punto, selezionare il sito creato e fare clic su **+Server Hyper-V** per aggiungere un server al sito.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. In **Aggiungi server** > **Tipo di server** verificare che sia visualizzato **Server Hyper-V**.

    - Verificare che il server Hyper-V che si desidera aggiungere sia conforme ai [prerequisiti](#on-premises-prerequisites) e sia in grado di accedere agli URL specificati.
    - Scaricare il file di installazione del provider di Azure Site Recovery. Eseguire questo file per installare il provider e l'agente di Servizi di ripristino in ogni host Hyper-V.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Installare provider e agente

1. Eseguire il file di installazione del provider in ogni host aggiunto al sito Hyper-V. Se l'installazione viene eseguita in un cluster Hyper-V, eseguire il file di installazione in ogni nodo del cluster. L'installazione e la registrazione di ogni nodo del cluster Hyper-V garantisce che le macchine virtuali siano protette anche se ne viene eseguita la migrazione tra i nodi.
2. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
3. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.
4. In **Impostazioni dell'insieme di credenziali** fare clic su **Esplora** per selezionare il file di chiave dell'insieme di credenziali scaricato. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.

    ![Server registration](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. In **Impostazioni proxy** specificare in che modo il provider in esecuzione negli host Hyper-V si connette ad Azure Site Recovery tramite Internet.

    * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    * Se per il proxy esistente è necessaria l'autenticazione o si desidera usare un proxy personalizzato per la connessione al provider, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
    * Se si usa un proxy:
        - Specificare l'indirizzo, la porta e le credenziali.
        - Assicurarsi che sia possibile accedere tramite il proxy agli URL indicati nei [prerequisiti](#prerequisites) .

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'insieme di credenziali.

    ![Percorso di installazione](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Al termine della registrazione, i metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **Site Recovery Infrastructure** >  (Infrastruttura di Site Recovery)**Hyper-V Hosts** (Host Hyper-V).


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Specificare l'account di archiviazione di Azure per la replica e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di Azure dopo il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le macchine virtuali.

3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

    - Se non si dispone di un account di archiviazione, fare clic su **+ Archiviazione** per creare un account basato su Resource Manager inline. Leggere i [requisiti per l'archiviazione](site-recovery-prereq.md#azure-requirements).
    - Se non si dispone di una rete Azure, fare clic su **+Rete** per creare una rete basata su Resource Manager inline.

    ![Archiviazione](./media/site-recovery-vmware-to-azure/enable-rep3.png)




## <a name="configure-replication-settings"></a>Configurare le impostazioni di replica

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. In **Criteri di creazione e associazione**specificare il nome dei criteri.
3. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.

    > [!NOTE]
    > Quando si esegue la replica in archiviazione Premium la frequenza di 30 secondi non è supportata. Il limite è determinato dal numero di snapshot per BLOB (100) supportato dal servizio di archiviazione Premium. [Altre informazioni](../storage/storage-premium-storage.md#snapshots-and-copy-blob).

4. In **Conservazione del punto di recupero** specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. Le macchine virtuali possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app** specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione.

    - Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale.
    - Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot.
    - L'abilitazione di snapshot coerenti con l'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.

6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori dell'orario di lavoro. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando si creano nuovi criteri, questi vengono associati automaticamente al sito Hyper-V. È possibile associare un sito Hyper-V e le VM che contiene a più criteri di replica in **Replica** > nome-criteri > **Associate Hyper-V Site** (Associa sito Hyper-V).

## <a name="capacity-planning"></a>Pianificazione della capacità

Dopo aver configurato l'infrastruttura di base è possibile passare alla pianificazione della capacità e valutare se sono necessarie altre risorse.

In Site Recovery è disponibile lo strumento Capacity Planner, che permette di allocare le risorse appropriate per il calcolo, la rete e l'archiviazione. È possibile eseguire lo strumento di pianificazione in modalità rapida, per ottenere stime basate su un numero medio di macchine virtuali, dischi e risorse di archiviazione oppure in modalità dettagliata, con numeri personalizzati a livello di carico di lavoro. Prima di iniziare è necessario:

* Raccogliere informazioni sull'ambiente di replica, incluse le macchine virtuali, i dischi per ogni macchina virtuale e le risorse di archiviazione per ogni disco.
* Stimare la frequenza di modifica giornaliera (varianza) per i dati replicati. A questo scopo è possibile usare lo strumento [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Vedere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2. Al termine scegliere **Sì** in **Have you run the Capacity Planner**(È stato eseguito lo strumento Capacity Planner?).

   ![Pianificazione della capacità](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

Ulteriori informazioni sul [controllo della larghezza di banda di rete](#network-bandwidth-considerations)



## <a name="enable-replication"></a>Abilitare la replica

Prima di iniziare, verificare che l'account utente di Azure disponga delle [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessarie per abilitare la replica di una nuova macchina virtuale in Azure.

Abilitare la replica per le macchine virtuali nel modo seguente:          

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**. Dopo avere configurato la replica per la prima volta, è possibile fare clic su **+Replica** per abilitare la replica per altri computer.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. In **Origine** selezionare il sito Hyper-V. Fare quindi clic su **OK**.
3. In **Destinazione** selezionare la sottoscrizione dell'insieme di credenziali e il modello di failover da usare in Azure (modalità classica o Resource Manager) dopo il failover.
4. Selezionare l'account di archiviazione da usare. Se non si dispone di un account da usare, è possibile [crearne uno](#set-up-an-azure-storage-account). Fare quindi clic su **OK**.
5. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure create dopo il failover.

    - Per applicare le impostazioni di rete a tutti i computer selezionati per la replica, selezionare **Configurare ora per le macchine virtuali selezionate**.
    - Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
    - Se non si dispone di una rete da usare, è possibile [crearne una](#set-up-an-azure-network). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.

   ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. In **Proprietà** > **Configura proprietà**selezionare il sistema operativo per le VM selezionate e il disco del sistema operativo.
8. Verificare che il nome della VM di Azure (nome di destinazione) sia conforme ai [requisiti per le macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Per impostazione predefinita, tutti i dischi della VM sono selezionati per la replica. È tuttavia possibile cancellarli per escluderli,
    - ad esempio per ridurre la larghezza di banda di replica. È possibile evitare, ad esempio, di replicare i dischi con dati temporanei o dati che vengono aggiornati ad ogni riavvio di un computer o di un'applicazione, come pagefile.sys o tempdb di Microsoft SQL Server. Per escludere un disco dalla replica, è sufficiente selezionarlo.
    - Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere dischi del sistema operativo
    - e non è consigliabile escludere dischi dinamici. Site Recovery non può determinare se un disco rigido virtuale all'interno della macchina virtuale guest è di base o dinamico. Se non sono esclusi tutti i volumi dinamici dipendenti, in caso di failover della VM il disco dinamico protetto risulterà guasto e non sarà possibile accedere ai dati presenti su tale disco.
        - Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si desidera aggiungere o escludere un disco, è necessario disabilitare la protezione della macchina virtuale e riabilitarla al termine dell'operazione.
        - Per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback da Azure a Hyper-V soltanto dei tre dischi. Non è possibile includere nel failback o nella replica inversa da Hyper-V ad Azure i dischi creati manualmente.
        - Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. È possibile in alternativa integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.

10. Fare clic su **OK** per salvare le modifiche. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. In **Impostazioni della replica** > **Configurare le impostazioni di replica** selezionare i criteri di replica da applicare per le VM protette. Fare quindi clic su **OK**. È possibile modificare i criteri di replica in **Criteri di replica** > nome-criterio > **Modifica impostazioni**. Le modifiche applicate verranno usate per i computer di cui è già in corso la replica e per i nuovi computer.


   ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

È consigliabile verificare le proprietà del computer di origine.

1. In **Elementi protetti** fare clic su **Elementi replicati** e selezionare il computer.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai requisiti di Azure. È anche possibile visualizzare e modificare le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP che verranno assegnati alla macchina virtuale di Azure. Tenere presente quanto segue:

   * È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover ha esito negativo. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
   * Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:

     * Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
     * Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
     * Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
     * Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
     * Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.

     ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.

#### <a name="managed-disks"></a>Dischi gestiti

In **Calcolo e rete** > **Proprietà di calcolo** è possibile impostare l'opzione "Usa dischi gestiti" della macchina virtuale su "Sì" per collegare i dischi gestiti al computer in uso al momento della migrazione in Azure. Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure grazie alla gestione degli account di archiviazione associati ai dischi delle macchine virtuali. [Altre informazioni su Managed Disks](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - I dischi gestiti vengono creati e collegati alla macchina virtuale solo in caso di failover in Azure. Abilitando la protezione, i dati verranno comunque replicati dai computer locali agli account di archiviazione.
   È possibile creare dischi gestiti solo per le macchine virtuali distribuite tramite il modello di distribuzione di Resource Manager.

  > [!NOTE]
  > Il failback da Azure all'ambiente Hyper-V locale non è al momento supportato per i computer con dischi gestiti. Impostare l'opzione "Usa dischi gestiti" su "Sì" solo se si prevede di eseguire la migrazione della macchina in Azure.

   - Quando l'opzione "Usa dischi gestiti" viene impostata su "Sì", nel gruppo di risorse è possibile selezionare solo i set di disponibilità con l'opzione "Usa dischi gestiti" impostata su "Sì". Infatti, le macchine virtuali con dischi gestiti possono solo far parte di set di disponibilità la cui proprietà "Usa dischi gestiti" è impostata su "Sì". Verificare di creare set di disponibilità con la proprietà "Usa dischi gestiti" impostata in funzione dell'intenzione di usare i dischi gestiti in caso di failover. Allo stesso modo, quando l'opzione "Usa dischi gestiti" è impostata su "No", nel gruppo di risorse è possibile selezionare solo i set di disponibilità con la proprietà "Usa dischi gestiti" impostata su "No". [Altre informazioni sui dischi gestiti e i set di disponibilità](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Se l'account di archiviazione usato per la replica è stato crittografato con Crittografia del servizio di archiviazione in un qualsiasi momento, la creazione dei dischi gestiti durante il failover non avrà esito positivo. È possibile impostare "Usa dischi gestiti" su "No" e ripetere il failover oppure disabilitare la protezione per la macchina virtuale e proteggerla in un account di archiviazione per il quale non sia attivata la Crittografia del servizio di archiviazione in qualsiasi momento.
  > [Altre informazioni su Crittografia del servizio di archiviazione e dischi gestiti](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>Test della distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale o un piano di ripristino che contenga una o più macchine virtuali.

### <a name="before-you-start"></a>Prima di iniziare

 - Per connettersi alle macchine virtuali di Azure con RDP dopo il failover, seguire le istruzioni per la [preparazione alla connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Per un test completo è necessario copiare Active Directory e DNS nell'ambiente di test. [Altre informazioni](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Per eseguire il failover di una singola macchina, in **Elementi replicati** fare clic sulla VM e quindi sull'icona **+Failover di test**.
2. Per eseguire il failover di un piano di ripristino, in **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi su **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).
3. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
6. Se sono state preparate le connessioni dopo il failover, sarà possibile connettersi alla VM di Azure.
7. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test.

Per altre informazioni, vedere l'articolo [Failover di test in Azure](site-recovery-test-failover-to-azure.md).



## <a name="monitor-the-deployment"></a>Monitorare la distribuzione

Per monitorare le impostazioni di configurazione, lo stato e l'integrità della distribuzione di Site Recovery, seguire questa procedura:

1. Fare clic sul nome dell'insieme di credenziali per accedere al dashboard **Informazioni di base** . In questo dashboard è possibile rilevare i processi di Site Recovery, lo stato della replica, i piani di ripristino, l'integrità del server e gli eventi.  

    ![Informazioni di base](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. Nel riquadro **Integrità** è possibile monitorare i server del sito in cui si verifica il problema e gli eventi generati da Site Recovery nelle ultime 24 ore. Il dashboard Informazioni di base può essere personalizzato con i riquadri e i layout più utili all'utente, incluso lo stato degli insiemi di credenziali di Backup e di Site Recovery.
3. È possibile gestire e monitorare la replica nei riquadri **Elementi replicati**, **Piani di ripristino** e **Processi di Site Recovery**. È possibile eseguire il drill-down dei processi altri dettagli in **Processi** > **Site Recovery Jobs** (Processi di Site Recovery).

## <a name="command-line-provider-and-agent-installation"></a>Installazione del provider e dell'agente dalla riga di comando

Il provider di Azure Site Recovery può essere installato anche usando la riga di comando seguente. Questo metodo può essere usato per installare il provider in un Server Core per Windows Server 2012 R2.

1. Scaricare il file di installazione del provider e il codice di registrazione in una cartella, ad esempio C:\ASR.
2. Da un prompt dei comandi con privilegi elevati eseguire questi comandi per estrarre il programma di installazione del provider:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Eseguire questo comando per installare i componenti:

            C:\ASR> setupdr.exe /i
4. Quindi eseguire questi comandi per registrare il server nell'insieme di credenziali:

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
Dove:

* **/Credentials**: parametro obbligatorio che specifica la posizione in cui si trova il file della chiave di registrazione.  
* **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V visualizzato nel portale di Azure Site Recovery.
* **/proxyAddress**: parametro facoltativo che specifica l'indirizzo del server proxy.
* **/proxyport** : parametro facoltativo che specifica la porta del server proxy.
* **/proxyUsername**: parametro facoltativo che specifica il nome utente proxy, se il proxy richiede l'autenticazione.
* **/proxyPassword**: parametro facoltativo che specifica la password per l'autenticazione nel server proxy, se il proxy richiede l'autenticazione.


## <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete
Lo [strumento Capacity Planner di Hyper-V](site-recovery-capacity-planner.md) può essere usato per calcolare la larghezza di banda necessaria per la replica iniziale e poi quella differenziale. Per controllare la quantità di larghezza di banda usata per la replica è possibile scegliere una delle opzioni seguenti.

* **Limitare la larghezza di banda**: il traffico Hyper-V che viene replicato in Azure passa per un host Hyper-V specifico. È possibile limitare la larghezza di banda nel server host.
* **Perfezionare la larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite una coppia di chiavi del Registro di sistema.

### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda
1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nel server host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'uso della larghezza di banda Internet per le operazioni di backup** e impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Limitare la larghezza di banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

### <a name="influence-network-bandwidth"></a>Influire sulla larghezza di banda di rete
1. Nel Registro di sistema passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM**oppure creare la chiave, se non esiste.
   * Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="next-steps"></a>Passaggi successivi

Al termine della replica iniziale e dei test della distribuzione, è possibile chiamare i failover in caso di necessità. Altre informazioni sui diversi tipi di failover e su come eseguirli sono disponibili [qui](site-recovery-failover.md).

