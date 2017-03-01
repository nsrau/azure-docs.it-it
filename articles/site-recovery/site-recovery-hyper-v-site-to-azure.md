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
ms.date: 02/19/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 080dce21c2c803fc05c945cdadb1edd55bd7fe1c
ms.openlocfilehash: 266b9d0e36aa5e3bef9b689e7602449fbe86b3cd
ms.lasthandoff: 02/22/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Replicare macchine virtuali Hyper-V (senza Virtual Machine Manager) in Azure usando Azure Site Recovery con il portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure classico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Gestione risorse](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Questo articolo illustra come eseguire la replica di macchine virtuali Hyper-V locali in Azure usando [Azure Site Recovery[](site-recovery-overview.md) nel portale di Azure.

La replica e il failover delle macchine virtuali Hyper-V vengono eseguiti nell'archiviazione di Azure se il sito primario diventa non disponibile. È possibile accedere ai carichi di lavoro in Azure ed eseguire il failback in locale quando il funzionamento torna normale. Le istruzioni contenute in questo articolo sono utili anche per migrare le macchine virtuali in Azure. In una migrazione la replica e il failover vengono eseguiti nelle macchine virtuali, senza poi eseguire il failback.

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="scenario-architecture"></a>Architettura dello scenario
Componenti dello scenario:

* **Host o cluster Hyper-V**: cluster o server host Hyper-V locali. Gli host Hyper-V che eseguono le macchine virtuali da proteggere sono raggruppati in siti Hyper-V logici durante la distribuzione di Site Recovery.
* **Provider di Azure Site Recovery e agente di Servizi di ripristino**: durante la distribuzione, vengono installati il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Microsoft Azure nei server host Hyper-V. Il provider con Azure Site Recovery tramite HTTPS 443 per eseguire la replica dell'orchestrazione. L'agente nel server host Hyper-V replica i dati in Archiviazione di Azure tramite HTTPS 443 per impostazione predefinita.
* **Azure**: è necessario avere una sottoscrizione di Azure, un account di archiviazione di Azure per l'archiviazione dei dati replicati e una rete virtuale di Azure in modo che le macchine virtuali di Azure siano connesse a una rete dopo il failover.

![Architettura del sito Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)

## <a name="azure-prerequisites"></a>Prerequisiti di Azure


| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Account di Azure** | Account di [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. |
| **Archiviazione di Azure** | Account di archiviazione standard. È possibile usare un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. [Altre informazioni](../storage/storage-redundancy.md)<br/><br/> L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.<br/><br/> L'account di archiviazione Premium non è attualmente supportato.<br/><br/> I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover.<br/><br/> [Altre informazioni](../storage/storage-introduction.md) sul servizio di archiviazione di Azure. |
| **Rete di Azure** |È necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando si verifica il failover. La rete virtuale di Azure deve risiedere nella stessa area dell'insieme di credenziali di Servizi di ripristino. |

## <a name="on-premises-prerequisites"></a>Prerequisiti locali
Elementi necessari in locale:

| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Hyper-V** |Uno o più server locali in cui è in esecuzione **Windows Server 2012 R2** con tutti gli aggiornamenti più recenti e il ruolo Hyper-V abilitato o **Microsoft Hyper-V Server 2012 R2**.<br/><br/>Il server Hyper-V deve contenere una o più macchine virtuali<br/><br/>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br/><br/>Nei server Hyper-V devono essere state installate le correzioni indicate nell'articolo [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") della Microsoft Knowledge Base. |
| **Provider e agente** |Durante la distribuzione di Azure Site Recovery verrà installato il provider di Azure Site Recovery. Durante l'installazione del provider verrà installato anche l'agente di Servizi di ripristino di Azure in ogni server Hyper-V che esegue le macchine virtuali da proteggere. In tutti i server Hyper-V in un insieme di credenziali di Site Recovery devono essere installate le stesse versioni del provider e dell'agente.<br/><br/>Il provider dovrà connettersi ad Azure Site Recovery tramite Internet. Il traffico può essere inviato direttamente oppure tramite un proxy. Si noti che il proxy basato su HTTPS non è supportato. Il server proxy deve consentire l'accesso a:  <br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.hypervrecoverymanager.windowsazure.com`` <br/><br/> ``*store.core.windows.net``<br/><br/> ``*.blob.core.windows.net``<br/><br/> ``https://www.msftncsi.com/ncsi.txt``<br/><br/> ``time.windows.com``<br/><br/> ``time.nist.gov``<br/><br/> Se nel server sono disponibili regole firewall basate sull'indirizzo IP, verificare che le regole consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali. |

## <a name="virtual-machine-prerequisites"></a>Prerequisiti delle macchine virtuali
| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Macchine virtuali protette** |Prima eseguire il failover di una macchina virtuale è necessario assicurarsi che il nome che verrà assegnato alla macchina virtuale di Azure sia conforme ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). È possibile modificare il nome dopo aver abilitato la replica per la VM.<br/><br/> La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. Una macchina virtuale può avere fino a 64 dischi (quindi fino a 64 TB).<br/><br/> I cluster guest in dischi condivisi non sono supportati.<br/><br/> Se la VM di origine dispone del gruppo NIC, questo viene convertito in una singola scheda NIC dopo il failover in Azure.<br/><br/>La protezione di macchine virtuali che eseguono Linux con un indirizzo IP statico non è supportata. |

## <a name="prepare-for-deployment"></a>Preparare la distribuzione
Per preparare la distribuzione è necessario:

1. [Configurare una rete di Azure](#set-up-an-azure-network) in cui verranno collocate le VM di Azure al momento della creazione, dopo il failover.
2. [Configurare un account di archiviazione di Azure](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare gli host Hyper-V](#prepare-the-hyper-v-hosts) e assicurarsi che possano accedere agli URL necessari.

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Configurare una rete di Azure. È necessario configurare una rete di Azure affinché le macchine virtuali di Azure siano connesse a una rete dopo il failover.

* La rete deve trovarsi nella stessa area della rete in cui viene distribuito l'insieme di credenziali di Servizi di ripristino.
* A seconda del modello di risorsa da usare per le VM di Azure di cui si esegue il failover, la rete di Azure deve essere configurata in [modalità Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [modalità classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* È consigliabile configurare una rete prima di iniziare. In caso contrario, sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery.

> [!NOTE]
> La [migrazione di reti](../azure-resource-manager/resource-group-move-resources.md) all'interno dei gruppi di risorse con la stessa sottoscrizione o all'interno delle sottoscrizioni non è supportata per le reti usate per la distribuzione di Site Recovery.
>
>

### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

- Per contenere i dati replicati in Azure è necessario un account di archiviazione di Azure Standard.
- A seconda del modello di risorsa da usare per le macchine virtuali di Azure di cui si esegue il failover, si configurerà l'account in [modalità Resource Manager](../storage/storage-create-storage-account.md) o in [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- È consigliabile configurare un account di archiviazione prima di iniziare. In caso contrario, sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery. L'account deve risiedere nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- Non è possibile spostare gli account di archiviazione usati da Site Recovery tra gruppi di risorse nella stessa sottoscrizione o in diverse sottoscrizioni.


### <a name="prepare-the-hyper-v-hosts"></a>Preparare gli host Hyper-V
* Assicurarsi che gli host Hyper-V siano conformi ai [prerequisiti](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Gestione** > **Backup e Site Recovery (OMS)**. In alternativa, è possibile fare clic su **Esplora** > **Servizi di ripristino** insieme di credenziali > **Aggiungi**.

    ![Nuovo insieme di credenziali](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)
3. In **Nome** specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionarne una.
4. [Creare un nuovo gruppo di risorse](../azure-resource-manager/resource-group-template-deploy-portal.md) o selezionarne uno esistente e specificare un'area di Azure. I computer verranno replicati in quest'area. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea insieme di credenziali**.

    ![Nuovo insieme di credenziali](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Il nuovo insieme di credenziali verrà visualizzato in **Dashboard** > **Tutte le risorse** e nel pannello **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="get-started"></a>Introduzione
Le attività iniziali di Site Recovery consentono di eseguire la distribuzione molto rapidamente. Permettono di verificare i prerequisiti e illustrano i passaggi della distribuzione di Site Recovery nell'ordine corretto.

Nelle attività iniziali viene selezionato il tipo di computer da replicare e la posizione in cui eseguire la replica. Vengono configurati i server locali, gli account di archiviazione di Azure e le reti. Vengono creati i criteri di replica e si esegue la pianificazione della capacità. Dopo aver configurato l'infrastruttura viene abilitata la replica per le macchine virtuali. È possibile eseguire failover per computer specifici o creare piani di ripristino per eseguire il failover di più macchine.

Per avviare le attività iniziali, scegliere la modalità di distribuzione di Site Recovery. Il flusso delle attività iniziali è leggermente diverso a seconda dei requisiti di replica.

## <a name="step-1-choose-your-protection-goals"></a>Passaggio 1: Scegliere gli obiettivi della protezione
Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Nel pannello **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Impostazioni** > **Attività iniziali** fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con Hyper-V**. Scegliere **No** per confermare che non si sta usando VMM. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine
Configurare il sito Hyper-V, installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino negli host Hyper-V e registrare gli host nell'insieme di credenziali.

1. Fare clic su **Passaggio 2: Preparare l'infrastruttura** > **Origine**. Per aggiungere un nuovo sito Hyper-V come contenitore per i cluster o gli host Hyper-V, fare clic su **+ Sito Hyper-V**.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. Nel pannello **Crea il sito Hyper-V** specificare un nome per il sito. Fare quindi clic su **OK**. Selezionare il sito appena creato.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)
3. Fare clic su **+ Server Hyper-V** per aggiungere un server al sito.
4. In **Aggiungi server** > **Tipo di server** verificare che sia disponibile **Server Hyper-V**. Assicurarsi che il server Hyper-V da aggiungere sia conforme ai [prerequisiti](#on-premises-prerequisites) e che possa accedere agli URL specificati.
5. Scaricare il file di installazione del provider di Azure Site Recovery. Sarà necessario eseguire questo file per installare sia il provider che l'agente di Servizi di ripristino in ogni host Hyper-V.
6. Scaricare la chiave di registrazione, che sarà necessaria durante l'installazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)
7. Eseguire il file di installazione del provider in ogni host aggiunto al sito Hyper-V. Se l'installazione viene eseguita in un cluster Hyper-V, eseguire il file di installazione in ogni nodo del cluster. L'installazione e la registrazione di ogni nodo del cluster Hyper-V garantisce che le macchine virtuali rimangano protette anche se ne viene eseguita la migrazione tra i nodi.

### <a name="install-the-provider-and-agent"></a>Installare provider e agente
1. Eseguire il file di installazione del provider.
2. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
3. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.
4. Nella pagina **Impostazioni dell'insieme di credenziali** fare clic su **Esplora** per selezionare il file di chiave dell'insieme di credenziali scaricato. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.

    ![Server registration](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. In **Impostazioni proxy** specificare in che modo il provider che verrà installato sul server si connetterà ad Azure Site Recovery tramite Internet.

    * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente senza un proxy**.
    * Per connettersi al proxy attualmente configurato nel server, selezionare **Connetti con le impostazioni proxy esistenti**.
    * Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connetti con le impostazioni proxy personalizzate**.
    * Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali
    * Se si usa un proxy, assicurarsi che sia possibile accedere agli URL indicati nei [prerequisiti](#on-premises-prerequisites) .

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'insieme di credenziali.

    ![Percorso di installazione](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Al termine della registrazione, i metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato nel pannello **Impostazioni** > **Infrastruttura di Site Recovery** > **Host Hyper-V**.

### <a name="command-line-installation"></a>Installazione dalla riga di comando
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

## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Specificare l'account di archiviazione di Azure da usare per la replica e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e quindi selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le macchine virtuali sottoposte a failover. Scegliere il modello di distribuzione che si vuole usare in Azure (classica o con Resource Manager) per il failover delle macchine virtuali.

3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

      ![Archiviazione](./media/site-recovery-vmware-to-azure/enable-rep3.png))


4. Per creare un account di archiviazione con Resource Manager, se non è già stato fatto, fare clic su **+ Account di archiviazione** per eseguire l'operazione inline. Nel pannello **Crea account di archiviazione** specificare il nome, il tipo, la sottoscrizione e la località dell'account. L'account deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

      ![Archiviazione](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)


Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/storage-create-storage-account-classic-portal.md).


Per creare una rete di Azure con Resource Manager, se non è già stato fatto, fare clic su **+ Rete** per eseguire l'operazione inline. Nel pannello **Crea rete virtuale** specificare il nome, l'intervallo di indirizzi, i dettagli della subnet, la sottoscrizione e la località della rete. La rete deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

   ![Rete](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

Per creare una rete con il modello di distribuzione classica è necessario usare [il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica
1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. In **Creare e associare i criteri** specificare il nome dei criteri.
3. In **Frequenza di copia** specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.
4. In **Conservazione del punto di ripristino**specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app** specificare la frequenza, da&1; a&12; ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. Si noti che un'eventuale abilitazione di snapshot coerenti dell'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.
6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori dell'orario di lavoro. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando si creano nuovi criteri, questi vengono associati automaticamente al sito Hyper-V. Fare clic su **OK**. È possibile associare un sito Hyper-V e le VM che contiene a più criteri di replica in **Impostazioni** > **Replica**nome criteri **Associa sito Hyper-V**.

## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione della capacità
Dopo aver configurato l'infrastruttura di base è possibile passare alla pianificazione della capacità e valutare se sono necessarie altre risorse.

In Site Recovery è disponibile lo strumento Capacity Planner, che permettere di allocare le risorse appropriate all'ambiente di origine, i componenti di Site Recovery, la rete e l'archiviazione. Lo strumento di pianificazione può essere eseguito in modalità rapida, per ottenere stime basate su un numero medio di macchine virtuali, dischi e risorse di archiviazione, oppure in modalità dettagliata, in cui è necessario inserire le cifre a livello di carico di lavoro. Prima di iniziare è necessario:

* Raccogliere informazioni sull'ambiente di replica, incluse le macchine virtuali, i dischi per ogni macchina virtuale e le risorse di archiviazione per ogni disco.
* Stimare la frequenza di modifica giornaliera (varianza) prevista per i dati replicati. A questo scopo è possibile usare lo strumento [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Vedere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2. Al termine scegliere **Sì** in **Have you run the Capacity Planner**(È stato eseguito lo strumento Capacity Planner?).

   ![Pianificazione della capacità](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete
Lo strumento Capacity Planner può essere usato per calcolare la larghezza di banda necessaria per la replica iniziale e poi quella differenziale. Per controllare la quantità di larghezza di banda usata per la replica è possibile scegliere una delle opzioni seguenti.

* **Limitare la larghezza di banda**: il traffico Hyper-V che viene replicato in Azure passa per un host Hyper-V specifico. È possibile limitare la larghezza di banda nel server host.
* **Perfezionare la larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite una coppia di chiavi del Registro di sistema.

#### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda
1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nel server host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'uso della larghezza di banda Internet per le operazioni di backup** e impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Limitare la larghezza di banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

#### <a name="influence-network-bandwidth"></a>Influire sulla larghezza di banda di rete
1. Nel Registro di sistema passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM**oppure creare la chiave, se non esiste.
   * Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="step-6-enable-replication"></a>Passaggio 6: Abilitare la replica
Per abilitare la replica, procedere come descritto di seguito.

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo aver abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altri computer.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. Nel pannello **Origine** selezionare il sito Hyper-V. Fare quindi clic su **OK**.
3. In **Destinazione** selezionare la sottoscrizione dell'insieme di credenziali e il modello di failover da usare in Azure (modalità classica o Resource Manager) dopo il failover.
4. Selezionare l'account di archiviazione da usare. Per usare un account di archiviazione diverso da quelli disponibili, è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un account di archiviazione con il modello di distribuzione di Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/storage-create-storage-account-classic-portal.md). Fare quindi clic su **OK**.
5. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure attivate dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se si vuole usare una rete diversa da quelle disponibili, è possibile [crearne una](#set-up-an-azure-network). Per creare una rete con il modello di distribuzione di Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.
   ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)
7. In **Proprietà** > **Configura proprietà**selezionare il sistema operativo per le VM selezionate e il disco del sistema operativo. Per impostazione predefinita, tutti i dischi della VM sono selezionati per la replica. Può essere necessario escludere dischi dalla replica per ridurre il consumo di larghezza di banda per la replica di dati non necessari in Azure. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ad ogni riavvio di un computer o un'applicazione come pagefile.sys o tempdb di Microsoft SQL Server. Per escludere un disco dalla replica, è sufficiente selezionarlo. Verificare che il nome della VM di Azure (nome di destinazione) sia conforme ai [requisiti per le macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) e, se necessario, modificarlo. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

     ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

     > [!NOTE]
     >
     > * Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere il disco del sistema operativo e non è consigliabile escludere i dischi dinamici. Site Recovery non può determinare se un disco rigido virtuale all'interno della macchina virtuale guest è di base o dinamico.  Se non sono esclusi tutti i volumi dinamici dipendenti, in caso di failover della VM il disco dinamico protetto risulterà guasto e non sarà possibile accedere ai dati presenti su tale disco.
    > * Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si vuole aggiungere o escludere un disco, è necessario disabilitare la protezione per la macchina virtuale e quindi riabilitarla.
    > * Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. In alternativa, è possibile integrare Automazione di Azure
    > * in un piano di ripristino per creare il disco durante il failover del computer.
    > * Per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback da Azure a Hyper-V soltanto dei tre dischi. Non è possibile includere nel failback o nella replica inversa da Hyper-V ad Azure i dischi creati manualmente.
    >
    >       

8. In **Impostazioni della replica** > **Configurare le impostazioni di replica** selezionare i criteri di replica da applicare per le VM protette. Fare quindi clic su **OK**. È possibile modificare i criteri di replica in **Impostazioni** > **Criteri di replica** > nome dei criteri > **Modifica impostazioni**. Le modifiche applicate verranno usate per i computer di cui è già in corso la replica e per i nuovi computer.

   ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale
È consigliabile verificare le proprietà del computer di origine.

1. Fare clic su **Impostazioni** > **Elementi protetti** > **Elementi replicati** e selezionare la macchina virtuale.

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



### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover
Per connettersi alle macchine virtuali di Azure con RDP dopo il failover, seguire questa procedura:

**Nel computer locale prima del failover**:

* Per l'accesso tramite Internet, abilitare RDP e assicurarsi che vengano aggiunte regole TCP e UDP per **Internet pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** -> **App e funzionalità consentite** per tutti i profili.
* Per l'accesso tramite una connessione da sito a sito, abilitare RDP nel computer e assicurarsi che il protocollo RDP sia consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio** e **private**.
* Installare l' [agente della VM di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) nel computer locale.
* Verificare che il criterio SAN del sistema operativo sia impostato su OnlineAll. [Altre informazioni](https://support.microsoft.com/kb/3031135)
* Disabilitare il servizio IPSec prima di eseguire il failover.

**Nella VM di Azure dopo il failover**:

* Aggiungere un indirizzo IP pubblico alla scheda di rete associata alla macchina virtuale di Azure per consentire il protocollo RDP.
* Assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale tramite un indirizzo pubblico.
* Provare a connettersi. Se non è possibile connettersi verificare che la macchina virtuale sia in esecuzione. Per altri suggerimenti sulla risoluzione dei problemi, vedere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Per accedere a una macchina virtuale di Azure che esegue Linux dopo il failover con un client Secure Shell (ssh), seguire questa procedura:

**Nel computer locale prima del failover**:

* Assicurarsi che il servizio Secure Shell nella macchina virtuale di Azure sia impostato per l'avvio automatico all'avvio del sistema.
* Verificare che le regole firewall accettino la connessione SSH.

**Nella VM di Azure dopo il failover**:

* Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.
* È necessario creare un endpoint pubblico per consentire le connessioni in ingresso sulla porta SSH, che per impostazione predefinita è la porta TCP 22.
* Se la macchina virtuale è accessibile tramite una connessione VPN, ExpressRoute o VPN da sito a sito, il client può essere usato per connettersi direttamente alla macchina virtuale tramite SSH.

## <a name="step-7-run-a-test-failover"></a>Passaggio 7: Eseguire un failover di test
Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale o un piano di ripristino che contenga una o più macchine virtuali.

1. Per eseguire il failover di una singola macchina, in **Impostazioni** > **Elementi replicati** fare clic sulla VM > sull'icona ** +Failover di test**.
2. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).
3. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Impostazioni** > **Processi** > **Processi di Site Recovery**.
5. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
6. Se sono state [preparate le connessioni dopo il failover](#prepare-to-connect-to-azure-vms-after-failover), sarà possibile connettersi alla VM di Azure.
7. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test.

Per altri dettagli, vedere il documento relativo al [failover di test in Azure](site-recovery-test-failover-to-azure.md).


## <a name="failover"></a>Failover
Al termine della replica iniziale per le macchine, è possibile chiamare i failover in caso di necessità. Site Recovery supporta vari tipi di failover: failover di test, failover pianificato e failover non pianificato.
[Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e una descrizione dettagliata di come e quando eseguire ognuno di essi.

> [!NOTE]
> Se si intende eseguire la migrazione di macchine virtuali in Azure, si consiglia fortemente di ricorrere a un'[operazione di Failover pianificato](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary). Dopo aver convalidato l'applicazione migrata in Azure tramite il failover di test, attenersi ai passaggi illustrati in [Completa migrazione](#Complete-migration-of-your-virtual-machines-to-Azure) per portare a compimento la migrazione delle macchine virtuali. Non è necessario eseguire un'istruzione Commit o Delete. La funzione Completa migrazione permette di portare a termine la migrazione, rimuove la protezione per la macchina virtuale e interrompe la fatturazione di Azure Site Recovery per la macchina.


### <a name="run-a-planned-failover"></a>Eseguire un failover pianificato
È consigliabile ricorrere a questa operazione per soddisfare i requisiti di conformità o durante la manutenzione pianificata, per eseguire il failover dei dati in modo da mantenere i carichi di lavoro in esecuzione in caso di interruzioni note, ad esempio per un'interruzione dell'alimentazione prevista o bollettini meteo negativi. Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda Macchine virtuali. Prima di iniziare verificare che tutte le macchine virtuali di cui eseguire il failover abbiano completato la replica iniziale.

1. Selezionare **Piani di ripristino > nome_pianodiripristino**.
2. Nel pannello del piano di ripristino fare clic su **Failover pianificato**.
3. Nella pagina **Conferma failover pianificato **selezionare il percorso di origine e destinazione.
4. Quando si avvia un failover pianificato, il primo passaggio è l’arresto delle macchine virtuali per garantire che non si verifichino perdite di dati. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. Se si verifica un errore nel failover (in una macchina virtuale o in uno script incluso nel piano di ripristino), il failover pianificato di un piano di ripristino si interrompe. È possibile avviare nuovamente il failover.
6. Dopo la replica le macchine virtuali create sono in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
7. Dopo il completamento della replica, le macchine virtuali vengono avviate nella località secondaria.


### <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato
Si dovrebbe ricorrere a tale operazione quando un sito primario diventa inaccessibile a causa di un evento imprevisto, ad esempio un’interruzione dell’alimentazione o un attacco di virus. Questa procedura descrive come eseguire un failover non pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda Macchine virtuali. Prima di iniziare verificare che tutte le macchine virtuali di cui eseguire il failover abbiano completato la replica iniziale.

1. Selezionare **Piani di ripristino > nome_pianodiripristino**.
2. Nel pannello relativo al piano di ripristino, fare clic su **Failover non pianificato**.
3. Nella pagina **Failover non pianificato** selezionare il percorso di origine e destinazione.
4. Selezionare **Arresta le macchine virtuali e sincronizza i dati più recenti** per specificare che Site Recovery deve tentare di arrestare le macchine virtuali protette e sincronizzare i dati in modo che venga eseguito il faiolver dei dati più recenti.
5. Dopo il failover le macchine virtuali sono uno stato di attesa di commit.  Fare clic su **Commit** per eseguire il commit del failover.

[Altre informazioni](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Completare la migrazione delle macchine virtuali in Azure
> [!NOTE]
> I passaggi seguenti si applicano solo in caso di migrazione delle macchine virtuali in Azure.
>
>

1. Eseguire il failover pianificato come indicato [qui](site-recovery-failover.md)
2. In **Impostazioni > Elementi replicati**, fare clic con il pulsante destro sulla macchina virtuale e selezionare **Completa migrazione**

    ![Completare la migrazione](./media/site-recovery-hyper-v-site-to-azure/migrate.png)
3. Fare clic su **OK** per completare l'operazione. Per tenere traccia dello stato di avanzamento del processo, fare clic sulla macchina virtuale per visualizzarne le proprietà oppure usare il processo Completa migrazione in **Impostazioni > Processi di Site Recovery**.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione
Per monitorare le impostazioni di configurazione, lo stato e l'integrità della distribuzione di Site Recovery, seguire questa procedura:

1. Fare clic sul nome dell'insieme di credenziali per accedere al dashboard **Informazioni di base** . In questo dashboard è possibile visualizzare i processi di Site Recovery, lo stato della replica, i piani di ripristino, l'integrità del server e gli eventi.  Il dashboard Informazioni di base può essere personalizzato con i riquadri e i layout più utili all'utente, incluso lo stato degli insiemi di credenziali di Backup e di Site Recovery.

    ![Informazioni di base](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. Nel riquadro **Integrità** è possibile monitorare i server del sito in cui si verifica il problema e gli eventi generati da Site Recovery nelle ultime 24 ore.
3. È possibile gestire e monitorare la replica nei riquadri **Elementi replicati**, **Piani di ripristino** e **Processi di Site Recovery**. Per eseguire il drill-down dei processi, accedere a **Impostazioni** -> **Processi** -> **Processi di Site Recovery**.

