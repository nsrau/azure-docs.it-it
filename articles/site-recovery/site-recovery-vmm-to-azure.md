---
title: Replicare le VM Hyper-V nei cloud VMM in Azure | Microsoft Docs
description: Orchestrare la replica, il failover e il ripristino di macchine virtuali Hyper-V gestite nei cloud di System Center VMM in Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-=article
ms.date: 03/20/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 95f4cb194d35d2781edef3b5a36e39724ea4850c
ms.lasthandoff: 03/22/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Eseguire la replica di macchine virtuali Hyper-V nei cloud VMM in Azure tramite Site Recovery nel Portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmm-to-azure.md)
> * [Azure classico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell - Classica](site-recovery-deploy-with-powershell.md)


Questo articolo illustra come eseguire la replica di macchine virtuali Hyper-V locali gestite nei cloud di System Center VMM in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel Portale di Azure.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="deployment-steps"></a>Passaggi di distribuzione


1. Ottenere [altre informazioni ](site-recovery-components.md#hyper-v-to-azure) sull'architettura di questa distribuzione. Ottenere inoltre [altre informazioni](site-recovery-hyper-v-azure-architecture.md) sul funzionamento della replica Hyper-V in Site Recovery.
2. Verificare i prerequisiti e le limitazioni.
3. Configurare la rete e gli account di archiviazione di Azure.
4. Preparare il server VMM e gli host Hyper-V locali.
5. Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.
6. Specificare le impostazioni di origine. Registrare il server VMM nell'insieme di credenziali. Installare il provider di Azure Site Recovery nel server VMM. Installare l'agente di Servizi di ripristino di Microsoft negli host Hyper-V.
7. Specificare le impostazioni di replica e di destinazione.
8. Abilitare la replica per le VM.
9. Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.



## <a name="prerequisites"></a>Prerequisiti


**Requisiti di supporto** | **Dettagli**
--- | ---
**Azure** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server locali** | [Altre informazioni](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure) sui requisiti per il server VMM e gli host Hyper-V locali.
**VM Hyper-V locali** | Le VM da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL di Azure** | Il server VMM deve poter accedere agli URL seguenti:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).


## <a name="prepare-for-deployment"></a>Preparare la distribuzione
Per preparare la distribuzione è necessario:

