---
title: Eseguire la migrazione di macchine virtuali VMware locali in Azure con Migrazione server di Azure Migrate basata su agente | Microsoft Docs
description: Questo articolo illustra come eseguire una migrazione basata su agente di macchine virtuali locali in Azure con Migrazione server di Azure Migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261404"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Eseguire la migrazione di macchine virtuali VMware in Azure (basata su agente)

Questo articolo illustra come eseguire una migrazione basata su agente di macchine virtuali VMware locali in Azure con lo strumento Migrazione server di Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale da cui monitorare l'individuazione, la valutazione e la migrazione dei carichi di lavoro e delle app locali, oltre che delle istanze di macchine virtuali AWS/GCP, in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti.


In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare l'ambiente di origine e distribuire un'appliance di replica di Azure Migrate per la migrazione basata su agente.
> * Configurare l'ambiente di destinazione della migrazione.
> * Configurare criteri di replica.
> * Abilitare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa ad Azure.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione e alla migrazione di VMware.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di eseguire la migrazione delle macchine virtuali VMware in Azure, è consigliabile provarne la valutazione con lo strumento Valutazione server di Azure Migrate. Configurare una valutazione nel modo seguente:

1. Seguire l'esercitazione per [preparare Azure e VMware](tutorial-prepare-vmware.md) per la valutazione.
2. Quindi, seguire [questa esercitazione](tutorial-assess-vmware.md) per configurare un'appliance di Azure Migrate per la valutazione e individuare e valutare le macchine virtuali.


Benché sia consigliabile provare una valutazione, non è necessario farlo prima di eseguire la migrazione delle macchine virtuali.

## <a name="migration-methods"></a>Metodi di migrazione

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione basata su agente (o replica). Installa l'agente dei servizi Mobility sulla macchina virtuale per la replica.

Per decidere se usare la migrazione senza agente o basata su agente, vedere questi articoli:

- [Informazioni](server-migrate-overview.md) sulle opzioni di migrazione di VMware.
- [Limitazioni](server-migrate-overview.md#agentless-migration-limitations) per la migrazione senza agente.
- [Esercitazione](tutorial-migrate-vmware.md) sulla migrazione senza agente.



## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Rivedere](migrate-architecture.md) l'architettura di migrazione di VMware.
2. Assicurarsi che al proprio account Azure sia assegnato il ruolo Collaboratore Macchina virtuale, in modo da avere le autorizzazioni per:

    - Creare una macchina virtuale nel gruppo di risorse selezionato.
    - Creare una macchina virtuale nella rete virtuale selezionata.
    - Scrivere in un disco gestito di Azure. 

3. [Configurare una rete di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). I computer locali vengono replicati in dischi gestiti di Azure. Quando si effettua il failover in Azure per la migrazione, le macchine virtuali di Azure vengono create da questi dischi gestiti e aggiunti alla rete di Azure specificata al momento della configurazione della migrazione.


## <a name="prepare-azure"></a>Preparare Azure

Se è già stata eseguita una valutazione con lo strumento Valutazione server di Azure Migrate, è possibile ignorare le istruzioni di questa sezione, dato che i passaggi descritti sono già stati completati. 

Se non è stata eseguita una valutazione, è necessario configurare le autorizzazioni di Azure prima di eseguire la migrazione con Migrazione server di Azure Migrate.

- **Creare un progetto**: l'account Azure deve avere le autorizzazioni per creare un progetto di Azure Migrate. 
- **Registrare l'appliance di replica Azure Migrate**: l'appliance di replica crea e registra un'app Azure Active Directory nell'account Azure. È necessario delegare le autorizzazioni per queste operazioni.
- **Creare un insieme di credenziali delle chiavi**: per eseguire la migrazione delle macchine virtuali VMware con Migrazione server di Azure Migrate, Azure Migrate crea un insieme di credenziali delle chiavi nel gruppo di risorse per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. Per creare l'insieme di credenziali delle chiavi sono necessarie autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance di replica

Per la migrazione basata su agente, delegare allo strumento Migrazione server di Azure Migrate le autorizzazioni per creare e registrare un'app Azure AD nel proprio account. È possibile assegnare le autorizzazioni usando uno dei metodi seguenti:

- Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

Vale la pena notare che:

- Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
- Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance di replica. Dopo la configurazione dell'appliance di replica è possibile rimuovere le autorizzazioni. 


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni 

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="assign-permissions-to-create-key-vault"></a>Assegnare le autorizzazioni per creare l'insieme di credenziali delle chiavi

Per assegnare le autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate, eseguire questa procedura:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni. Sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).
3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse. 


## <a name="prepare-on-premises-vmware"></a>Preparare VMware locali

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Migrazione server di Azure Migrate deve avere accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'accensione delle macchine virtuali.

