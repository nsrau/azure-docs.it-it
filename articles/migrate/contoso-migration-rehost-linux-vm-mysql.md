---
title: Eseguire il rehosting di un'app Service Desk Linux di Contoso in Azure e Azure MySQL | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app Linux in locale eseguendone la migrazione in macchine virtuali di Azure e Azure MySQL.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 4367bf7cb02bb6a1e343dc3fb171be731e15c32b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300712"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migrazione di Contoso: eseguire il rehosting di un'app Linux in locale in macchine virtuali di Azure e Azure MySQL

Questo articolo descrive come Contoso esegue il rehosting dell'app Service Desk Linux a due livelli in locale (osTicket) eseguendone la migrazione in Azure e Azure MySQL.

Questo documento è l'ottavo di una serie di articoli che illustrano in che modo l'azienda fittizia Contoso esegue la migrazione delle proprie risorse locali nel cloud di Microsoft Azure. La serie include informazioni di base e scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app locale a due livelli locale SmartHotel in esecuzione in VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting nella macchina virtuale di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale dell'App Web mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) a un'istanza gestita di database SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione di SmartHotel in macchine virtuali IaaS di Azure usando il servizio Site Recovery.
[Articolo 6: Eseguire il rehosting nelle macchine virtuali di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue la migrazione dell'app Linux osTicket nelle macchine virtuali IaaS di Azure usando Azure Site Recovery.
Articolo 8: Eseguire il rehosting di un'app Linux nelle macchine virtuali di Azure e in Azure MySQL Server (questo articolo) | Spiega in che modo Contoso esegue la migrazione dell'app Linux osTicket. Usa Site Recovery per la migrazione delle macchine virtuali e MySQL Workbench per eseguire la migrazione in un'istanza di Azure MySQL Server. | Disponibile

In questo articolo Contoso esegue la migrazione di un'app Service Desk PHP MySQL Apache Linux (LAMP), denominata osTicket, in Azure. Se si vuole usare questa app open source, è possibile scaricarla da [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Driver di business

Il team di responsabili IT ha lavorato a stretto contatto con i partner di business per comprenderne le effettive esigenze:

- **Gestire la crescita aziendale**: Contoso è in crescita e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Limitare i rischi**: l'app Service Desk è fondamentale per il business di Contoso. L'azienda intende migrarla ad Azure con zero rischi.
- **Estendere**: Contoso non desidera sostituire l'app subito, ma desidera semplicemente mantenerla stabile.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team cloud di Contoso ha stabilito gli obiettivi di questa migrazione, per determinare il metodo di migrazione più consono:

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali di cui dispone attualmente nell'ambiente VMware locale.  L'app manterrà la stessa importanza critica nel cloud come la corrispondente versione in locale. 
- Contoso non intende investire in questa app.  È importante per l'azienda, ma nella sua forma attuale intende semplicemente migrarla in modo sicuro nel cloud.
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
- Poiché l'azienda intende eseguire la migrazione di un carico di lavoro di produzione, le risorse risiederanno nel gruppo di risorse di produzione **ContosoRG**.
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
2. Dopo aver preparato i componenti di Azure e locali, si imposta e si abilita la replica per la macchina virtuale Web.
3. Dopo che la replica è operativa, si esegue la migrazione della macchina virtuale eseguendone il failover in Azure.

Per eseguire la migrazione del database:

1. Si esegue il provisioning di un'istanza di MySQL in Azure.
2. Si configura MySQL Workbench e si esegue il backup del database localmente.
3. Si ripristina successivamente il database dal backup locale in Azure.

![Processo di migrazione](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.
[Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/) | Il database si basa sul motore del server MySQL open source. Offre un database MySQL come servizio completamente gestito, di livello aziendale e supportato dalla community MySQL per lo sviluppo e la distribuzione di app. 

 
## <a name="prerequisites"></a>prerequisiti

Di seguito sono elencati i requisiti per eseguire questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Dovrebbe essere già stata creata una sottoscrizione nei primi articoli di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruttura di Azure** | Contoso configura la propria infrastruttura di Azure come descritto in [Azure infrastructure for migration](contoso-migration-infrastructure.md) (Infrastruttura di Azure per la migrazione).<br/><br/> Sono disponibili altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Server locali** | I server vCenter locali devono eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.
**VM locali** | [Rivedere i requisiti delle macchine virtuali Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) che sono supportati per la migrazione di Site Recovery.<br/><br/> Verificare il [file system e il sistema di archiviazione di Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passaggi dello scenario

Di seguito viene descritto il processo di completamento della migrazione di Azure:

> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure per Site Recovery**. Si crea un account di archiviazione di Azure in cui memorizzare i dati replicati e un insieme di credenziali dei Servizi di ripristino.
> * **Passaggio 2: Preparare VMware in locale per Site Recovery**. Si preparano gli account per l'individuazione di macchine virtuali e l'installazione dell'agente, quindi si prepara la connessione alle macchine virtuali di Azure dopo il failover.
 * **Passaggio 3: Eseguire il provisioning del database**. In Azure, eseguire il provisioning di un'istanza del database MySQL di Azure.
> * **Passaggio 4: Replicare le macchine virtuali**. Si configura l'ambiente di origine e di destinazione di Site Recovery, si impostano i criteri di replica e si avvia la replica delle macchine virtuali nell'archiviazione di Azure.
> * **Passaggio 5: Eseguire la migrazione del database**. Si configura la migrazione con gli strumenti di MySQL.
> * **Passaggio 6: Eseguire la migrazione delle macchine virtuali con Site Recovery**. Si effettua un failover di test per verificare che tutto funzioni come previsto e un failover completo per eseguire la migrazione delle macchine virtuali ad Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passaggio 1: Preparare Azure per il servizio Site Recovery

Contoso necessita di due componenti di Azure per Site Recovery:

- Una rete virtuale in cui si trovano le risorse di cui è stato eseguito il failover (Contoso utilizzerà la rete virtuale di produzione che ha già distribuito).
- Un nuovo account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Contoso ha già creato la rete virtuale durante la [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md), pertanto deve solo creare un account di archiviazione e un insieme di credenziali.


1. Si crea un account di archiviazione di Azure (**contosovmsacc20180528**) nell'area Stati Uniti orientali 2.

    - L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
    - Si usa un account per utilizzo generico con archiviazione standard e la replica dell'archiviazione con ridondanza locale.

    ![Archiviazione di Site Recovery](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Dopo avere configurato la rete e l'account di archiviazione, si crea un insieme di credenziali (ContosoMigrationVault) e lo inserisce nel gruppo di risorse **ContosoFailoverRG** nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Servono altre informazioni?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery

Contoso prepara l'infrastruttura VMware locale come indicato di seguito:

- Si crea un account nel server vCenter per l'individuazione automatica delle macchine virtuali.
- Si crea un account che consente l'installazione automatica del servizio Mobility nelle macchine virtuali VMware che si desidera replicare.
- Si preparano le macchine virtuali locali in modo che possano connettersi alle macchine virtuali di Azure create dopo la migrazione.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Contoso configura l'account come segue:

1. Crea un ruolo a livello di vCenter.
2. Assegna quindi al ruolo le autorizzazioni necessarie.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni macchina virtuale che Contoso vuole migrare.

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando si abilita la replica per le macchine virtuali.
- Per l'installazione automatica. Site Recovery necessita di un account con autorizzazioni per accedere alla macchina virtuale. 
- I dettagli dell'account vengono inseriti durante l'impostazione della replica. 
- L'account può essere un account di dominio o locale, purché disponga delle autorizzazioni di installazione.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure, in Contoso si intende connettersi alle macchine virtuali di Azure. A tale scopo occorre eseguire alcune operazioni: 

- Per accedere a Internet, si attiva il protocollo SSH nella macchina virtuale Linux in locale prima della migrazione.  Per Ubuntu questa operazione può essere eseguita con il comando seguente: **Sudo apt-get ssh install -y**.
- Dopo il failover, si deve selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.
- Se l'operazione non funziona, si verifica che la macchina virtuale sia in esecuzione e si rivedono questi [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-3-provision-azure-database-for-mysql"></a>Passaggio 3: Eseguire il provisioning del database di Azure per MySQL

In Contoso si esegue il provisioning di un'istanza del database MySQL nell'area primaria Stati Uniti orientali 2.

1. Nel portale di Azure si crea una risorsa per il database di Azure per MySQL. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Si aggiunge il nome **contosoosticket** per il database di Azure. Si aggiunge il database al gruppo di risorse di produzione **ContosoRG** e se ne specificano le credenziali.
3. Il database MySQL locale è la versione 5.7, pertanto si seleziona questa versione per compatibilità. Si usano le dimensioni predefinite, che corrispondono ai relativi requisiti di database.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Per **Opzioni di ridondanza per il backup** si seleziona l'utilizzo di **Con ridondanza geografica**. Questa opzione consente di ripristinare il database nell'area secondaria Stati Uniti centrali in caso di interruzione. Si può configurare questa opzione solo quando si esegue il provisioning del database.

     ![Ridondanza](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Nella rete **VNET-PROD-EUS2** > **Endpoint di servizio**, si aggiunge un endpoint di servizio (una subnet di database) per il servizio SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Dopo avere aggiunto la subnet, si crea una regola della rete virtuale che consente l'accesso dalla subnet del database nella rete di produzione.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Passaggio 4: Replicare le macchine virtuali locali

Prima di poter eseguire la migrazione della macchina virtuale Web in Azure, Contoso imposta e abilita la replica.

### <a name="set-a-protection-goal"></a>Impostare un obiettivo di protezione

1. Nel nome dell'insieme di credenziali (ContosoVMVault) Contoso seleziona un obiettivo di replica **Attività iniziali** > **Site Recovery** > **Preparare l'infrastruttura**.
2. Specifica che i computer si trovano in locale, che sono macchine virtuali VMware e che occorre eseguirne la replica in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, conferma di aver completato la pianificazione della distribuzione selezionando **Yes, I have done it** (Operazione completata). In questo scenario Contoso esegue la migrazione di una sola macchina virtuale, pertanto non ha bisogno di pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Contoso deve configurare l'ambiente di origine. Mediante un modello OVF, si distribuisce un server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.

Contoso esegue questi passaggi come indicato di seguito:


1. Scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la macchina virtuale.

    ![Modello OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Alla prima accensione, la macchina virtuale viene avviata con un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
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

14. Al termine della registrazione, Contoso verifica nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
15. Dopo che sono state completate tutte le impostazioni, Site Recovery si connette ai server VMware e individua le macchine virtuali.

### <a name="set-up-the-target"></a>Configurare la destinazione

In Contoso si specificano a questo punto le impostazioni della replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** seleziona le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione di Azure e una rete.


### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Dopo che l'origine e la destinazione sono impostate, il team di Contoso è pronto per creare i criteri della replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** crea i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
        ![Creare criteri di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Servono altre informazioni?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sull'agente guest di Azure per Linux.

### <a name="enable-replication-for-the-web-vm"></a>Abilitare la replica per la macchina virtuale Web

Contoso può a questo punto iniziare a replicare la macchina virtuale **OSTICKETWEB**.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Si indica di voler abilitare le macchine virtuali e selezionare le impostazioni di origine, incluso il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Si specificano a questo punto le impostazioni di destinazione. Tra queste impostazioni sono inclusi il gruppo di risorse e la rete in cui la macchina virtuale di Azure verrà posizionata dopo il failover e l'account di archiviazione in cui verranno memorizzati i dati replicati. 

     ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso seleziona **OSTICKETWEB** per la replica. 

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Nelle proprietà della macchina virtuale, si seleziona l'account che deve essere usato per installare automaticamente il servizio Mobility nella macchina virtuale.

     ![Servizio Mobility](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verifica che siano selezionati i criteri di replica corretti e seleziona **Abilitazione della replica**. Il servizio Mobility verrà installato automaticamente.
6.  Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.


**Servono altre informazioni?**

È possibile leggere una procedura dettagliata completa di tutti questi passaggi in [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Passaggio 5: Eseguire la migrazione del database

In Contoso si eseguirà la migrazione del database tramite backup e ripristino, con gli strumenti di MySQL. Si installa MySQL Workbench, si esegue il backup del database da OSTICKETMYSQL e quindi lo si ripristina in Database di Azure per il server MySQL.

### <a name="install-mysql-workbench"></a>Installare MySQL Workbench

1. Si verificano i [prerequisiti e si scarica MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Si installa MySQL Workbench per Windows in conformità con le [istruzioni di installazione](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. In MySQL Workbench si crea una connessione MySQL a OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Si esporta il database come **osticket**, in un file autonomo locale.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Dopo avere eseguito il backup del database localmente, si crea una connessione all'istanza di Database di Azure per MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Si può a questo punto importare (ripristinare) il database nell'istanza di MySQL di Azure, dal file autonomo. Viene creato un nuovo schema (osticket) per l'istanza.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Passaggio 6: Eseguire la migrazione delle macchine virtuali con Site Recovery

In Contoso si effettua un rapido failover di test e poi si esegue la migrazione della macchina virtuale.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

L'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto, prima della migrazione. 

1. Contoso effettua un failover di test al punto di ripristino più recente disponibile **Elaborato più recente**.
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene effettuato il failover di test: 

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Si verifica che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo la verifica, elimina il failover, registra e salva eventuali osservazioni.

### <a name="migrate-the-vm"></a>Eseguire la migrazione della macchina virtuale

Per eseguire la migrazione della macchina virtuale, si crea un piano di ripristino che include la macchina virtuale e si esegue il failover del piano in Azure.

1. Crea un piano e vi aggiunge **OSTICKETWEB**.

    ![Piano di ripristino](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Si esegue il failover del piano. Si seleziona il punto di ripristino più recente e si specifica che Site Recovery deve tentare di arrestare la macchina virtuale locale prima di avviare il failover. Contoso segue l'avanzamento del failover nella pagina **Processi**.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Durante il failover, il server vCenter genera comandi per arrestare le due macchine virtuali in esecuzione nell'host ESXi.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Dopo il failover, si verifica che la macchina virtuale di Azure venga visualizzata come previsto nel portale di Azure.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Dopo avere verificato la macchina virtuale, si completa la migrazione. Arresta la replica per la macchina virtuale e la relativa fatturazione di Site Recovery per la macchina virtuale.

    ![Failover](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come effettuare un failover di test. 
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come effettuare il failover in Azure.


### <a name="connect-the-vm-to-the-database"></a>Connettere la macchina virtuale al database

Il passaggio finale del processo di migrazione consiste nell'aggiornare la stringa di connessione dell'app in modo che punti a Database di Azure per MySQL. 

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

### <a name="security"></a>Sicurezza

Il team di sicurezza di Contoso esamina la macchina virtuale e il database per determinare eventuali problemi di sicurezza.

- Per controllare l'accesso, esamina i gruppi di sicurezza di rete (NSG) per la VM. I gruppi di sicurezza di rete vengono usati per assicurarsi che possa passare solo il traffico consentito all'applicazione.
- Il team considera di proteggere i dati sui dischi della macchina virtuale usando Crittografia dischi e Azure KeyVault.
- La comunicazione tra la macchina virtuale e l'istanza del database non è configurata per SSL. Sarà necessario eseguire questa operazione per proteggere il traffico del database.

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sulle procedure di sicurezza per le macchine virtuali.

### <a name="backups"></a>Backup

- In Contoso si eseguirà il backup dei dati nella macchina virtuale tramite il servizio Backup di Microsoft Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Non è necessario configurare il backup per il database. Database di Azure per MySQL crea automaticamente i backup del server e gli archivi. In Contoso è stato scelto di usare la ridondanza geografica per il database, in modo che sia resiliente e pronto per la produzione.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo la distribuzione delle risorse, si assegnano tag di Azure, in base alle decisioni prese durante la distribuzione dell'[infrastruttura di Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Non vi sono problemi di gestione delle licenze per i server Ubuntu di Contoso.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) su Gestione costi di Azure.


## <a name="next-steps"></a>Passaggi successivi

In questo scenario è stato descritto lo scenario di rehosting finale applicato da Contoso. È stata eseguita la migrazione della macchina virtuale di front-end dell'app osTicket Linux locale a una macchina virtuale di Azure ed è stata eseguita la migrazione del database dell'app a un'istanza di MySQL di Azure.

Nel prossimo set di esercitazioni della serie sulla migrazione, viene illustrato in che modo in Contoso viene eseguito un set più complesso di migrazioni, che include il refactoring dell'app, rispetto alle migrazioni in modalità lift-and-shift.