1. [Configurare una rete di Azure](#set-up-an-azure-network) in cui verranno collocate le VM di Azure dopo il failover.
2. [Configurare un account di archiviazione di Azure](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare il server VMM](#prepare-the-vmm-server) per la distribuzione di Site Recovery.
4. Preparare il mapping di rete. Configurare le reti in modo che sia possibile configurare il mapping di rete durante la distribuzione di Site Recovery.

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure
È necessario configurare una rete di Azure a cui connettere le VM di Azure create dopo il failover.

* La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
* A seconda del modello di risorsa da usare per le VM di Azure di cui si esegue il failover, la rete di Azure viene configurata in [modalità Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [modalità classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* È consigliabile configurare una rete prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery.
Le reti di Azure usate da Site Recovery non possono essere [spostate](../azure-resource-manager/resource-group-move-resources.md) all'interno della stessa sottoscrizione o tra sottoscrizioni diverse.

### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure
* Per contenere i dati replicati in Azure è necessario un account di archiviazione di Azure Standard. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
* A seconda del modello di risorsa da usare per le macchine virtuali di Azure di cui si esegue il failover, l'account deve essere configurato in [modalità Resource Manager](../storage/storage-create-storage-account.md) o in [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
* È consigliabile configurare un account prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery.
- Si noti che gli account di archiviazione usati per Site Recovery non possono essere [spostati](../azure-resource-manager/resource-group-move-resources.md) all'interno della stessa sottoscrizione o tra sottoscrizioni diverse.

### <a name="prepare-the-vmm-server"></a>Preparare il server VMM
* Assicurarsi che il server VMM sia conforme ai [prerequisiti](#on-premises-prerequisites).
* Durante la distribuzione di Site Recovery è possibile specificare la disponibilità di tutti i cloud in un server VMM nel portale di Azure. Per fare in modo che nel portale siano visibili soltanto cloud specifici è possibile abilitare tale impostazione nel cloud dalla console di amministrazione VMM.

### <a name="prepare-for-network-mapping"></a>Preparare il mapping di rete
Durante la distribuzione di Site Recovery è necessario configurare il mapping di rete. Questa operazione esegue il mapping tra le reti VM di VMM di origine e le reti di Azure di destinazione per consentire quanto segue:

* I computer di cui viene eseguito il failover nella stessa rete possono connettersi tra loro, anche se il failover non avviene nello stesso modo o nello stesso piano di ripristino.
* Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali di Azure possono connettersi alle macchine virtuali locali.
* Per configurare un mapping di rete è necessario quanto segue:

  * Accertarsi che le macchine virtuali nel server host Hyper-V di origine siano connesse a una rete VM di VMM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
  * Una rete di Azure come descritto [in precedenza](#set-up-an-azure-network)

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Gestione** > **Servizi di ripristino**. In alternativa, è possibile fare clic su **Esplora** > **Servizi di ripristino** insieme di credenziali > **Aggiungi**.

    ![Nuovo insieme di credenziali](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. In **Nome**specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionarne una.
4. [Creare un gruppo di risorse](../azure-resource-manager/resource-group-template-deploy-portal.md)o selezionarne uno esistente. Specificare un'area di Azure. I computer verranno replicati in quest'area. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** > **Crea insieme di credenziali**.

    ![Nuovo insieme di credenziali](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Il nuovo insieme di credenziali verrà visualizzato in **Dashboard** > **Tutte le risorse** e nel pannello **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="get-started"></a>Introduzione

Le attività iniziali di Site Recovery consentono di eseguire la distribuzione molto rapidamente. Permettono di verificare i prerequisiti e illustrano i passaggi della distribuzione di Site Recovery nell'ordine corretto.

Viene selezionato il tipo di computer da replicare e il percorso in cui eseguire la replica. Vengono configurati i server locali, gli account di archiviazione di Azure e le reti. Vengono creati i criteri di replica e si esegue la pianificazione della capacità. Una volta completata l'infrastruttura, abilitare la replica per le VM. È possibile eseguire failover per computer specifici o creare piani di ripristino per eseguire il failover di più macchine.

Per avviare le attività iniziali, scegliere la modalità di distribuzione di Site Recovery. Il flusso delle attività iniziali è leggermente diverso a seconda dei requisiti di replica.

## <a name="step-1-choose-your-protection-goals"></a>Passaggio 1: Scegliere gli obiettivi della protezione
Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Nel pannello **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali.
2. In **Introduzione** fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con Hyper-V**. Selezionare **Sì** per confermare l'uso di VMM per la gestione degli host Hyper-V e del sito di ripristino. Fare quindi clic su **OK**.

## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine
Installare il provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. Installare l'agente di Servizi di ripristino di Azure negli host Hyper-V.

1. Fare clic su **Passaggio 2: Preparare l'infrastruttura** > **Origine**.

    ![Impostare l'origine](./media/site-recovery-vmm-to-azure/set-source1.png)

2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM.

    ![Impostare l'origine](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Nel pannello **Aggiungi server** verificare che in **Tipo di server** sia visualizzato **System Center VMM server** (Server System Center VMM) e che il server VMM sia conforme a [prerequisiti e requisiti URL](#on-premises-prerequisites).
4. Scaricare il file di installazione del provider di Azure Site Recovery.
5. Scaricare la chiave di registrazione, che sarà necessaria durante l'installazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Installare il provider di Azure Site Recovery nel server VMM.

### <a name="set-up-the-azure-site-recovery-provider"></a>Configurare il provider di Azure Site Recovery
1. Eseguire il file di installazione del provider.
2. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
3. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.

    ![Percorso di installazione](./media/site-recovery-vmm-to-azure/provider2.png)
4. Al termine dell'installazione fare clic su **Registra** per registrare il server VMM nell'insieme di credenziali.
5. Nella pagina **Impostazioni dell'insieme di credenziali** fare clic su **Sfoglia** per selezionare il file di chiave dell'insieme di credenziali. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali.

    ![Server registration](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. In **Connessione Internet**specificare la modalità di connessione a Site Recovery tramite Internet del provider in esecuzione nel server VMM.

   * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
   * Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
   * Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
   * Se si usa un proxy, è necessario che gli URL descritti nei [prerequisiti](#on-premises-prerequisites) siano già consentiti.
   * Se si usa un proxy personalizzato, un account RunAs di VMM (DRAProxyAccount) verrà creato automaticamente con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. Le impostazioni dell'account RunAs di VMM possono essere modificate nella console VMM. In **Impostazioni** espandere **Sicurezza** > **Account RunAs** e quindi modificare la password di DRAProxyAccount. È necessario riavviare il servizio VMM per rendere effettiva l'impostazione.

     ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Accettare o modificare il percorso del certificato SSL generato automaticamente per la crittografia dei dati. Questo certificato viene usato se si abilita la crittografia dei dati per un cloud protetto da Azure nel portale di Azure Site Recovery. Conservare il certificato in una posizione sicura, Se la crittografia dei dati è abilitata, quando si esegue un failover in Azure è necessario eseguire la decrittografia.
8. In **Nome server**specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM.
9. Abilitare **Sincronizza i metadati cloud** per sincronizzare i metadati relativi a tutti i cloud presenti nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM. Fare clic su **Register** per completare il processo.

    ![Server registration](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Verrà avviata la registrazione. Al termine della registrazione, il server verrà visualizzato in **Infrastruttura di Site Recovery** > **Server VMM**.

#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installazione dalla riga di comando per il provider di Azure Site Recovery
Il provider di Azure Site Recovery può essere installato dalla riga di comando. Questo metodo può essere usato per installare il provider in un Server Core per Windows Server 2012 R2.

1. Scaricare il file di installazione del provider e il codice di registrazione in una cartella, ad esempio C:\ASR.
2. Da un prompt dei comandi con privilegi elevati eseguire questi comandi per estrarre il programma di installazione del provider:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Eseguire questo comando per installare i componenti:

            C:\ASR> setupdr.exe /i
4. Quindi eseguire questi comandi per registrare il server nell'insieme di credenziali:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Dove:

* **/Credentials**: parametro obbligatorio che specifica la posizione in cui si trova il file della chiave di registrazione.  
* **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V che viene visualizzato nel portale di Azure Site Recovery.
* * **/EncryptionEnabled**: parametro facoltativo usato quando si esegue la replica di macchine virtuali Hyper-V nei cloud VMM in Azure. Specificare se le macchine virtuali devono essere crittografate in Azure (crittografia dei dati inattivi). Assicurarsi che il nome del file abbia l'estensione **pfx** . La crittografia è disabilitata per impostazione predefinita.
* **/proxyAddress**: parametro facoltativo che specifica l'indirizzo del server proxy.
* **/proxyport**: parametro facoltativo che specifica la porta del server proxy.
* **/proxyUsername**: parametro facoltativo che specifica il nome utente proxy, se il proxy richiede l'autenticazione.
* **/proxyPassword**: parametro facoltativo che specifica la password per l'autenticazione con il server proxy, se il proxy richiede l'autenticazione.

### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di Servizi di ripristino di Azure negli host Hyper-V
1. Dopo aver configurato il provider è necessario scaricare il file di installazione per l'agente di Servizi di ripristino di Azure. Eseguire l'installazione in ogni server Hyper-V nel cloud VMM.

    ![Siti Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. In **Controllo dei prerequisiti** fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Prerequisiti per l'agente di Servizi di ripristino di Azure](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. In **Impostazioni di installazione** accettare o modificare il percorso di installazione e il percorso della cache. È possibile configurare la cache in un'unità con almeno 5 GB di spazio di archiviazione disponibile ma è consigliabile usare un disco della cache con almeno 600 GB di spazio disponibile. Fare clic su **Installa**.
4. Al termine dell'installazione fare clic su **Chiudi** per completare l'operazione.

    ![Registrare l'Agente di Servizi di ripristino di Microsoft Azure](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Installazione dalla riga di comando per l'agente di Servizi di ripristino di Microsoft Azure
È possibile installare l'agente di Servizi di ripristino di Microsoft Azure dalla riga di comando con questo comando:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Impostare l'accesso tramite proxy Internet a Site Recovery da host Hyper-V
L'agente di servizi di ripristino in esecuzione negli host Hyper-V richiede l'accesso ad Azure tramite Internet per la replica delle macchine virtuali. Se si accede a Internet tramite un proxy, configurarlo come indicato di seguito:

1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nell'host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Configurazione proxy** specificare le informazioni del server proxy.

    ![Registrare l'Agente di Servizi di ripristino di Microsoft Azure](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Assicurarsi che l'agente possa raggiungere gli URL indicati nei [prerequisiti](#on-premises-prerequisites).

## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione
Specificare l'account di archiviazione di Azure da usare per la replica e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**, selezionare la sottoscrizione e il gruppo di risorse in cui si vuole creare il failover delle macchine virtuali. Scegliere il modello di distribuzione che si vuole usare in Azure (classica o con Resource Manager) per il failover delle macchine virtuali.

    ![Archiviazione](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.
      ![Archiviazione](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4. Per creare un account di archiviazione con Resource Manager, se non è già stato fatto, fare clic su **+ Account di archiviazione** per eseguire l'operazione inline.  Nel pannello **Crea account di archiviazione** specificare il nome, il tipo, la sottoscrizione e la località dell'account. L'account deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

   ![Archiviazione](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

   Si noti che:

   * Per creare un account di archiviazione con il modello di distribuzione classica, usare il portale di Azure. [Altre informazioni](../storage/storage-create-storage-account-classic-portal.md)
   * Se si usa un account di archiviazione Premium per i dati replicati, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
5. Per creare una rete di Azure con Resource Manager, se non è già stato fatto, fare clic su **+ Rete** per eseguire l'operazione inline. Nel pannello **Crea rete virtuale** specificare il nome, l'intervallo di indirizzi, i dettagli della subnet, la sottoscrizione e la località della rete. La rete deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

   ![Rete](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Per creare una rete con il modello di distribuzione classica, usare il portale di Azure. [Altre informazioni](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurare il mapping di rete

* [Fare clic qui](#prepare-for-network-mapping) per una panoramica rapida del funzionamento del mapping di rete.
* Verificare che le macchine virtuali nel server VMM siano connesse a una rete VM e di aver creato almeno una rete virtuale di Azure. È possibile mappare più reti VM a una singola rete di Azure.

Per configurare il mapping, procedere come segue:

1. In **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete** fare clic sull'icona **+Mapping di rete**.

    ![Mapping di rete](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. In **Aggiungi mapping di rete** selezionare il server VMM di origine e **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**selezionare la rete VM locale di origine di cui si vuole eseguire il mapping dall'elenco associato al server VMM.
5. In **Rete di destinazione**selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica dopo la creazione. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Quando ha inizio il mapping di rete vengono eseguite le operazioni seguenti:

* Le macchine virtuali esistenti nella rete VM di origine vengono connesse alla rete di destinazione quando ha inizio il mapping. Le nuove macchine virtuali connesse alla rete VM di origine vengono connesse alla rete di Azure di cui è stato eseguito il mapping quando viene eseguita la replica.
* Se si modifica un mapping di rete esistente, le macchine virtuali di replica si connettono usando le nuove impostazioni.
* Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica si connette a questa subnet di destinazione dopo il failover.
* Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale si connette alla prima subnet della rete.

## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica
1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. In **Criteri di creazione e associazione**specificare il nome dei criteri.
3. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.
4. In **Conservazione del punto di ripristino**specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. Si noti che un'eventuale abilitazione di snapshot coerenti dell'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.
6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori dell'orario di lavoro.
7. In **Crittografare i dati archiviati in Azure**specificare se crittografare i dati inattivi in Archiviazione di Azure. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM. Fare clic su **OK**. È possibile associare altri cloud VMM (e le VM in essi contenute) a questi criteri di replica in **Replica** > nome del criterio > **Associate VMM Cloud (Associa cloud VMM)**.

    ![Criteri di replica](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione della capacità
Dopo avere configurato l'infrastruttura di base, passare alla pianificazione della capacità e valutare se sono necessarie altre risorse.

In Site Recovery è disponibile lo strumento Capacity Planner, che permettere di allocare le risorse appropriate all'ambiente di origine, i componenti di Site Recovery, la rete e l'archiviazione. È possibile eseguire lo strumento di pianificazione in modalità rapida, per ottenere stime basate su un numero medio di macchine virtuali, dischi e risorse di archiviazione, oppure in modalità dettagliata, dove si inseriscono le cifre a livello di carico di lavoro. Prima di iniziare:

* Raccogliere informazioni sull'ambiente di replica, incluse le macchine virtuali, i dischi per ogni macchina virtuale e le risorse di archiviazione per ogni disco.
* Stimare la frequenza di modifica giornaliera (varianza) prevista per i dati replicati. A questo scopo è possibile usare lo strumento [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Vedere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2. Al termine scegliere **Sì** in **Have you run the Capacity Planner**(È stato eseguito lo strumento Capacity Planner?).

   ![Pianificazione della capacità](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete
Lo strumento Capacity Planner può essere usato per calcolare la larghezza di banda necessaria per la replica iniziale e poi quella differenziale. Per controllare la quantità di larghezza di banda usata per la replica è possibile scegliere una delle opzioni seguenti:

* **Limitare la larghezza di banda**: il traffico Hyper-V che viene replicato in un sito secondario passa attraverso un host Hyper-V specifico. È possibile limitare la larghezza di banda nel server host.
* **Perfezionare la larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite una coppia di chiavi del Registro di sistema.

#### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda
1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nel server host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'uso della larghezza di banda Internet per le operazioni di backup** e impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Limitare la larghezza di banda](./media/site-recovery-vmm-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

#### <a name="influence-network-bandwidth"></a>Influire sulla larghezza di banda di rete
Il valore **UploadThreadsPerVM** del Registro di sistema controlla il numero di thread usati per il trasferimento dei dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica. Il valore **DownloadThreadsPerVM** del Registro di sistema specifica il numero di thread usati per il trasferimento dei dati durante il failback.

1. Nel Registro di sistema passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

   * Per controllare i thread usati per la replica dei dischi, modificare il valore **UploadThreadsPerVM** o creare la chiave, se non esiste.
   * Per controllare i thread usati per il traffico di failback da Azure, modificare il valore **DownloadThreadsPerVM** o creare la chiave, se non esiste.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="step-6-enable-replication"></a>Passaggio 6: Abilitare la replica

Per abilitare la replica, procedere come descritto di seguito.

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altre macchine.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. Nel pannello **Origine** selezionare il cloud e il server VMM in cui si trovano gli host Hyper-V. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. In **Destinazione** selezionare la sottoscrizione, il modello di distribuzione da usare dopo il failover e l'account di archiviazione usato per i dati replicati.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Selezionare l'account di archiviazione da usare. Per usare un account di archiviazione diverso da quelli disponibili, è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un account di archiviazione con il modello di distribuzione di Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/storage-create-storage-account-classic-portal.md). Fare quindi clic su **OK**.
5. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se si vuole usare una rete diversa da quelle disponibili, è possibile [crearne una](#set-up-an-azure-network). Per creare una rete con il modello di distribuzione di Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.
6. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. In **Proprietà** > **Configura proprietà**selezionare il sistema operativo per le VM selezionate e il disco del sistema operativo. Per impostazione predefinita, tutti i dischi della VM sono selezionati per la replica. Può essere necessario escludere dischi dalla replica per ridurre il consumo di larghezza di banda per la replica di dati non necessari in Azure. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ad ogni riavvio di un computer o un'applicazione come pagefile.sys o tempdb di Microsoft SQL Server. Per escludere un disco dalla replica, è sufficiente selezionarlo. Verificare che il nome della VM di Azure (nome di destinazione) sia conforme ai [requisiti per le macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) e, se necessario, modificarlo. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

    >[!NOTE]
    >
    > * Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere il disco del sistema operativo e non è consigliabile escludere i dischi dinamici. Site Recovery non può determinare se un disco rigido virtuale all'interno della macchina virtuale guest è di base o dinamico.  Se non sono esclusi tutti i volumi dinamici dipendenti, in caso di failover della VM il disco dinamico protetto risulterà guasto e non sarà possibile accedere ai dati presenti su tale disco.
    > * Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si vuole aggiungere o escludere un disco, è necessario disabilitare la protezione per la macchina virtuale e quindi riabilitarla.
    > * Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. In alternativa, è possibile integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
    > * Per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback da Azure a Hyper-V soltanto dei tre dischi. Non è possibile includere nel failback o nella replica inversa da Hyper-V ad Azure i dischi creati manualmente.
    >
    >


8. In **Impostazioni della replica** > **Configurare le impostazioni di replica** selezionare i criteri di replica da applicare per le VM protette. Fare quindi clic su **OK**. È possibile modificare i criteri di replica in **Criteri di replica** > nome del criterio > **Modifica impostazioni**. Le modifiche applicate vengono usate per i computer di cui è già in corso la replica e per i nuovi computer.

   ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication7.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale
È consigliabile verificare le proprietà del computer di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Elementi protetti** fare clic su **Elementi replicati** e selezionare il computer per visualizzarne i dettagli.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) . È anche possibile visualizzare e modificare le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP assegnati alla VM di Azure.
Si noti che:

   * È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover ha esito negativo. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
   * Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:

     * Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
     * Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà usata la dimensione di destinazione massima.
     * Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
     * Se la VM ha più schede di rete, si connetteranno tutte alla stessa rete.

     ![Abilitare la replica](./media/site-recovery-vmm-to-azure/test-failover4.png)
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

* Aggiungere un endpoint pubblico per il protocollo RDP (porta 3389) e specificare le credenziali per l'account di accesso.
* Assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale tramite un indirizzo pubblico.
* Provare a connettersi. Se non è possibile connettersi verificare che la macchina virtuale sia in esecuzione. Per altri suggerimenti sulla risoluzione dei problemi, vedere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Per accedere a una macchina virtuale di Azure che esegue Linux dopo il failover con un client Secure Shell (ssh), seguire questa procedura:

**Nel computer locale prima del failover**:

* Assicurarsi che il servizio Secure Shell nella macchina virtuale di Azure sia impostato per l'avvio automatico all'avvio del sistema.
* Verificare che le regole firewall accettino la connessione SSH.

**Nella VM di Azure dopo il failover**:

* Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.
* È necessario creare un endpoint pubblico per consentire le connessioni in ingresso sulla porta SSH, che per impostazione predefinita è la porta TCP 22.
* Se la macchina virtuale è accessibile tramite una connessione VPN, Express Route o VPN da sito a sito, il client può essere usato per connettersi direttamente alla macchina virtuale tramite SSH.


## <a name="step-7-test-your-deployment"></a>Passaggio 7: Testare la distribuzione
Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale o un piano di ripristino che contenga una o più macchine virtuali.

1. Per eseguire il failover di una VM, in **Elementi replicati** fare clic sulla VM e quindi su **+Failover di test**.
1. Per eseguire il failover di un piano di ripristino, in **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi su **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).
1. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.
1. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure sul processo **Failover di test** in **Processi di Site Recovery**.
1. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la VM sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
1. Se sono state [preparate le connessioni dopo il failover](#prepare-to-connect-to-Azure-VMs-after-failover), sarà possibile connettersi alla VM di Azure.
1. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test.

Per altri dettagli, vedere il documento [Failover di test in Azure](site-recovery-test-failover-to-azure.md).

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione
Per monitorare le impostazioni di configurazione, lo stato e l'integrità della distribuzione di Site Recovery, seguire questa procedura:

1. Fare clic sul nome dell'insieme di credenziali per accedere al dashboard **Informazioni di base** . In questo dashboard è possibile visualizzare i processi di Site Recovery, lo stato della replica, i piani di ripristino, l'integrità del server e gli eventi.  Il dashboard **Informazioni di base** può essere personalizzato con i riquadri e i layout più utili all'utente, incluso lo stato degli insiemi di credenziali di Backup e di Site Recovery.

    ![Informazioni di base](./media/site-recovery-vmm-to-azure/essentials.png)
2. In **Integrità** è possibile monitorare i problemi sui server locali (VMM o server di configurazione) e gli eventi generati da Site Recovery nelle ultime 24 ore.
3. È possibile gestire e monitorare la replica nei riquadri **Elementi replicati**, **Piani di ripristino** e **Processi di Site Recovery**. Per eseguire il drill-down dei processi, accedere a **Processi** > **Processi di Site Recovery**.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, leggere [altre informazioni](site-recovery-failover.md) sul failover.