Creare l'account come illustrato di seguito:

1. Per usare un account dedicato, creare un ruolo a livello vCenter. Assegnare un nome al ruolo, ad esempio **Azure_Site_Recovery**.
2. Assegnare al ruolo le autorizzazioni elencate nella tabella seguente.
3. Creare un utente nel server vCenter o nell'host vSphere. Assegnare il ruolo all'utente.

#### <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

**Attività** | **Ruolo/Autorizzazioni** | **Dettagli**
--- | --- | ---
**Individuazione di macchine virtuali** | Almeno un utente in sola lettura<br/><br/> Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
**Replica completa, failover, failback** |  Creare un ruolo (Azure_Site_Recovery) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o gruppo VMware<br/><br/> Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in tutte le macchine virtuali da replicare.

- L'appliance di replica di Azure Migrate può eseguire un'installazione push del servizio quando si abilita la replica per una macchina virtuale oppure è possibile installarlo manualmente o tramite strumenti di installazione.
- In questa esercitazione, il servizio Mobility verrà installato con l'installazione push.
- Per l'installazione push è necessario preparare un account che possa essere usato da Migrazione server di Azure Migrate per accedere alla macchina virtuale.

Preparare l'account come illustrato di seguito:

1. Preparare un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.
2. Per le macchine virtuali Windows, se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale aggiungendo la voce DWORD **LocalAccountTokenFilterPolicy** con il valore 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
3. Per le macchine virtuali Linux, preparare un account radice nel server Linux di origine.


### <a name="check-vmware-requirements"></a>Verificare i requisiti di VMware

Assicurarsi che i server e le macchine virtuali VMware siano conformi ai requisiti per la migrazione ad Azure. 


> [!NOTE]
> La migrazione basata su agente con Migrazione server di Azure Migrate si basa sulle funzionalità del servizio Azure Site Recovery. Alcuni requisiti potrebbero includere collegamenti alla documentazione di Site Recovery.

1. [Verificare](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) i requisiti dei server VMware.
2. [Verificare](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) i requisiti di supporto delle macchine virtuali per la migrazione.
3. Verificare le impostazioni delle macchine virtuali. Le macchine virtuali locali replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Aggiungere lo strumento Migrazione server di Azure Migrate

