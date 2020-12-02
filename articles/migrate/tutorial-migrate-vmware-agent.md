---
title: Eseguire la migrazione di macchine virtuali VMware con la soluzione Migrazione server di Azure Migrate basata su agente
description: Informazioni su come eseguire una migrazione di macchine virtuali VMware basata su agente con Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 46f23953e6572b752f8773bc9db86be946ccf212
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492998"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Eseguire la migrazione di macchine virtuali VMware in Azure (basata su agente)

Questo articolo illustra come eseguire la migrazione di macchine virtuali VMware locali in Azure usando lo strumento [Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) con il metodo di migrazione basata su agente.  Per eseguire la migrazione di macchine virtuali VMware è anche possibile usare il metodo di migrazione senza agente. [Confrontare](server-migrate-overview.md#compare-migration-methods) i metodi.


 In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Preparare Azure per l'uso con Azure Migrate.
> * Preparare Azure per la migrazione basata su agente. Configurare un account VMware per consentire ad Azure Migrate di individuare le macchine virtuali per la migrazione. Configurare un account in modo da installare l'agente del servizio di mobilità nelle macchine virtuali di cui si vuole eseguire la migrazione e preparare una macchina virtuale che funga da appliance di replica.
> * Aggiungere lo strumento Migrazione server di Azure Migrate
> * Configurare l'appliance di replica.
> * Replicare le macchine virtuali.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa ad Azure.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, [rivedere](./agent-based-migration-architecture.md) l'architettura della migrazione basata su agente di VMware.

## <a name="prepare-azure"></a>Preparare Azure

Completare le attività descritte nella tabella per preparare Azure per la migrazione basata su agente.

**Attività** | **Dettagli**
--- | ---
**Creare un progetto di Azure Migrate** | Per creare un progetto, è necessario che l'account Azure abbia autorizzazioni di Collaboratore o Proprietario.
**Verificare le autorizzazioni dell'account di Azure** | L'account Azure necessita delle autorizzazioni per creare una macchina virtuale ed eseguire la scrittura su un disco gestito di Azure.
**Configurare una rete di Azure** | Configurare una rete a cui verranno aggiunte le VM di Azure dopo la migrazione.

### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto
Se non si ha un progetto Azure Migrate, verificare di disporre delle autorizzazioni per crearne uno.


1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. Verificare di avere le autorizzazioni di **Collaboratore** o **Proprietario**.

    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.
    
### <a name="assign-azure-account-permissions"></a>Assegnare le autorizzazioni all'account Azure

Assegnare all'account il ruolo Collaboratore Macchina virtuale, in modo da avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere in un disco gestito di Azure. 


### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

[Configurare una rete di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). I computer locali vengono replicati in dischi gestiti di Azure. Quando si effettua il failover in Azure per la migrazione, le macchine virtuali di Azure vengono create da questi dischi gestiti e aggiunte alla rete di Azure configurata.

## <a name="prepare-for-migration"></a>Preparare la migrazione

Verificare i requisiti di supporto e le autorizzazioni e preparare la distribuzione di un'appliance di replica. 

### <a name="prepare-an-account-to-discover-vms"></a>Preparare un account per l'individuazione delle VM

Lo strumento Azure Migrate: Migrazione del server deve avere accesso ai server VMware per individuare le VM di cui eseguire la migrazione. Creare l'account come illustrato di seguito:

1. Per usare un account dedicato, creare un ruolo a livello vCenter. Assegnare un nome al ruolo, ad esempio **Azure_Migrate**.
2. Assegnare al ruolo le autorizzazioni elencate nella tabella seguente.
3. Creare un utente nel server vCenter o nell'host vSphere. Assegnare il ruolo all'utente.

#### <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

**Attività** | **Ruolo/Autorizzazioni** | **Dettagli**
--- | --- | ---
**Individuazione di macchine virtuali** | Almeno un utente in sola lettura<br/><br/> Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **Nessun accesso** con **Propagate to Child Object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, macchine virtuali e reti).
**Replica** |  Creare un ruolo (Azure_Site_Recovery) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o gruppo VMware<br/><br/> Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **Nessun accesso** con **Propagate to Child Object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, macchine virtuali e reti).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in tutte le macchine virtuali da replicare.

