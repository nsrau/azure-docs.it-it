---
title: Rehosting di un'app Linux locale per le macchine virtuali di Azure| Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app Linux locale eseguendone la migrazione nelle macchine virtuali di Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 6c96beee347a7a36a3dc04ecf8cd994484fd6bb7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007251"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migrazione Contoso: eseguire il rehosting di un'app Linux locale nelle macchine virtuali di Azure

In questo articolo viene illustrato come Contoso esegue il rehosting di un'app service desk basata su Linux (**osTicket**) locale nelle macchine virtuali IaaS Azure.

Questo documento fa parte di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni di fondo e scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale front-end dell'app mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il servizio [Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel alle macchine virtuali di Azure. | Disponibile
[Articolo 6: Eseguire il rehosting nelle macchine virtuali di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure | Spiega in che modo Contoso esegue la migrazione dell'app Linux osService usando Azure Site Recovery. | Questo articolo.
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osService di Linux usando Site Recovery per la migrazione delle macchine virtuali, nonché MySQL Workbench per la migrazione a un'istanza di Azure MySQL Server. | Disponibile
[Articolo 9: Refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Disponibile
[Articolo 10: Refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app osTicket Linux alle app Web di Azure in più siti, integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Viene illustrato come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Viene illustrato come Contoso ricompila le app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app di Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile

In questo articolo Contoso eseguirà la migrazione in Azure dell'app **osTicket** a due livelli in esecuzione su Linux Apache MySQL PHP (LAMP). La migrazione delle macchine virtuali dell'app avverrà tramite il servizio Azure Site Recovery. Se si vuole usare questa app open source, è possibile scaricarla da [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Driver di business

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in crescita e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Limitare i rischi**: l'app Service Desk è fondamentale per il business di Contoso. L'azienda intende migrarla ad Azure con zero rischi.
- **Estendere**: Non desidera sostituire l'app subito, ma semplicemente garantirne la stabilità.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team cloud di Contoso ha stabilito gli obiettivi di questa migrazione, per determinare il metodo di migrazione più consono:

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali di cui dispone attualmente nell'ambiente VMware locale.  L'app manterrà la stessa importanza critica nel cloud come la corrispondente versione in locale. 
- Contoso non intende investire in questa app.  È importante per l'azienda, ma nella sua forma corrente intende semplicemente migrarla in modo sicuro al cloud.
- Contoso non intende modificare il modello operativo dell'app. Punta all'interazione nel cloud esattamente come avviene ora.
- Contoso non intende modificare le funzionalità dell'app. Cambierà solo la posizione.
- Dopo aver completato un paio di migrazioni di app di Windows, Contoso desidera scoprire come usare un'infrastruttura basata su Linux in Azure.

## <a name="proposed-architecture"></a>Architettura proposta

In questo scenario:

- L'app è su più livelli tra due macchine virtuali (OSTICKETWEB e OSTICKETMYSQL).
- Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (**contoso-datacenter**) con un controller di dominio locale (**contosodc1**).
- Poiché l'azienda intende eseguire la migrazione di un carico di lavoro di produzione, le macchine virtuali in Azure risiederanno nel gruppo di risorse di produzione **ContosoRG**.
- Le risorse verranno migrate nell'area primaria (Stati Uniti orientali 2) e collegate alla rete di produzione (VNET-PROD-EUS2):
    - La macchina virtuale Web si troverà nella subnet di front-end (PROD-FE-EUS2).
    - La macchina virtuale del database si troverà nella subnet del database (PROD-DB-EUS2).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>Processo di migrazione

Contoso eseguirà la migrazione nel modo seguente:

1. Come primo passaggio, Contoso imposta l'infrastruttura di Azure e locale necessaria a distribuire Site Recovery.
2. Dopo aver preparato i componenti di Azure e locali, imposta e abilita la replica per le macchine virtuali.
3. Non appena la replica sarà funzionante, eseguirà la migrazione delle macchine virtuali effettuandone il failover in Azure.

![Processo di migrazione](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.

 
## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario:

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Dovrebbe essere già stata creata una sottoscrizione nei primi articoli di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruttura di Azure** | Contoso configura la propria infrastruttura di Azure come descritto in [Azure infrastructure for migration](contoso-migration-infrastructure.md) (Infrastruttura di Azure per la migrazione).<br/><br/> Altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Server locali** | I server vCenter locali devono eseguire la versione 5.5, 6.0 o 6.5.<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.
**VM locali** | [Rivedere le macchine Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) supportate per la migrazione con Site Recovery.<br/><br/> Verificare il [file system e il sistema di archiviazione di Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passaggi dello scenario

Di seguito viene descritto il processo di completamento della migrazione di Azure:

> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure per Site Recovery**. Crea un account di archiviazione di Azure in cui memorizzare i dati replicati e un insieme di credenziali dei Servizi di ripristino.
> * **Passaggio 2: Preparare VMware in locale per Site Recovery**. Si preparano gli account da usare per individuare le macchine virtuali e installare l'agente, quindi si prepara la connessione alle macchine virtuali di Azure dopo il failover.
> * **Passaggio 3: Replicare le macchine virtuali**. Si configura l'ambiente di migrazione di origine e di destinazione, si creano i criteri di replica e si avvia la replica delle macchine virtuali nell'archiviazione di Azure.
> * **Passaggio 4: Eseguire la migrazione delle macchine virtuali con Site Recovery**. Effettua un failover di test per verificare che tutto funzioni come previsto e un failover completo per migrare le VM ad Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passaggio 1: Preparare Azure per il servizio Site Recovery

Contoso necessita di due componenti di Azure per Site Recovery:

- Una rete virtuale in cui si trovano le risorse di cui è stato eseguito il failover (Contoso utilizzerà la rete virtuale di produzione che ha già distribuito)
- Un nuovo account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Contoso ha già creato la rete virtuale durante la [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md), pertanto deve solo creare un account di archiviazione e un insieme di credenziali.

1. Contoso crea un account di archiviazione di Azure (contosovmsacc20180528) nell'area Stati Uniti orientali 2.

    - L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
    - Viene usato un account di utilizzo generico con archiviazione standard e replica dell'archiviazione con ridondanza locale.

    ![Archiviazione di Site Recovery](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Dopo avere configurato la rete e l'account di archiviazione, Contoso crea un insieme di credenziali (ContosoMigrationVault) e lo inserisce nel gruppo di risorse **ContosoFailoverRG** nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery

Contoso prepara l'infrastruttura VMware locale come indicato di seguito:

- Crea un account nel server vCenter o nell'host ESXi vSphere per rendere automatica l'individuazione delle macchine virtuali.
- Crea un account che consente l'installazione automatica del servizio Mobility nelle macchine virtuali VMware che si desidera replicare.
- Prepara le macchine virtuali locali in modo che possano connettersi alle macchine virtuali di Azure create dopo la migrazione.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Per configurare l'account, Contoso segue questa procedura:

1. Crea un ruolo a livello di vCenter.
2. Assegna quindi al ruolo le autorizzazioni necessarie.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato nelle macchine virtuali Linux che Contoso sta migrando:

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando si abilita la replica per le macchine virtuali.
- Per l'installazione push automatica è necessario preparare un account che Site Recovery userà per accedere alle macchine virtuali.
- I dettagli dell'account vengono inseriti durante l'impostazione della replica. 
- L'account può essere di dominio o locale, con le autorizzazioni per l'installazione nelle macchine virtuali.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure, Contoso intende connettersi alle macchine virtuali di Azure replicate. Deve a tale scopo effettuare alcune operazioni:

- Per accedere a Internet, attiva il protocollo SSH nella macchina virtuale Linux in locale prima della migrazione.  Per Ubuntu questa operazione può essere eseguita con il comando seguente: **Sudo apt-get ssh install -y**.
- Dopo aver eseguito la migrazione, deve selezionare **Boot diagnostics** (Diagnostica di avvio) per visualizzare una screenshot della macchina virtuale.
- Se l'operazione non funziona, verifica che la macchina virtuale sia in esecuzione e rivede questi [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Passaggio 3: Replicare le macchine virtuali locali

Prima di poter eseguire la migrazione della macchina virtuale Web in Azure, Contoso imposta e abilita la replica.

### <a name="set-a-protection-goal"></a>Impostare un obiettivo di protezione

1. Nel nome dell'insieme di credenziali (ContosoVMVault) Contoso seleziona un obiettivo di replica **Attività iniziali** > **Site Recovery** > **Preparare l'infrastruttura**.
2. Specifica che i computer si trovano in locale, che sono macchine virtuali VMware e che occorre eseguirne la replica in Azure.
    ![Obiettivo di replica](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, conferma di aver completato la pianificazione della distribuzione selezionando **Yes, I have done it** (Operazione completata). In questo scenario Contoso esegue la migrazione di una sola macchina virtuale, pertanto non ha bisogno di pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Contoso deve configurare l'ambiente di origine. A tale scopo, scarica un modello OVF e lo usa per distribuire il server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.

Contoso esegue questi passaggi come indicato di seguito:

1. Scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la macchina virtuale.

    ![Modello OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Al primo avvio della VM, viene visualizzata un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento Contoso specifica un nome per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, esegue l'accesso alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.

    ![Registrare il server di configurazione](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
9. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
10. Seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.

    ![insieme di credenziali](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. A questo punto scarica e installa il server MySQL e VMware PowerCLI. 
12. Dopo la convalida, specifica l'indirizzo IP o il nome di dominio completo dell'host vSphere o del server vCenter. Mantiene la porta predefinita e specifica un nome descrittivo per il server vCenter.
13. Specifica l'account creato per l'individuazione automatica e le credenziali usate per installare automaticamente il servizio Mobility.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Al termine della registrazione, Contoso verifica nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
15. Site Recovery si connette quindi ai server VMware e individua le macchine virtuali.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora Contoso configura le impostazioni di replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** seleziona le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione di Azure e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Una volta impostate l'origine e la destinazione, Contoso è pronta per creare i criteri di replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** crea i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
        ![Creare criteri di replica](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Ulteriore assistenza?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sull'agente guest di Azure per Linux.

**Servono altre informazioni?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sull'agente guest di Azure per Linux.



### <a name="enable-replication-for-osticketweb"></a>Abilitare la replica per OSTICKETWEB

Contoso può a questo punto iniziare a replicare la macchina virtuale **OSTICKETWEB**.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Indica che intende abilitare le macchine virtuali e seleziona le impostazioni di origine, incluso il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Indica le impostazioni di destinazione, inclusi il gruppo di risorse e la rete virtuale in cui la macchina virtuale di Azure verrà posizionata dopo il failover e l'account di archiviazione in cui verranno memorizzati i dati replicati.

     ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Contoso seleziona **OSTICKETWEB** per la replica. 

    - In questa fase Contoso seleziona solo **OSTICKETWEB** perché la rete virtuale e la subnet devono essere selezionate e le macchine virtuali non sono nella stessa subnet.
    - Quando viene abilitata la replica, Site Recovery installa automaticamente il servizio Mobility nella macchina virtuale.

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Nelle proprietà della macchina virtuale Contoso seleziona l'account usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.

     ![Servizio Mobility](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verifica che siano selezionati i criteri di replica corretti e seleziona **Abilitazione della replica**.
6.  Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.



### <a name="enable-replication-for-osticketmysql"></a>Abilitare la replica per OSTICKETMYSQL

Ora Contoso può iniziare a replicare **OSTICKETMYSQL**.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine e di destinazione.
2. Contoso seleziona **OSTICKETMYSQL** per la replica e seleziona l'account da usare per l'installazione del servizio Mobility.

    ![Abilitare la replica](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso applica gli stessi criteri di replica usati per OSTICKETWEB e abilita la replica.  

**Servono altre informazioni?**

È possibile leggere una procedura dettagliata completa di tutti questi passaggi in [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Passaggio 4: Eseguire la migrazione delle macchine virtuali 

Contoso effettua un rapido failover di test e quindi esegue la migrazione delle macchine virtuali.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

L'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto prima della migrazione. 

1. Contoso esegue un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene eseguito il failover di test: 
    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.
3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Verifica che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo la verifica, elimina il failover, registra e salva eventuali osservazioni.

### <a name="create-and-customize-a-recovery-plan"></a>Creare e personalizzare un piano di ripristino

 Dopo aver verificato che il failover di test funzioni come previsto, Contoso crea un piano di ripristino per la migrazione. 

- Un piano di ripristino specifica l'ordine in cui si verifica il failover e indica in che modo le macchine virtuali di Azure verranno attivate in Azure.
- Poiché l'app è a due livelli, l'azienda personalizza il piano di ripristino in modo che la macchina virtuale dei dati (SQLVM) venga avviata prima della macchina virtuale front-end (WEBVM).


1. In **Piani di ripristino (Site Recovery)** > **+Piano di ripristino**, crea un piano e vi aggiunge le macchine virtuali.

    ![Piano di ripristino](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Dopo aver creato il piano, lo seleziona per la personalizzazione **Recovery Plans** > **OsTicketMigrationPlan** > **Customize** (Piani di ripristino > OsTicketMigrationPlan > Personalizza).
3.  Rimuove **OSTICKETWEB** da **Group 1: Start** (Gruppo 1: avvio).  In questo modo la prima azione di avvio avrà effetto solo su **OSTICKETMYSQL**.

    ![Gruppo di ripristino](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  In **+Group** > **Add protected items** (+Gruppo > Aggiungi elementi protetti) aggiunge **OSTICKETWEB** a **Group 2: Start** (Gruppo 2: Avvio).  Contoso deve averli in due gruppi distinti.

    ![Gruppo di ripristino](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Eseguire la migrazione delle macchine virtuali


Contoso è pronto per eseguire un failover sul piano di ripristino, per migrare le macchine virtuali.

1. Seleziona il piano > **Failover**.
2.  Sceglie di effettuare il failover al punto di ripristino più recente e tenta di arrestare la macchina virtuale locale tramite Site Recovery prima che venga attivato il failover. Contoso segue l'avanzamento del failover nella pagina **Processi**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)

3. Durante il failover, il server vCenter genera comandi per arrestare le due macchine virtuali in esecuzione nell'host ESXi.

    ![Failover](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Dopo il failover, Contoso verifica che la macchina virtuale di Azure venga visualizzata come previsto nel portale di Azure.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Dopo aver verificato la macchina virtuale in Azure completa la migrazione per terminare il processo di migrazione per ogni macchina virtuale. Arresta la replica per la macchina virtuale e la relativa fatturazione di Site Recovery per la macchina virtuale.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Connettere la macchina virtuale al database

Come passaggio finale del processo di migrazione Contoso aggiorna la stringa di connessione dell'applicazione in modo che punti al database dell'app in esecuzione sulla macchina virtuale **OSTICKETMYSQL**. 

1. Crea una connessione SSH alla macchina virtuale **OSTICKETWEB** tramite Putty o un altro client SSH. La macchina virtuale è privata, pertanto Contoso si connette usando l'indirizzo IP privato.

    ![Connettersi al database](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Connettersi al database](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Contoso aggiorna le impostazioni in modo che la macchina virtuale **OSTICKETWEB** possa comunicare con la macchina virtuale **OSTICKETMYSQL**. Attualmente la configurazione è hardcoded con l'indirizzo IP locale 172.16.0.43.

    **Prima dell'aggiornamento**
    
    ![Aggiornare l'indirizzo IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Dopo l'aggiornamento**
    
    ![Aggiornare l'indirizzo IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Contoso riavvia il servizio con **systemctl restart apache2**.

    ![Riavvio](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Aggiorna infine i record DNS per **OSTICKETWEB** e **OSTICKETMYSQL**, in uno dei controller di dominio Contoso.

    ![Aggiornare il DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Aggiornare il DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come eseguire un failover di test. 
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come eseguire il failover in Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della migrazione, i livelli dell'app osTicket sono in esecuzione nelle macchine virtuali di Azure.

Ora Contoso ha bisogno di fare un po' di pulizia:  

- Rimuove le macchine virtuali locali dall'inventario vCenter.
- Rimuove le macchine virtuali locali dai processi di backup locali.
- Aggiorna la documentazione interna per mostrare la nuova posizione e gli indirizzi IP per OSTICKETWEB e OSTICKETMYSQL.
- Esamina le risorse che interagiscono con le macchine virtuali e aggiorna eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.
- Contoso ha usato il servizio Azure Migrate con il mapping delle dipendenze per valutare le macchine virtuali per la migrazione. Deve ora rimuovere dalla macchina virtuale gli agenti (Microsoft Monitoring Agent/Dependency Agent) installati per questo scopo.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Con l'app in esecuzione, Contoso deve rendere pienamente operativa e proteggere la nuova infrastruttura.

### <a name="security"></a>Sicurezza

Il team di sicurezza di Contoso esamina le macchine virtuali OSTICKETWEB e OSTICKETMYSQL per determinare eventuali problemi di sicurezza.

- Per controllare l'accesso, esamina i gruppi di sicurezza di rete (NSG) per le macchine virtuali. I gruppi di sicurezza di rete vengono usati per assicurarsi che possa passare solo il traffico consentito all'applicazione.
- Considera inoltre l'opportunità di proteggere i dati sui dischi delle macchine virtuali usando la crittografia del disco e Azure KeyVault.

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sulle procedure di sicurezza per le macchine virtuali.

### <a name="backups"></a>Backup

Contoso esegue il backup dei dati nelle macchine virtuali mediante il servizio Backup di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo la distribuzione delle risorse, Contoso assegna tag di Azure, in base alle decisioni prese durante la [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso non ha problemi di licenza con i propri server Ubuntu.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo abbiamo mostrato come Contoso ha migrato un'app del service desk locale a più livelli su due macchine virtuali Linux su macchine virtuali Azure IaaS, usando Azure Site Recovery.

Nel prossimo articolo della serie mostreremo come Contoso esegue la migrazione della stessa app del service desk in Azure. Questa volta Contoso usa Site Recovery per migrare la macchina virtuale front-end per l'app e migra il database dell'app usando il backup e il ripristino in Database di Azure per MySQL, con lo strumento workbench MySQL. [Introduzione](contoso-migration-rehost-linux-vm-mysql.md).