Se non è stata seguita l'esercitazione per valutare le macchine virtuali VMware, configurare un progetto di Azure Migrate e quindi aggiungere lo strumento Migrazione server di Azure Migrate:

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.

    ![Configurare Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. In **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server** .
4. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server**.

    ![Individuare e valutare i server](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Aggiungi strumenti**.
2. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.
3. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare, quindi fare clic su **Avanti**.

    ![Creare un progetto di Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    È possibile creare un progetto di Azure Migrate in una delle aree geografiche seguenti.

    **Area geografica** | **Area**
    --- | ---
    Asia | Asia sud-orientale
    Europa | Europa settentrionale o Europa occidentale
    Stati Uniti | Stati Uniti orientali o Stati Uniti centro-occidentali

    L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. Per la migrazione effettiva è possibile selezionare qualsiasi area di destinazione.
4. In **Selezionare lo strumento di valutazione** selezionare **Ignora l'aggiunta di uno strumento di valutazione per adesso** > **Avanti**.
5. In **Selezionare lo strumento di migrazione** selezionare **Azure Migrate: Migrazione server** > **Avanti**.
6. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**
7. Dopo l'aggiunta, lo strumento viene visualizzato nel progetto di Azure Migrate > **Server** > **Strumenti di migrazione**.

## <a name="set-up-the-replication-appliance"></a>Configurare l'appliance di replica

La prima fase del processo di migrazione è la configurazione dell'appliance di replica. L'appliance di replica è una singola macchina virtuale VMware locale a disponibilità elevata che ospita questi componenti:

- **Server di configurazione**: Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- **Server di elaborazione** Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione cache in Azure. Il server di elaborazione installa anche l'agente del servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.


Per configurare l'appliance di replica occorre scaricare un modello OVA (Open Virtualization Application) già preparato, importarlo in VMware e creare la VM appliance di replica. 

### <a name="download-the-replication-appliance-template"></a>Scaricare il modello di appliance di replica

Per scaricare il modello, seguire questa procedura:

1. Nel progetto di Azure Migrate fare clic su **Server** in **Obiettivi della migrazione**.
2. In **Azure Migrate - Server** > **Azure Migrate: Migrazione server** fare clic su **Individua**.

    ![Individuare le VM](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Individua macchine virtuali** > **I computer sono virtualizzati?** fare clic su **Sì, con VMware vSphere Hypervisor**.
4. In **Scegliere la modalità di migrazione** selezionare **Con replica basata su agente**.
5. In **Area di destinazione** selezionare l'area di Azure in cui si vuole eseguire la migrazione delle macchine virtuali.
6. Selezionare **Confermare che l'area di destinazione della migrazione è <nome area>** .
7. Fare clic su **Crea risorse**. Verrà creato un insieme di credenziali di Azure Site Recovery in background.
    - Dopo aver fatto clic su questo pulsante non è più possibile cambiare l'area di destinazione di questo progetto.
    - Tutte le migrazioni successive avverranno in questa area.

    ![Creare un insieme di credenziali di Servizi di ripristino](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. In **Installare una nuova appliance di replica o aumentare le prestazioni della configurazione esistente?** selezionare **Installare un'appliance di replica**.
9. Fare clic su **Scarica** per scaricare l'appliance di replica. Viene scaricato un modello OVF da usare per creare una nuova macchina virtuale VMware che esegue l'appliance.
    ![Download del modello OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Prendere nota del nome del gruppo di risorse e dell'insieme di credenziali di Servizi di ripristino. Serviranno durante la distribuzione dell'appliance.


### <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

Dopo aver scaricato il modello OVF, occorre importarlo in VMware per creare l'applicazione di replica in una macchina virtuale VMware che esegue Windows Server 2016.

1. Accedere al server VMware vCenter o all'host vSphere ESXi con il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) **per avviare la relativa procedura**guidata. 
3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) > **Select virtual disk format** (Seleziona formato disco virtuale) selezionare **Thick Provision Eager Zeroed**.
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Pronto per completare**, per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Accendi al termine della distribuzione** > **Fine**.

   > [!TIP]
   > Se si vuole aggiungere un'altra scheda di interfaccia di rete, deselezionare **Power on after deployment** > **Finish** (Accendi al termine della distribuzione, Fine). Per impostazione predefinita, il modello contiene una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.

### <a name="kick-off-replication-appliance-setup"></a>Avviare l'installazione dell'appliance di replica

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore usando la password amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione dell'appliance di replica (strumento di configurazione di Azure Site Recovery) entro pochi secondi.
5. Immettere un nome da usare per registrare l'appliance con Migrazione server di Azure Migrate. Quindi fare clic su **Next**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure.
7. Attendere che lo strumento termini la registrazione di un'app Azure AD per identificare l'appliance. L'appliance viene riavviata.
1. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente entro pochi secondi.

### <a name="register-the-replication-appliance"></a>Registrare l'appliance di replica

Completare la configurazione e la registrazione dell'appliance di replica.

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività**.
2. Selezionare la scheda di interfaccia di rete (per impostazione predefinita ne è presente solo una) usata dall'appliance di replica per l'individuazione delle macchine virtuali e per eseguire un'installazione push del servizio Mobility nelle macchine virtuali di origine.
3. Selezionare la scheda di interfaccia di rete usata dall'appliance di replica per la connettività con Azure. Selezionare quindi **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
4. Se l'appliance è protetta da un server proxy, è necessario specificare le impostazioni del proxy.
    - Specificare il nome del proxy nel formato **http://ip-address** o **http://FQDN** . I server proxy HTTPS non sono supportati.
5. Quando viene chiesto di immettere i dettagli relativi alla sottoscrizione, ai gruppi di risorse e all'insieme di credenziali, aggiungere le informazioni annotate quando si è scaricato il modello di appliance.
6. In **Installa software di terze parti** accettare il contratto di licenza. Selezionare **Scarica e installa** per installare Server MySQL.
7. Selezionare **Installa VMware PowerCLI**. Prima di eseguire questa operazione, assicurarsi che tutte le finestre del browser siano chiuse. Selezionare quindi **Continua**.
8. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
9. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
10. Immettere le credenziali dell'account [creato](#prepare-an-account-for-automatic-discovery) per l'individuazione VMware. Selezionare **Aggiungi** > **Continua**.
11. In **Configura le credenziali della macchina virtuale** immettere le credenziali [create](#prepare-an-account-for-mobility-service-installation) per l'installazione push del servizio Mobility quando è stata abilitata la replica per le macchine virtuali.  
    - Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare.
    - Per Linux, specificare i dettagli dell'account radice.
12. Selezionare **Finalizza configurazione** per completare la registrazione.


Dopo la registrazione dell'appliance di replica, lo strumento Valutazione server di Azure Migrate si connette ai server VMware usando le impostazioni specificate e individua le macchine virtuali. È possibile visualizzare le macchine virtuali individuate in **Gestisci** > **Elementi individuati** nella scheda **Altro**.


## <a name="replicate-vms"></a>Replicare le VM

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server** e fare clic su **Replica**.

    ![Replicare le VM](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. In **Replica** > **Impostazioni origine** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata.
4. In **Server vCenter** specificare il nome del server vCenter che gestisce le VM, ovvero il server vSphere che le ospita.
5. In **Server di elaborazione** selezionare il nome dell'appliance di replica.
6. In **Credenziali guest** specificare l'account amministratore di macchine virtuali che verrà usato per l'installazione push del servizio Mobility. Fare quindi clic su **Avanti: Macchine virtuali**.

    ![Replicare le VM](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. In **Macchine virtuali** selezionare le macchine virtuali da replicare.

    - Se è stata eseguita una valutazione delle VM, è possibile applicare i consigli dei risultati della valutazione in merito al tipo di disco (Premium/Standard) e alle dimensioni delle VM. A questo scopo, in **Importare le impostazioni di migrazione da una valutazione di Azure Migrate?** selezionare **Sì**.
    - Se non è stata eseguita una valutazione o non si vogliono usare le impostazioni di valutazione, selezionare **No**.
    - Se si è scelto di usare la valutazione, selezionare il gruppo di VM e il nome della valutazione.

8. Selezionare ogni macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.
9. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione in cui eseguire la migrazione e quindi specificare il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione.
10. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
11. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

12. In **Calcolo** controllare il nome, le dimensioni, il tipo di disco del sistema operativo e il set di disponibilità delle VM. Le macchine virtuali devono essere conformi ai [requisiti di Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale conterrà le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la macchina virtuale. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Set di disponibilità**: se la macchina virtuale deve essere inclusa in un set di disponibilità di Azure dopo la migrazione, specificare il set. Il set deve trovarsi nel gruppo di risorse di destinazione specificato per la migrazione.

13. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

14. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Dopo l'avvio della replica le impostazioni non possono essere più modificate.




## <a name="track-and-monitor"></a>Tenere traccia e monitorare

- Quando si fa clic su **Replica** viene avviato un processo Avvia replica. 
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.


È possibile tenere traccia dello stato del processo nelle notifiche del portale.

![Traccia del processo](./media/tutorial-migrate-vmware-agent/jobs.png)

È possibile monitorare lo stato della replica facendo clic su **Replica dei server** in **Azure Migrate: Migrazione server**.
![Monitorare la replica](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Eseguire una migrazione di test


All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sui computer locali, che rimangono operativi, e che la replica continui. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Per eseguire una migrazione di test, seguire questa procedura:


1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Migrazione server** fare clic su **Testare i server con migrazione completata**.

     ![Testare i server con migrazione completata](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Fare clic con il pulsante destro del mouse sulla macchina virtuale da testare e scegliere **Migrazione di test**.

    ![Migrazione di test](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. In **Migrazione di test** selezionare la rete virtuale di Azure in cui verrà inserita la macchina virtuale di Azure dopo la migrazione. È consigliabile usare una rete virtuale non di produzione.
4. Verrà avviato il processo **Migrazione di test**. Monitorare il processo nelle notifiche del portale.
5. Al termine della migrazione, visualizzare la macchina virtuale di Azure di cui è stata eseguita la migrazione in **Macchine virtuali** nel portale di Azure. Il nome della macchina virtuale ha il suffisso **-Test**.
6. Al termine del test, fare clic con il pulsante destro del mouse sulla macchina virtuale di Azure in **Replica delle macchine virtuali** e scegliere **Pulisci migrazione di test**.

    ![Eseguire la pulizia della migrazione](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle macchine virtuali locali.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. In **Replica delle macchine virtuali** fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Esegui la migrazione**.
3. In **Esegui la migrazione** > **Spegnere le macchine virtuali ed eseguire una migrazione pianificata senza perdita di dati?** selezionare **Sì** > **OK**.
    - Per impostazione predefinita, Azure Migrate arresta la macchina virtuale locale ed esegue una replica su richiesta per sincronizzare le eventuali modifiche apportate alla macchina virtuale dopo l'ultima replica. Questa operazione assicura che non vi sia alcuna perdita di dati.
    - Se non si vuole arrestare la macchina virtuale, selezionare **No**
4. Verrà avviato un processo di migrazione per la VM. Tenere traccia del processo nelle notifiche di Azure.
5. Al termine del processo è possibile visualizzare e gestire la macchina virtuale dalla pagina **Macchine virtuali**.

## <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Arresta migrazione**. La replica della macchina virtuale locale verrà arrestata e verrà eseguita la pulizia delle informazioni sullo stato della replica della VM.
2. Installare l'agente [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
3. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
4. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
5. Trasferire il traffico all'istanza della macchina virtuale di Azure di cui è stata eseguita la migrazione.
6. Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
7. Rimuovere le macchine virtuali locali dai processi di backup locali.
8. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure. 

## <a name="post-migration-best-practices"></a>Procedure consigliate dopo la migrazione

- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuire [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
-  È consigliabile distribuire [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/overview) per monitorare l'utilizzo delle risorse e le spese.


## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.