- L'appliance di replica di Azure Migrate può eseguire un'installazione push del servizio quando si abilita la replica per una macchina virtuale oppure è possibile installarlo manualmente o tramite strumenti di installazione.
- In questa esercitazione, il servizio Mobility verrà installato con l'installazione push.
- Per l'installazione push è necessario preparare un account che possa essere usato da Migrazione server di Azure Migrate per accedere alla macchina virtuale. Questo account viene usato solo per l'installazione push, se non si installa manualmente il servizio di mobilità.

Preparare l'account come illustrato di seguito:

1. Preparare un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.
2. Per le macchine virtuali Windows, se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale aggiungendo la voce DWORD **LocalAccountTokenFilterPolicy** con il valore 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
3. Per le macchine virtuali Linux, preparare un account radice nel server Linux di origine.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparare un computer per l'appliance di replica

L'appliance viene usata per la replica delle VM in Azure. L'appliance è una singola macchina virtuale VMware locale a disponibilità elevata che ospita questi componenti:

- **Server di configurazione**: Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- **Server di elaborazione** Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione cache in Azure. Il server di elaborazione installa anche l'agente del servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.

Preparare l'appliance nel modo seguente:

- [Verificare i requisiti dell'appliance](migrate-replication-appliance.md#appliance-requirements). In genere si configura l'appliance di replica come macchina virtuale VMware usando un file OVA scaricato. Il modello crea un'appliance conforme a tutti i requisiti.
- MySQL deve essere installato nell'appliance. [Esaminare](migrate-replication-appliance.md#mysql-installation) i metodi di installazione.
- Esaminare gli [URL del cloud pubblico](migrate-replication-appliance.md#url-access) e gli [URL di Azure per enti pubblici](migrate-replication-appliance.md#azure-government-url-access) a cui l'appliance deve accedere.
- [Esaminare le porte](migrate-replication-appliance.md#port-access) a cui l'appliance di replica deve accedere.



### <a name="check-vmware-requirements"></a>Verificare i requisiti di VMware

Assicurarsi che i server e le macchine virtuali VMware siano conformi ai requisiti per la migrazione ad Azure. 

1. [Verificare](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) i requisiti dei server VMware.
2. [Verificare](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) i requisiti delle macchine virtuali per la migrazione.
3. Verificare le impostazioni di Azure. Le macchine virtuali locali replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Prima di eseguire la migrazione delle macchine virtuali ad Azure, è necessario apportarvi alcune modifiche.
    - È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
    - Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).

> [!NOTE]
> La migrazione basata su agente con lo strumento Migrazione server di Azure Migrate si basa sulle funzionalità del servizio Azure Site Recovery. Alcuni requisiti potrebbero includere collegamenti alla documentazione di Site Recovery.

## <a name="set-up-the-replication-appliance"></a>Configurare l'appliance di replica

Questa procedura descrive come configurare l'appliance con un modello OVA (Open Virtualization Application) scaricato. Se non è possibile usare questo metodo, si può configurare l'appliance [usando uno script](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Scaricare il modello di appliance di replica

Per scaricare il modello, seguire questa procedura:

1. Nel progetto di Azure Migrate fare clic su **Server** in **Obiettivi della migrazione**.
2. In **Azure Migrate - Server** > **Azure Migrate: Migrazione server** fare clic su **Individua**.

    ![Individuare le VM](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Individua macchine virtuali** > **I computer sono virtualizzati?** fare clic su **Sì, con VMware vSphere Hypervisor**.
4. In **Scegliere la modalità di migrazione** selezionare **Con replica basata su agente**.
5. In **Area di destinazione** selezionare l'area di Azure in cui eseguire la migrazione delle macchine virtuali.
6. Selezionare **Confermare che l'area di destinazione della migrazione è <nome area>** .
7. Fare clic su **Crea risorse**. Verrà creato un insieme di credenziali di Azure Site Recovery in background. Dopo aver fatto clic su questo pulsante non è più possibile cambiare l'area di destinazione di questo progetto e tutte le migrazioni successive vengono eseguite in questa area.

    ![Creare un insieme di credenziali di Servizi di ripristino](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. In **Installare una nuova appliance di replica o aumentare le prestazioni della configurazione esistente?** selezionare **Installare un'appliance di replica**.
9. Fare clic su **Download**. Viene scaricato un modello OVF.
    ![Download del modello OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Prendere nota del nome del gruppo di risorse e dell'insieme di credenziali di Servizi di ripristino. Serviranno durante la distribuzione dell'appliance.


### <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

Dopo aver scaricato il modello OVF, occorre importarlo in VMware per creare l'applicazione di replica in una macchina virtuale VMware che esegue Windows Server 2016.

1. Accedere al server VMware vCenter o all'host vSphere ESXi con il client VMware vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) **per avviare la relativa procedura** guidata. 
3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) > **Select virtual disk format** (Seleziona formato disco virtuale) selezionare **Thick Provision Eager Zeroed**.
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Pronto per completare**, per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Accendi al termine della distribuzione** > **Fine**.

   > [!TIP]
   > Se si vuole aggiungere un'altra scheda di interfaccia di rete, deselezionare **Power on after deployment** > **Finish** (Accendi al termine della distribuzione, Fine). Per impostazione predefinita, il modello contiene una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.

### <a name="start-appliance-setup"></a>Avviare la configurazione dell'appliance

1. Accendere la macchina virtuale nella console del client VMware vSphere. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016.
2. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore usando la password amministratore. Al primo accesso verrà avviato lo strumento di configurazione dell'appliance di replica (strumento di configurazione di Azure Site Recovery) entro pochi secondi.
5. Immettere un nome da usare per registrare l'appliance con lo strumento Migrazione del server. Quindi fare clic su **Next**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure.
7. Attendere che lo strumento termini la registrazione di un'app Azure AD per identificare l'appliance. L'appliance viene riavviata.
1. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente entro pochi secondi.

### <a name="register-the-replication-appliance"></a>Registrare l'appliance di replica

Completare la configurazione e la registrazione dell'appliance di replica.

1. Nella finestra di configurazione dell'appliance selezionare **Configura la connettività**.
2. Selezionare la scheda di interfaccia di rete (per impostazione predefinita ne è presente solo una) usata dall'appliance di replica per l'individuazione delle macchine virtuali e per eseguire un'installazione push del servizio Mobility nelle macchine virtuali di origine.
3. Selezionare la scheda di interfaccia di rete usata dall'appliance di replica per la connettività con Azure. Selezionare quindi **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
4. Se l'appliance è protetta da un server proxy, è necessario specificare le impostazioni del proxy.
    - Specificare il nome del proxy nel formato **http://ip-address** o **http://FQDN** . I server proxy HTTPS non sono supportati.
5. Quando viene chiesto di immettere i dettagli relativi alla sottoscrizione, ai gruppi di risorse e all'insieme di credenziali, aggiungere le informazioni annotate quando si è scaricato il modello di appliance.
6. In **Installa software di terze parti** accettare il contratto di licenza. Selezionare **Scarica e installa** per installare Server MySQL.
7. Selezionare **Installa VMware PowerCLI**. Prima di eseguire questa operazione, assicurarsi che tutte le finestre del browser siano chiuse. Selezionare quindi **Continua**.
8. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
9. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
10. Immettere le credenziali dell'account [creato](#prepare-an-account-to-discover-vms) per l'individuazione VMware. Selezionare **Aggiungi** > **Continua**.
11. In **Configura le credenziali della macchina virtuale** immettere le credenziali [create](#prepare-an-account-for-mobility-service-installation) per l'installazione push del servizio Mobility quando è stata abilitata la replica per le macchine virtuali.  
    - Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare.
    - Per Linux, specificare i dettagli dell'account radice.
12. Selezionare **Finalizza configurazione** per completare la registrazione.


Dopo la registrazione dell'appliance di replica, lo strumento Valutazione server di Azure Migrate si connette ai server VMware usando le impostazioni specificate e individua le macchine virtuali. È possibile visualizzare le macchine virtuali individuate in **Gestisci** > **Elementi individuati** nella scheda **Altro**.



## <a name="replicate-vms"></a>Replicare le VM

Selezionare le VM di cui eseguire la migrazione.

> [!NOTE]
> Nel portale è possibile selezionare un massimo di 10 VM contemporaneamente per la replica. Se occorre replicarne di più, raggrupparle in batch di 10 VM.

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server**, quindi **Replica**.

    ![Screenshot della schermata Server in Azure Migrate. Il pulsante Replica è selezionato in Azure Migrate: Migrazione del server in Strumenti di migrazione.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. In **Replica** > **Impostazioni origine** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata.
4. In **Server vCenter** specificare il nome del server vCenter che gestisce le VM, ovvero il server vSphere che le ospita.
5. In **Server di elaborazione** selezionare il nome dell'appliance di replica.
6. In **Credenziali guest** specificare l'account amministratore di macchine virtuali che verrà usato per l'installazione push del servizio Mobility. Fare quindi clic su **Avanti: Macchine virtuali**.

    ![Screenshot della scheda Impostazioni origine nella schermata Replica. Il campo Credenziali guest è evidenziato e il valore è impostato su VM-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. In **Macchine virtuali** selezionare le macchine virtuali da replicare.

    - Se è stata eseguita una valutazione delle VM, è possibile applicare i consigli dei risultati della valutazione in merito al tipo di disco (Premium/Standard) e alle dimensioni delle VM. A questo scopo, in **Importare le impostazioni di migrazione da una valutazione di Azure Migrate?** selezionare l'opzione **Sì**.
    - Se non è stata eseguita una valutazione o non si vogliono usare le impostazioni della valutazione, selezionare l'opzione **No**.
    - Se si è scelto di usare la valutazione, selezionare il gruppo di VM e il nome della valutazione.
8. In **Opzioni di disponibilità** selezionare:
    -  Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Se si seleziona questa opzione, sarà necessario specificare la zona di disponibilità da usare per ogni macchina selezionata nella scheda Calcolo. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità
    -  Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità.
    - L'opzione La ridondanza dell'infrastruttura non è richiesta se non è necessaria una di queste configurazioni di disponibilità per le macchine migrate.
9. Selezionare ogni macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.
10. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione in cui eseguire la migrazione e quindi specificare il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione.
11. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
12. In **Opzioni di disponibilità** selezionare:
    -  Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Se si seleziona questa opzione, sarà necessario specificare la zona di disponibilità da usare per ogni macchina selezionata nella scheda Calcolo. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità
    -  Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità.
    - L'opzione La ridondanza dell'infrastruttura non è richiesta se non è necessaria una di queste configurazioni di disponibilità per le macchine migrate.
    
13. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

14. In **Calcolo** controllare il nome della macchina virtuale, le dimensioni, il tipo di disco del sistema operativo e la configurazione della disponibilità, se selezionata nel passaggio precedente. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale mostra le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Zona di disponibilità**: specificare la zona di disponibilità da usare.
    - **Set di disponibilità**: specificare il set di disponibilità da usare.

15. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

16. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Le impostazioni non possono essere modificate dopo l'avvio della replica.


## <a name="track-and-monitor"></a>Tenere traccia e monitorare

1. Tenere traccia dello stato del processo nelle notifiche del portale. 

    ![Traccia del processo](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Per monitorare lo stato della replica, fare clic su **Replica dei server** in **Azure Migrate: Migrazione server**.

    ![Monitorare la replica](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

La replica avviene nel modo indicato di seguito:
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.


## <a name="run-a-test-migration"></a>Eseguire una migrazione di test


All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sui computer locali, che rimangono operativi, e che la replica continui. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Per eseguire una migrazione di test, seguire questa procedura:


1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Migrazione server** fare clic su **Testare i server con migrazione completata**.

     ![Testare i server con migrazione completata](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Fare clic con il pulsante destro del mouse sulla VM da testare e scegliere **Migrazione di test**.

    ![Migrazione di test](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. In **Migrazione di test** selezionare la rete virtuale di Azure in cui verrà inserita la VM di Azure dopo la migrazione. È consigliabile usare una rete virtuale non di produzione.
4. Verrà avviato il processo **Migrazione di test**. Monitorare il processo nelle notifiche del portale.
5. Al termine della migrazione, visualizzare la VM di Azure di cui è stata eseguita la migrazione in **Macchine virtuali** nel portale di Azure. Il nome della macchina virtuale ha il suffisso **-Test**.
6. Al termine del test, fare clic con il pulsante destro del mouse sulla macchina virtuale di Azure in **Replica delle macchine virtuali** e scegliere **Pulisci migrazione di test**.

    ![Eseguire la pulizia della migrazione](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle macchine virtuali locali.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. In **Replica delle macchine virtuali** fare clic con il pulsante destro del mouse sulla VM e scegliere **Esegui la migrazione**.
3. In **Esegui la migrazione** > **Spegnere le macchine virtuali ed eseguire una migrazione pianificata senza perdita di dati** selezionare **Sì** > **OK**.
    - Per impostazione predefinita, Azure Migrate arresta la macchina virtuale locale per garantire una perdita di dati minima. 
    - Se non si vuole arrestare la VM, selezionare **No**
4. Verrà avviato un processo di migrazione per la VM. Tenere traccia del processo nelle notifiche di Azure.
5. Al termine del processo, è possibile visualizzare e gestire la VM dalla pagina **Macchine virtuali**.

## <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla VM e scegliere **Arresta migrazione**. Vengono eseguite le operazioni seguenti:
    - Arresta la replica per il computer locale.
    - Rimuove il computer dal numero di **Server in fase di replica** in Azure Migrate: Server Migration.
    - Esegue la pulizia delle informazioni sullo stato di replica per la macchina virtuale.
2. Installare l'agente [Windows](../virtual-machines/extensions/agent-windows.md) o [Linux](../virtual-machines/extensions/agent-linux.md) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
3. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
4. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
5. Trasferire il traffico all'istanza della VM di Azure di cui è stata eseguita la migrazione.
6. Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
7. Rimuovere le macchine virtuali locali dai processi di backup locali.
8. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure. 

## <a name="post-migration-best-practices"></a>Procedure consigliate dopo la migrazione

- Locale
    - Spostare il traffico dell'app sull'app in esecuzione nell'istanza della macchina virtuale di Azure migrata.
    - Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
    - Rimuovere le macchine virtuali locali dai processi di backup locali.
    - Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure.
- Modificare le impostazioni della macchina virtuale di Azure dopo la migrazione:
    - L'[agente di macchine virtuali di Azure](../virtual-machines/extensions/agent-windows.md) gestisce l'interazione tra le macchine virtuali e il controller di infrastruttura di Azure. È obbligatorio per alcuni servizi di Azure, tra cui Backup di Azure, Site Recovery e Sicurezza di Azure. Quando si esegue la migrazione di macchine virtuali VMare con la migrazione basata su agente, il programma di installazione del servizio di mobilità installa l'agente di macchine virtuali di Azure nelle macchine virtuali Windows. Nelle macchine virtuali Linux è consigliabile installare l'agente dopo la migrazione.
    - Disinstallare manualmente il servizio di mobilità dalla macchina virtuale di Azure dopo la migrazione.
    - Disinstallare manualmente gli strumenti VMware dopo la migrazione.
- In Azure:
    - Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
    - Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
- Continuità aziendale/Ripristino di emergenza
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).
    - Distribuire [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
    - È consigliabile distribuire [Gestione costi di Azure](../cost-management-billing/cloudyn/overview.md) per monitorare l'utilizzo delle risorse e le spese.




 ## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.