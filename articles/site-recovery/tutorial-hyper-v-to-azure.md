---
title: Configurare il ripristino di emergenza per macchine virtuali Hyper-V locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in Azure. L'esercitazione interessa sia le macchine virtuali Hyper-V gestite in cloud di System Center Virtual Machine Manager (VMM), sia quelle che non lo sono. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti locali e di Azure
> * Creare un insieme di credenziali dei servizi di ripristino per Site Recovery 
> * Configurare gli ambienti di replica di origine e di destinazione 
> * Impostare il mapping di rete (se Hyper-V è gestito da System Center VMM)
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](concepts-hyper-v-to-azure-architecture.md).
- Esaminare i [requisiti di supporto](site-recovery-support-matrix-to-azure.md) per tutti i componenti.
- Assicurarsi che la macchine virtuali da replicare siano conformi ai [requisiti di Azure per le macchine virtuali](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Calcolare la pianificazione della capacità:
    - Usare lo [strumento di pianificazione della capacità di Hyper-V](http://aka.ms/asr-capacity-planner-excel)per determinare la varianza.
    - Usare [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) per analizzare l'ambiente di origine, stimare la larghezza di banda e i requisiti di destinazione.
- Preparare Azure. Sono necessari una sottoscrizione di Azure, una rete virtuale di Azure e un account di archiviazione di Azure.
- Preparare gli host Hyper-V locali e gli eventuali server VMM.





### <a name="set-up-an-azure-account"></a>Configurare un account Azure

Ottenere un [account Microsoft Azure](http://azure.microsoft.com/).

- È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Altre informazioni sui [prezzi di Site Recovery](site-recovery-faq.md#pricing) e [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).
- Informazioni sulle [aree supportate](https://azure.microsoft.com/pricing/details/site-recovery/) per Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verificare le autorizzazioni dell'account di Azure

Verificare che l'account di Azure disponga delle autorizzazioni necessarie per la replica di macchine virtuali.

- Rivedere le [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessarie per la replica di macchine in Azure
- Verificare e modificare le autorizzazioni per gli [accessi in base al ruolo](../active-directory/role-based-access-control-configure.md). 


### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Configurare una [rete di Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Le VM di Azure create dopo il failover verranno inserite in questa rete.
- La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.


### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

Configurare un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replica le macchine locali in Archiviazione di Azure. Le VM di Azure vengono create dalla risorsa di archiviazione dopo il failover.
- L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
- L'account di archiviazione può essere Standard o [Premium](../storage/common/storage-premium-storage.md).
- Se si configura un account Premium, sarà necessario un altro account Standard per i dati di log.

### <a name="prepare-hyper-v-hosts"></a>Preparare gli host Hyper-V

1. Verificare che gli host Hyper-V soddisfino i [requisiti di supporto](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Assicurarsi che gli host possano accedere a questi URL:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
    - Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

### <a name="prepare-vmm-servers"></a>Preparare i server VMM

Se gli host Hyper-V sono gestiti da VMM, è necessario preparare il server VMM locale. 

- Verificare che il server VMM soddisfi i [requisiti di supporto](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Verificare che il server VMM disponga di almeno un cloud, con uno o più gruppi host. L'host Hyper-V in cui sono in esecuzione le macchine virtuali deve trovarsi nel cloud.
- Il server VMM richiede l'accesso a internet, con accesso agli URL seguenti:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
    - Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).
- Preparare il server VMM per il mapping di rete.


#### <a name="prepare-vmm-for-network-mapping"></a>Preparare VMM per il mapping di rete

Se si usa VMM, il [mapping di rete](site-recovery-network-mapping.md) esegue il mapping tra reti di macchine virtuali VMM locali e reti virtuali di Azure. Il mapping garantisce che le macchine virtuali di Azure siano connesse alla rete giusta quando vengono create dopo il failover.

Preparare VMM per il mapping di rete come segue:

1. Assicurarsi di disporre di una [rete logica VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associata al cloud in cui si trovano gli host Hyper-V.
2. Assicurarsi di disporre di una [rete VM](https://docs.microsoft.com/system-center/vmm/network-virtual) collegata alla rete logica.
3. Connettere le macchine virtuali alla rete VM.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare l'elemento da replicare e la posizione in cui replicarlo.

1. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
2. In **Obiettivo di protezione** selezionare **To Azure (In Azure)**> **Yes, with Hyper-V (Sì, con Hyper-V)**. 
    - Se gli host Hyper-V non sono gestiti da VMM, selezionare **No** per confermare che non si sta usando VMM.
    - Se gli host vengono gestiti in cloud VMM, selezionare **Sì**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, si installano il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure e si registrano i server locali nell'insieme di credenziali. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**. 
    - Se non si utilizza VMM, fare clic su **+Sito Hyper-V**e specificare un nome di sito. Fare quindi clic su **+Server Hyper-V** e aggiungere un host o un cluster al sito.
    - Se si usa VMM, in **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
2. Scaricare il provider e i componenti dell'agente, a seconda dell'ambiente.
    - Solo per Hyper-V, scaricare il file di installazione del provider. Eseguire il file in ogni host Hyper-V per installare sia il Provider che l'agente.
        
        ![Provider senza VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Per Hyper-V con VMM, scaricare il provider e l'agente separatamente. Eseguire l'installazione del provider sul server VMM. Eseguire l'installazione dell'agente in ogni host Hyper-V.
    
        ![Provider e agente con VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.

### <a name="install-components"></a>Installazione dei componenti

Installare i componenti come riepilogato in tabella. 

**Componente** | **Dettagli** | **Solo Hyper-V** | **Hyper-V con VMM**
--- | --- | --- | ---
**Provider di Azure Site Recovery** | Orchestra la replica in Azure | Installare su ogni host Hyper-V | Installare sul server VMM
**Agente di Servizi di ripristino** | Gestisce la replica dei dati | Installare su ogni host Hyper-V | Installare sull'host Hyper-V


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Installare il provider in Hyper-V senza VMM

Installare il provider in ogni host aggiunto al sito Hyper-V. Se l'installazione viene eseguita in un cluster Hyper-V, eseguire il file di installazione in ogni nodo del cluster. L'installazione e la registrazione di ogni nodo del cluster Hyper-V garantisce che le macchine virtuali siano protette anche se ne viene eseguita la migrazione tra i nodi.

1. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
2. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.
4. In **Impostazioni dell'insieme di credenziali** fare clic su **Esplora** per selezionare il file di chiave dell'insieme di credenziali scaricato. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.
5. In **Impostazioni proxy**, specificare in che modo il provider in esecuzione sul server VMM o Hyper-V si connette a Site Recovery tramite Internet.
    * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    * Per il proxy, selezionare **Connetti ad Azure Site Recovery usando un server proxy**. Specificare l'indirizzo proxy, la porta e le credenziali, se necessario.
6. Dopo avere registrato il server nell'insieme di credenziali, fare clic su **Fine**.

I metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **S Infrastruttura di Site Recovery** > **Host Hyper-V**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Installare l'agente di Servizi di ripristino sull'host Hyper-V senza VMM

Se si usa VMM nella distribuzione, eseguire la configurazione dell'agente di Servizi di ripristino in ogni host Hyper-V.

1. In Installazione guidata > **Controllo dei prerequisiti** fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Prerequisiti per l'agente di Servizi di ripristino di Azure](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. In **Impostazioni di installazione** accettare o modificare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Fare clic su **Installa**.
4. In **Installazione**, al termine dell'installazione, fare clic su **Chiudi** per completare la procedura guidata.

##### <a name="set-up-internet-access-via-a-proxy"></a>Configurare l'accesso a internet tramite proxy

L'agente di Servizi di ripristino in esecuzione negli host Hyper-V richiede l'accesso ad Azure tramite Internet per la replica delle macchine virtuali. Se si accede a Internet tramite un proxy, configurarlo come indicato di seguito:

1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nell'host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Configurazione proxy** specificare le informazioni del proxy.

    ![Registrare l'Agente di Servizi di ripristino di Microsoft Azure](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Verificare che l'agente possa raggiungere gli [URL necessari](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Installare il provider nel server VMM (Hyper-V con VMM)

1. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
2. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**. 
3. Registrare adesso il server VMM nell'insieme di credenziali. In **Impostazioni dell'insieme di credenziali** fare clic su **Esplora** per selezionare il file di chiave dell'insieme di credenziali scaricato. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali.

    ![Server registration](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. In **Impostazioni proxy**, specificare in che modo il provider in esecuzione sul server VMM o Hyper-V si connette a Site Recovery tramite Internet.

    * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    * Per il proxy, selezionare **Connetti ad Azure Site Recovery usando un server proxy**. Specificare l'indirizzo proxy, la porta e le credenziali, se necessario.
    - Verrà creato automaticamente un account RunAs di VMM (DRAProxyAccount) con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. È possibile modificare le impostazioni dell'account RunAs nella console di VMM > **Impostazioni** > **Sicurezza** > **Account RunAs**. Riavviare il servizio VMM per aggiornare le modifiche.
5. In **Crittografia dati**, specificare se crittografare tutti i dati inviati ad Azure. Se si seleziona questa opzione, Site Recovery emette un certificato, necessario per decrittografare i dati in un secondo momento.
6. In **Server VMM**, specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM. In **Synchronize cloud metadata with the vault** (Sincronizza i metadati cloud con l'insieme di credenziali), scegliere se sincronizzare i metadati per tutti i cloud presenti nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.






## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione. 

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di Azure dopo il failover. Scegliere il modello di distribuzione da usare in Azure per le macchine virtuali.

3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="configure-network-mapping-with-vmm"></a>Configurare il mapping di rete (con VMM)

Se si usa VMM, configurare il mapping di rete.

1. In **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete** fare clic sull'icona **+Mapping di rete**.
2. In **Aggiungi mapping di rete**, selezionare il server VMM di origine. Selezionare **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**, selezionare la rete VM di origine locale.
5. In **Rete di destinazione**, selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica create dopo il falover. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri.
3. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.

    > [!NOTE]
    >  Quando si esegue la replica in archiviazione Premium la frequenza di 30 secondi non è supportata. Il limite è determinato dal numero di snapshot per BLOB (100) supportato dal servizio di archiviazione Premium. [Altre informazioni](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. In **Conservazione del punto di recupero**, specificare la durata (in ore) dell'intervallo di conservazione per ogni punto di recupero. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V usa due tipi di snapshot:
    - **Snapshot standard**: fornisce uno snapshot incrementale dell'intera macchina virtuale.
    - **Snapshot coerente con l'app**: è uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Il servizio Copia Shadow del volume assicura che lo stato dell'app sia coerente quando viene creato lo snapshot. L'abilitazione di snapshot coerenti con l'app influisce sulle prestazioni dell'app su macchine virtuali di origine. Impostare un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori degli orari di punta.
7. In **Crittografare i dati archiviati in Azure**specificare se crittografare i dati inattivi in Archiviazione di Azure. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM o al sito Hyper-V.

## <a name="enable-replication"></a>Abilitare la replica


1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**. 
2. In **Origine**, selezionare il sito Hyper-V/server VMM/cloud. Fare quindi clic su **OK**.
3. In **Destinazione**, verificare che sia impostato Azure come destinazione e controllare la sottoscrizione dell'insieme di credenziali e il modello che si desidera usare in Azure dopo il failover.
4. Specificare l'account di archiviazione per i dati replicati e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.
5. In **Macchine virtuali** > **Seleziona**, selezionare le macchine virtuali da replicare. Fare quindi clic su **OK**.

 È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

