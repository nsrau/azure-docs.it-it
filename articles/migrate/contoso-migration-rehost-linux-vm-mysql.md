---
title: Eseguire il rehosting di un'app Service Desk Linux di Contoso in Azure e Azure MySQL | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app Linux in locale eseguendone la migrazione in macchine virtuali di Azure e Azure MySQL.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 3cac893fcaafd4fe8d35aab2a10da92019d3ed42
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698965"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migrazione di Contoso: eseguire il rehosting di un'app Linux in locale in macchine virtuali di Azure e Azure MySQL

Questo articolo descrive come Contoso esegue il rehosting dell'app Service Desk Linux a due livelli in locale (osTicket) eseguendone la migrazione in Azure e Azure MySQL.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle proprie risorse locali al cloud di Microsoft Azure. La serie include informazioni di base e scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire l'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant. | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in un'istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile   
[Articolo 5: Effettuare il rehosting di un'app nelle macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 nelle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app SmartHotel360. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile 
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery | Disponibile
Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e Azure MySQL | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Questo articolo
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile 
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app nei contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione dell'app SmartHotel ad Azure. e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui il servizio app di Azure, servizio Azure Kubernetes, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile


In questo articolo Contoso esegue la migrazione di un'app Service Desk PHP MySQL Apache Linux (LAMP), denominata osTicket, in Azure. Se si vuole usare questa app open source, è possibile scaricarla da [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Driver di business

Il team di responsabili IT ha lavorato a stretto contatto con i partner di business per comprenderne le effettive esigenze:

- **Stare al passo con la crescita del business**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Limitare i rischi**: l'app Service Desk è fondamentale a livello aziendale. L'obiettivo è spostare l'app in Azure senza correre alcun rischio.
- **Estendere**:  Contoso non vuole sostituire l'app subito, semplicemente, intende mantenere stabili le app.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team cloud di Contoso ha stabilito gli obiettivi di questa migrazione, per determinare il metodo di migrazione più consono:

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali di cui dispone attualmente nell'ambiente VMware locale.  L'app manterrà la stessa importanza critica nel cloud come la corrispondente versione in locale. 
- Contoso non intende investire in questa app.  L'app è importante per l'azienda, ma Contoso vuole semplicemente eseguirne la migrazione sicura al cloud nella sua forma attuale.
- Dopo aver completato un paio di migrazioni di app di Windows, Contoso desidera scoprire come usare un'infrastruttura basata su Linux in Azure.
- Si desidera ridurre al minimo le attività di amministrazione del database dopo che l'applicazione è stata spostata nel cloud.

## <a name="proposed-architecture"></a>Architettura proposta

In questo scenario:

- L'app è su più livelli tra due macchine virtuali (OSTICKETWEB e OSTICKETMYSQL).
- Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- L'app a livello Web su OSTICKETWEB verrà migrata in una macchina virtuale IaaS di Azure.
- Il database dell'app verrà migrato nel servizio PaaS Database di Azure per MySQL.
- Poiché Contoso intende eseguire la migrazione di un carico di lavoro di produzione, le risorse risiederanno nel gruppo di risorse di produzione **ContosoRG**.
- Le risorse verranno replicate nell'area primaria (Stati Uniti orientali 2) e collegate alla rete di produzione (VNET-PROD-EUS2):
    - La macchina virtuale Web si troverà nella subnet di front-end (PROD-FE-EUS2).
    - L'istanza del database si troverà nella subnet del database (PROD-DB-EUS2).
- Il database dell'app verrà migrato in MySQL di Azure usando gli strumenti di MySQL.
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


![Architettura dello scenario](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Processo di migrazione

In Contoso il processo di migrazione verrà completato come indicato di seguito:

Per eseguire la migrazione della macchina virtuale Web:

1. Come primo passaggio, Contoso imposta l'infrastruttura di Azure e locale necessaria a distribuire Site Recovery.
2. Dopo aver preparato i componenti di Azure e locali, Contoso imposta e abilita la replica per la macchina virtuale Web.
3. Dopo che la replica è operativa, Contoso esegue la migrazione della macchina virtuale eseguendone il failover in Azure.

Per eseguire la migrazione del database:

1. Si esegue il provisioning di un'istanza di MySQL in Azure.
2. Contoso configura MySQL Workbench ed esegue il backup del database localmente.
3. Contoso ripristina successivamente il database dal backup locale in Azure.

![Processo di migrazione](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.
[Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/) | Il database si basa sul motore del server MySQL open source. Offre un database MySQL come servizio completamente gestito, di livello aziendale e supportato dalla community MySQL per lo sviluppo e la distribuzione di app. 

 
## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruttura di Azure** | Contoso configura l'infrastruttura di Azure come descritto in [Azure infrastructure for migration](contoso-migration-infrastructure.md) (Infrastruttura di Azure per la migrazione).<br/><br/> Altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Server locali** | Il server vCenter locale deve eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.
**VM locali** | [Rivedere i requisiti delle macchine virtuali Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) che sono supportati per la migrazione di Site Recovery.<br/><br/> Verificare il [file system e il sistema di archiviazione di Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco come gli amministratori di Contoso eseguiranno la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure per Site Recovery**: viene creato un account di archiviazione di Azure per i dati replicati e un insieme di credenziali di Servizi di ripristino.
> * **Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery**: vengono preparati gli account per l'individuazione delle macchine virtuali e l'installazione dell'agente, in preparazione alla connessione alle macchine virtuali di Azure dopo il failover.
 * **Passaggio 3: Effettuare il provisioning del database]**: In Azure, eseguire il provisioning di un'istanza del database MySQL di Azure.
> * **Passaggio 4: Replicare le macchine virtuali**: configurare l'ambiente di origine e di destinazione di Site Recovery, impostare i criteri di replica e avviare la replica delle macchine virtuali nell'archiviazione di Azure.
> * **Passaggio 5: eseguire la migrazione del database**. Viene configurata la migrazione con gli strumenti di MySQL.
> * **Passaggio 6: Eseguire la migrazione delle macchine virtuali con Site Recovery**: infine, vengono effettuati un failover di test per verificare che tutto funzioni come previsto e un failover completo per eseguire la migrazione delle macchine virtuali ad Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passaggio 1: Preparare Azure per il servizio Site Recovery

Contoso necessita di due componenti di Azure per Site Recovery:

- Una rete virtuale in cui si trovano le risorse di cui è stato effettuato il failover. Contoso già creato la rete virtuale durante [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md)
- Un nuovo account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Gli amministratori di Contoso creano un account di archiviazione e l'insieme di credenziali come indicato di seguito:

1. Creano un account di archiviazione (**contosovmsacc20180528**) nell'area Stati Uniti orientali 2.

    - L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
    - Usano un account per utilizzo generico con archiviazione standard e la replica dell'archiviazione con ridondanza locale.

    ![Archiviazione di Site Recovery](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Dopo avere configurato la rete e l'account di archiviazione, gli amministratori creano un insieme di credenziali (ContosoMigrationVault) e lo inseriscono nel gruppo di risorse **ContosoFailoverRG** nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery

Gli amministratori di Contoso preparano l'infrastruttura VMware locale come indicato di seguito:

- Creano un account nel server vCenter per l'individuazione automatica delle macchine virtuali.
- Creano un account che consente l'installazione automatica del servizio Mobility nelle macchine virtuali VMware da replicare.
- Preparano le macchine virtuali locali in modo da poter connettersi alle macchine virtuali di Azure create dopo la migrazione.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Per configurare l'account, gli amministratori di Contoso seguono questa procedura:

1. Crea un ruolo a livello di vCenter.
2. Assegnano quindi al ruolo le autorizzazioni necessarie.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni macchina virtuale che Contoso vuole migrare.

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando si abilita la replica per le macchine virtuali.
- Per l'installazione automatica. Site Recovery necessita di un account con autorizzazioni per accedere alla macchina virtuale. 
- I dettagli dell'account vengono inseriti durante l'impostazione della replica. 
- L'account può essere un account di dominio o locale, purché disponga delle autorizzazioni di installazione.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure, in Contoso si intende connettersi alle macchine virtuali di Azure. A tale scopo, gli amministratori di Contoso dovranno eseguire le operazioni seguenti:

- Per accedere a Internet, si attiva il protocollo SSH nella macchina virtuale Linux in locale prima della migrazione.  Per Ubuntu questa operazione può essere eseguita con il comando seguente: **Sudo apt-get ssh install -y**.
- Dopo il failover, si deve selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.
- Se l'operazione non funziona, si verifica che la macchina virtuale sia in esecuzione e si rivedono questi [suggerimenti per la risoluzione dei problemi](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-3-provision-azure-database-for-mysql"></a>Passaggio 3: eseguire il provisioning del database di Azure per MySQL

In Contoso gli amministratori eseguono il provisioning di un'istanza del database MySQL nell'area primaria Stati Uniti orientali 2.

1. Nel portale di Azure si crea una risorsa per il database di Azure per MySQL. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Si aggiunge il nome **contosoosticket** per il database di Azure. Si aggiunge il database al gruppo di risorse di produzione **ContosoRG** e se ne specificano le credenziali.
3. Il database MySQL locale è la versione 5.7, pertanto si seleziona questa versione per compatibilità. Si usano le dimensioni predefinite, che corrispondono ai relativi requisiti di database.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Per **Opzioni di ridondanza per il backup**, selezionano l'uso **Con ridondanza geografica**. Questa opzione consente di ripristinare il database nell'area secondaria Stati Uniti centrali in caso di interruzione. Si può configurare questa opzione solo quando si esegue il provisioning del database.

     ![Ridondanza](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Nella rete **VNET-PROD-EUS2** > **Endpoint di servizio**, si aggiunge un endpoint di servizio (una subnet di database) per il servizio SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Dopo avere aggiunto la subnet, si crea una regola della rete virtuale che consente l'accesso dalla subnet del database nella rete di produzione.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Passaggio 4: Replicare le macchine virtuali locali

Prima di poter eseguire la migrazione della macchina virtuale Web in Azure, gli amministratori di Contoso configurano e abilitano la replica.

### <a name="set-a-protection-goal"></a>Impostare un obiettivo di protezione

1. Nel nome dell'insieme di credenziali (ContosoVMVault) Contoso seleziona un obiettivo di replica **Attività iniziali** > **Site Recovery** > **Preparare l'infrastruttura**.
2. Specifica che i computer si trovano in locale, che sono macchine virtuali VMware e che occorre eseguirne la replica in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, conferma di aver completato la pianificazione della distribuzione selezionando **Yes, I have done it** (Operazione completata). In questo scenario Contoso esegue la migrazione di una sola macchina virtuale, pertanto non ha bisogno di pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Gli amministratori Contoso devono ora configurare l'ambiente di origine. Mediante un modello OVF, si distribuisce un server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.

Gli amministratori di Contoso procedono come segue:


1. Scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la macchina virtuale.

    ![Modello OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Al primo avvio della VM, viene visualizzata un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento Contoso specifica un nome per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure.
7. Dopo aver stabilito la connessione, esegue l'accesso alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si registra il server di configurazione.

    ![Registrare il server di configurazione](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
9. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
10. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
11. Seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.

    ![insieme di credenziali](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. A questo punto si scaricano e si installano il server MySQL e VMware PowerCLI. 
13. Dopo la convalida, specifica l'indirizzo IP o l'FQDN dell'host vSphere o del server vCenter. Mantiene la porta predefinita e specifica un nome descrittivo per il server vCenter.
14. Si specificano l'account creato per l'individuazione automatica e le credenziali che Site Recovery userà per installare automaticamente il servizio Mobility. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Al termine della registrazione, nel portale di Azure gli amministratori verificano che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
15. Dopo che sono state completate tutte le impostazioni, Site Recovery si connette ai server VMware e individua le macchine virtuali.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora gli amministratori di Contoso inseriscono le impostazioni della replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** vengono selezionate le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione di Azure e una rete.


### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Dopo che l'origine e la destinazione sono impostate, gli amministratori di Contoso sono pronti per creare i criteri della replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** vengono creati i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**: Il valore predefinito è 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
        ![Creare criteri di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Ulteriore assistenza?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sull'agente guest di Azure per Linux.

### <a name="enable-replication-for-the-web-vm"></a>Abilitare la replica per la macchina virtuale Web

A questo punto gli amministratori di Contoso possono iniziare a replicare la macchina virtuale **OSTICKETWEB**.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Si indica di voler abilitare le macchine virtuali e selezionare le impostazioni di origine, incluso il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Si specificano a questo punto le impostazioni di destinazione. Tra queste impostazioni sono inclusi il gruppo di risorse e la rete in cui la macchina virtuale di Azure verrà posizionata dopo il failover e l'account di archiviazione in cui verranno memorizzati i dati replicati. 

     ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Selezionano **OSTICKETWEB** per la replica. 

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Nelle proprietà della macchina virtuale, si seleziona l'account che deve essere usato per installare automaticamente il servizio Mobility nella macchina virtuale.

     ![Servizio Mobility](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verifica che siano selezionati i criteri di replica corretti e seleziona **Abilitazione della replica**. Il servizio Mobility verrà installato automaticamente.
6.  Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.


**Ulteriore assistenza?**

È possibile leggere una procedura dettagliata completa di tutti questi passaggi in [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Passaggio 5: Eseguire la migrazione del database

Gli amministratori di Contoso eseguiranno la migrazione del database tramite backup e ripristino, con gli strumenti di MySQL. Si installa MySQL Workbench, si esegue il backup del database da OSTICKETMYSQL e quindi lo si ripristina in Database di Azure per il server MySQL.

### <a name="install-mysql-workbench"></a>Installare MySQL Workbench

1. Verificano i [prerequisiti e scaricano MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Si installa MySQL Workbench per Windows in conformità con le [istruzioni di installazione](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. In MySQL Workbench si crea una connessione MySQL a OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Si esporta il database come **osticket**, in un file autonomo locale.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Dopo avere eseguito il backup del database localmente, si crea una connessione all'istanza di Database di Azure per MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. A questo punto, possono importare (ripristinare) il database nell'istanza di MySQL di Azure, dal file autonomo. Viene creato un nuovo schema (osticket) per l'istanza.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Passaggio 6: Eseguire la migrazione delle macchine virtuali con Site Recovery

Infine, gli amministratori Contoso effettuano un rapido failover di test e poi eseguono la migrazione della macchina virtuale.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

L'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto, prima della migrazione. 

1. Gli amministratori effettuano un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene eseguito il failover di test: 

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Verifica che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo la verifica, elimina il failover, registra e salva eventuali osservazioni.

### <a name="migrate-the-vm"></a>Eseguire la migrazione della macchina virtuale

Per eseguire la migrazione della macchina virtuale, gli amministratori di Contoso creano un piano di ripristino che include la macchina virtuale ed eseguono il failover del piano in Azure.

1. Creano un piano e vi aggiungono **OSTICKETWEB**.

    ![Piano di ripristino](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Esegue il failover nel piano. Si seleziona il punto di ripristino più recente e si specifica che Site Recovery deve tentare di arrestare la macchina virtuale locale prima di avviare il failover. Contoso segue l'avanzamento del failover nella pagina **Processi**.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Durante il failover, il server vCenter genera comandi per arrestare le due macchine virtuali in esecuzione nell'host ESXi.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Dopo il failover, verifica che la VM di Azure venga visualizzata come previsto nel portale di Azure.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Dopo avere verificato la macchina virtuale, si completa la migrazione. Arresta la replica per la macchina virtuale e la relativa fatturazione di Site Recovery per la macchina virtuale.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come eseguire un failover di test. 
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come effettuare il failover in Azure.


### <a name="connect-the-vm-to-the-database"></a>Connettere la macchina virtuale al database

Il passaggio finale del processo di migrazione consiste nell'aggiornamento della stringa di connessione dell'app da parte degli amministratori in modo che punti a Database di Azure per MySQL. 

1. Si crea una connessione SSH alla macchina virtuale OSTICKETWEB tramite Putty o un altro client SSH. La macchina virtuale è privata, pertanto Contoso si connette usando l'indirizzo IP privato.

    ![Connettersi al database](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Connettersi al database](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Si aggiornano le impostazioni in modo che la macchina virtuale **OSTICKETWEB** possa comunicare con il database **OSTICKETMYSQL**. Attualmente la configurazione è hardcoded con l'indirizzo IP locale 172.16.0.43.

    **Prima dell'aggiornamento**
    
    ![Aggiornare l'indirizzo IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Dopo l'aggiornamento**
    
    ![Aggiornare l'indirizzo IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Aggiornare l'indirizzo IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Contoso riavvia il servizio con **systemctl restart apache2**.

    ![Riavvio](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Si aggiornano infine i record DNS per **OSTICKETWEB**, in uno dei controller di dominio Contoso.

    ![Aggiornare il DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della migrazione, i livelli dell'app osTicket sono in esecuzione nelle macchine virtuali di Azure.

Occorre a questo punto eseguire le operazioni seguenti: 
- Rimuovere le macchine virtuali VMware dall'inventario vCenter.
- Rimuovere le macchine virtuali in locale dai processi di backup locali.
- Aggiornare la documentazione interna in modo da mostrare i nuovi percorsi e gli indirizzi IP. 
- Esaminare le risorse che interagiscono con le macchine virtuali locali e aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.
- In Contoso è stato usato il servizio Azure Migrate con il mapping delle dipendenze per valutare la macchina virtuale **OSTICKETWEB** per la migrazione. Occorre ora rimuovere dalla macchina virtuale gli agenti (Microsoft Monitoring Agent/Dependency Agent) installati per questo scopo.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Con l'app in esecuzione, occorre rendere pienamente operativa e sicura la nuova infrastruttura.

### <a name="security"></a>Security

Il team di sicurezza di Contoso esamina la macchina virtuale e il database per determinare eventuali problemi di sicurezza.

- Per controllare l'accesso, esamina i gruppi di sicurezza di rete (NSG) per la VM. I gruppi di sicurezza di rete vengono usati per assicurarsi che possa passare solo il traffico consentito all'applicazione.
- Il team considera di proteggere i dati sui dischi della macchina virtuale usando Crittografia dischi e Azure KeyVault.
- La comunicazione tra la macchina virtuale e l'istanza del database non è configurata per SSL. Sarà necessario eseguire questa operazione per proteggere il traffico del database.

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sulle procedure di sicurezza per le macchine virtuali.

### <a name="bcdr"></a>BCDR

Per la continuità aziendale e il ripristino di emergenza, Contoso esegue le azioni seguenti:

- **Proteggere i dati**: Contoso esegue il backup dei dati nell'app delle macchine virtuali usando il servizio Backup di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Non è necessario configurare il backup per il database. Database di Azure per MySQL crea e archivia automaticamente i backup del server. In Contoso è stato scelto di usare la ridondanza geografica per il database, in modo che sia resiliente e pronto per la produzione.
- **Mantenere le app in esecuzione**: Contoso esegue la replica delle macchine virtuali dell'app in Azure in un'area secondaria usando Site Recovery. [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)


### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo la distribuzione delle risorse, si assegnano tag di Azure, in base alle decisioni prese durante la distribuzione dell'[infrastruttura di Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Non vi sono problemi di gestione delle licenze per i server Ubuntu di Contoso.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.


## <a name="next-steps"></a>Passaggi successivi

In questo scenario è stato descritto lo scenario di rehosting finale. Contoso ha eseguito la migrazione della macchina virtuale di front-end dell'app osTicket Linux locale a una macchina virtuale di Azure ed è stata eseguita la migrazione del database dell'app a un'istanza di MySQL di Azure.

Nel prossimo set di esercitazioni della serie sulla migrazione, viene illustrato in che modo in Contoso viene eseguito un set più complesso di migrazioni, che include il refactoring dell'app, rispetto alle migrazioni in modalità lift-and-shift.
