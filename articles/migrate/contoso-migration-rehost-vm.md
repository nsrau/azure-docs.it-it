---
title: Eseguire il rehosting di un'app Contoso con la migrazione alle VM di Azure mediante Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il rehosting di un'app locale con una migrazione lift-and-shift ad Azure di macchine locali mediante il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60668407"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migrazione di Contoso: Rehosting di un'app locale in macchine virtuali di Azure


Questo articolo spiega in che modo Contoso esegue il rehosting dell'app SmartHotel360 locale in Azure, migrando le VM dell'app alle VM di Azure.


Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire l'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant. | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in un'istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile
Articolo 5: Effettuare il rehosting di un'app in macchine virtuali di Azure | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 nelle macchine virtuali di Azure usando il servizio Site Recovery. | Questo articolo
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app SmartHotel360. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app nei contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione dell'app SmartHotel ad Azure. e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui il servizio app di Azure, servizio Azure Kubernetes, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile


In questo articolo Contoso esegue la migrazione dell'app a due livelli Windows. App NET SmartHotel360 in esecuzione in VM di VMware ad Azure. Questa app è disponibile per l'uso in modalità open source ed è possibile scaricarla da [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Stare al passo con la crescita del business**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali sono sotto pressione.
- **Limitare i rischi**: l'app SmartHotel360 è fondamentale per il business di Contoso. L'obiettivo è spostare l'app in Azure senza correre alcun rischio.
- **Estendere**: Contoso non vuole modificare l'app, ma vuole assicurarsi che sia stabile.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale:

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali riscontrate oggi in VMware. L'app manterrà la stessa importanza critica nel cloud come la corrispondente versione in locale.
- Contoso non intende investire in questa app. L'app è importante per l'azienda, ma Contoso vuole semplicemente eseguirne la migrazione sicura al cloud nella sua forma attuale.
- Contoso non intende modificare il modello operativo dell'app. Punta all'interazione nel cloud esattamente come avviene ora.
- Contoso non intende modificare le funzionalità dell'app. Cambierà solo la posizione.


## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app è suddivisa a livelli tra due VM (**WEBVM** e **SQLVM**).
- Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).

### <a name="proposed-architecture"></a>Architettura proposta

- Poiché l'app costituisce un carico di lavoro di produzione, le macchine virtuali dell'app in Azure risiederanno nel gruppo di risorse di produzione ContosoRG.
- Le macchine virtuali dell'app verranno migrate nell'area primaria di Azure (Stati Uniti orientali 2) e collegate alla rete di produzione (VNET-PROD-EUS2).
- La macchina virtuale del front-end Web (WEBVM) risiederà nella subnet del front-end (PROD-FE-EUS2) nella rete di produzione.
- La macchina virtuale del database risiederà nella subnet del database (PROD-DB-EUS2) nella rete di produzione.
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Considerazioni sul database

Come parte del processo di progettazione della soluzione, Contoso ha eseguito un confronto delle funzionalità tra Database SQL di Azure e SQL Server. Le considerazioni seguenti hanno portato alla decisione di usare SQL Server in esecuzione su una macchina virtuale IaaS di Azure:

- L'uso di una macchina virtuale di Azure con SQL Server sembra essere una soluzione ottimale se Contoso ha l'esigenza di personalizzare il sistema operativo o il server di database oppure se vuole posizionare ed eseguire app di terze parti nella stessa macchina virtuale.
- Con Software Assurance, Contoso può scambiare in futuro le licenze esistenti con tariffe scontate per un'istanza gestita di database SQL usando l'offerta Vantaggio Azure Hybrid per SQL Server. In questo modo può risparmiare fino al 30% sull'istanza gestita.



### <a name="solution-review"></a>Revisione della soluzione

Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | Entrambe le macchine virtuali dell'app verranno spostate in Azure senza modifiche, semplificando così la migrazione.<br/><br/> Poiché Contoso usa la modalità lift-and-shift per entrambe le macchine virtuali dell'app, non sono necessari particolari strumenti di configurazione o migrazione per il database dell'app.<br/><br/> Contoso può sfruttare l'investimento in Software Assurance usando l'offerta Vantaggio Azure Hybrid.<br/><br/> Contoso manterrà il controllo completo delle macchine virtuali dell'app in Azure.
**Svantaggi** | WEBVM e SQLVM eseguono Windows Server 2008 R2. Il sistema operativo è supportato da Azure per ruoli specifici (luglio 2018). [Altre informazioni](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)<br/><br/> I livelli Web e dati dell'app rimarranno come singolo punto di failover.</br><br/> SQLVM è in esecuzione in SQL Server 2008 R2, che non è incluso nel supporto Mainstream, anche se è supportato per le macchine virtuali di Azure (luglio 2018). [Altre informazioni](https://support.microsoft.com/en-us/help/956893)<br/><br/> Contoso dovrà continuare a supportare l'app come macchine virtuali di Azure anziché passare a un servizio gestito come Servizio app di Azure e Database SQL di Azure.



### <a name="migration-process"></a>Processo di migrazione

Contoso eseguirà la migrazione delle macchine virtuali del front-end e del database dell'app con Site Recovery:

- In primo luogo, Contoso prepara e configura i componenti di Azure per Site Recovery e predispone l'infrastruttura VMware locale.
- Poiché l'[infrastruttura di Azure](contoso-migration-infrastructure.md) è già configurata, deve solo aggiungere un paio di componenti di Azure in modo specifico per Site Recovery.
- Al termine delle attività di preparazione, Contoso può iniziare a replicare le macchine virtuali.
- Non appena la replica è abilitata e operativa, Contoso esegue la migrazione della macchina eseguendone il failover in Azure.

![Processo di migrazione](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure. In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.


## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.<br/><br/> Altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Server locali** | I server vCenter locali devono eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Gli host ESXi devono eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Nell'host ESXi devono essere in esecuzione una o più VM VMware.
**VM locali** | Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo gli amministratori di Contoso eseguiranno la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure per Site Recovery**: viene creato un account di archiviazione di Azure per i dati replicati e un insieme di credenziali di Servizi di ripristino.
> * **Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery**: vengono preparati gli account per l'individuazione delle macchine virtuali e l'installazione dell'agente, in preparazione alla connessione alle macchine virtuali di Azure dopo il failover.
> * **Passaggio 3: Replicare le macchine virtuali**: viene configurata e avviata la replica delle VM in Archiviazione di Azure.
> * **Passaggio 4: Eseguire la migrazione delle macchine virtuali con Site Recovery**: vengono effettuati un failover di test per verificare che tutto funzioni come previsto e un failover completo per eseguire la migrazione delle macchine virtuali ad Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Passaggio 1: Preparare Azure per il servizio Site Recovery

Ecco i componenti di Azure necessari a Contoso per la migrazione delle VM ad Azure:

- Una rete virtuale in cui verranno collocate le VM di Azure al momento della creazione, durante il failover.
- Un account di archiviazione di Azure per i dati replicati.
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Per configurare questi elementi, Contoso segue questa procedura:

1. Configurare una rete. Contoso ha già configurato una rete che può usare per Site Recovery quando ha [distribuito l'infrastruttura di Azure](contoso-migration-infrastructure.md)

    - SmartHotel360 è un'app di produzione e le VM verranno migrate alla rete di produzione di Azure (VNET-PROD-EUS2) nell'area primaria degli Stati Uniti orientali 2.
    - Entrambe le VM verranno inserite nel gruppo di risorse ContosoRG, usato per le risorse di produzione.
    - La VM front-end dell'app (WEBVM) verrà migrata alla subnet front-end (PROD-FE-EUS2) nella rete di produzione.
    - La VM del database dell'app (SQLVM) verrà migrata alla subnet del database (PROD-DB-EUS2) nella rete di produzione.

2. Configurare un account di archiviazione. Contoso crea un account di archiviazione di Azure (contosovmsacc20180528) nell'area primaria.
   - L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.
   - Usa un account generico con archiviazione standard e la replica dell'archiviazione con ridondanza locale.

     ![Archiviazione di Site Recovery](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Creare un insieme di credenziali. Una volta configurati rete e account di archiviazione, Contoso crea un insieme di credenziali di Servizi di ripristino (ContosoMigrationVault) e lo inserisce nel gruppo di risorse ContosoFailoverRG nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](./media/contoso-migration-rehost-vm/asr-vault.png)

**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 2: Preparare l'ambiente VMware locale per Site Recovery

Ecco gli elementi definiti da Contoso per preparare l'ambiente locale:

- Un account nel server vCenter o nell'host ESXi vSphere per l'individuazione automatica delle VM.
- Un account per l'installazione automatica del servizio Mobility nelle VM VMware.
- Impostazioni delle VM locali, in modo che Contoso possa connettersi alle VM replicate di Azure dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali.
- Orchestrare la replica, il failover e il failback per le VM.
- È necessario almeno un account di sola lettura. L'account deve essere in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di VM.

Per configurare l'account, gli amministratori di Contoso seguono questa procedura:

1. Crea un ruolo a livello di vCenter.
2. Assegna al ruolo le autorizzazioni necessarie.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni VM.

- Site Recovery è in grado di eseguire un'installazione push automatica del servizio Mobility quando è abilitata la replica della VM.
- È necessario un account, in modo che Site Recovery possa accedere alle VM per l'installazione push. Questo account viene specificato quando si configura la replica.
- L'account può essere di dominio o locale, con le autorizzazioni per l'installazione nelle VM.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover, Contoso intende connettersi alle VM di Azure. A tale scopo, gli amministratori di Contoso eseguono le operazioni seguenti prima della migrazione:

1. Per l'accesso tramite Internet:

   - Abilitano RDP nella VM locale prima del failover.
   - Verifica che per il profilo **Pubblico** siano aggiunte le regole TCP e UDP.
   - Verifica che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.

2. Per l'accesso tramite VPN da sito a sito:

   - Abilita RDP nella macchina virtuale locale.
   - Consente RDP in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
   - Imposta i criteri SAN del sistema operativo nella VM locale su **OnlineAll**.

Inoltre, quando esegue un failover, deve verificare quanto segue:

- Quando si attiva un failover, nella VM non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla VM finché questi non sono completati.
- Dopo il failover, può selezionare **Diagnostica di avvio** per visualizzare uno screenshot della VM. Se l'operazione non funziona, verifica che la VM sia in esecuzione e rivede questi [suggerimenti per la risoluzione dei problemi](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Passaggio 3: Replicare le macchine virtuali locali

Prima di poter eseguire una migrazione ad Azure, gli amministratori di Contoso devono configurare e abilitare la replica.

### <a name="set-a-replication-goal"></a>Impostare un obiettivo di replica

1. Nell'insieme di credenziali, sotto il nome visualizzato (ContosoVMVault), Contoso seleziona un obiettivo di replica (**Introduzione** > **Site Recovery** > **Preparare l'infrastruttura**).
2. Specifica che le macchine virtuali si trovano in locale, vengono eseguite su VMware e replicano i dati in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, conferma di aver completato la pianificazione della distribuzione selezionando **Operazione completata**. In questo scenario Contoso migra solo due VM e non ha bisogno di pianificare la distribuzione.


### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Gli amministratori di Contoso devono configurare l'ambiente di origine. A tale scopo, scarica un modello OVF e lo usa per distribuire il server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.



Gli amministratori di Contoso eseguono questi passaggi come indicato di seguito:

1. Nell'insieme di credenziali scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la VM.

    ![Modello OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. Al primo avvio della VM, viene visualizzata un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento specifica un nome per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, esegue l'accesso alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si registra il server di configurazione.

    ![Registrare il server di configurazione](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
9. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
10. Seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.
        ![insieme di credenziali](./media/contoso-migration-rehost-vm/cswiz1.png)

10. Scarica e installa il server MySQL e VMware PowerCLI.
11. Dopo la convalida, specifica l'indirizzo IP o l'FQDN dell'host vSphere o del server vCenter. Mantiene la porta predefinita e specifica un nome descrittivo per il server in Azure.
12. Specifica l'account creato per l'individuazione automatica e le credenziali usate per installare automaticamente il servizio Mobility. Per le macchine Windows, l'account deve avere privilegi di amministratore locale sulle VM.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Al termine della registrazione, nel portale di Azure gli amministratori verificano che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti.
8. Site Recovery si connette quindi ai server VMware usando le impostazioni specificate e individua le VM.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora gli amministratori di Contoso specificano le impostazioni della replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** vengono selezionate le impostazioni di destinazione.
2. Site Recovery verifica che nel percorso di destinazione specificato siano disponibili un account di archiviazione di Azure e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Ora gli amministratori di Contoso possono creare i criteri di replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** vengono creati i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**: Il valore predefinito è 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.

        ![Creare criteri di replica](./media/contoso-migration-rehost-vm/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione.

    ![Associare i criteri di replica](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Abilitare la replica per WEBVM

A questo punto gli amministratori di Contoso possono abilitare la replica per le VM. Inizia con WebVM.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Indica che intende abilitare le VM, seleziona il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Seleziona le impostazioni di destinazione, inclusi il gruppo di risorse e la rete di Azure, e l'account di archiviazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Gli amministratori selezionano **WebVM** per la replica, controllano i criteri di replica e abilitano la replica.

   - In questa fase selezionano solo WEBVM perché devono essere selezionate la rete virtuale e la subnet e le VM dell'app verranno posizionate in subnet diverse.
   - Quando viene abilitata la replica, Site Recovery installa automaticamente il servizio Mobility nella VM.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
6. In **Informazioni di base** nel portale di Azure gli amministratori possono visualizzare la struttura per le VM con replica in Azure.


### <a name="enable-replication-for-sqlvm"></a>Abilitare la replica per SQLVM

Ora gli amministratori di Contoso possono avviare la replica della macchina SQLVM seguendo lo stesso processo di prima.

1. Seleziona le impostazioni di origine.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Specifica le impostazioni di destinazione.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Seleziona SQLVM per la replica.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Applica gli stessi criteri di replica usati per WEBVM e abilita la replica.

    ![Visualizzazione dell'infrastruttura](./media/contoso-migration-rehost-vm/essentials.png)

**Ulteriore assistenza?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Altre informazioni per [abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Passaggio 4: Eseguire la migrazione delle macchine virtuali

Gli amministratori di Contoso eseguono un rapido failover di test e poi un failover completo per la migrazione delle VM.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

Un failover di test consente di verificare che tutti gli elementi funzionino come previsto.

1. Gli amministratori effettuano un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo.
3. Viene eseguito il failover di test:

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Gli amministratori verificano che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
4. Dopo aver verificato il failover di test, lo elimina e registra e salva eventuali osservazioni.

### <a name="create-and-customize-a-recovery-plan"></a>Creare e personalizzare un piano di ripristino

 Dopo aver verificato che il failover di test funzioni come previsto, gli amministratori di Contoso creano un piano di ripristino per la migrazione.

- Un piano di ripristino specifica l'ordine in cui si verifica il failover e indica in che modo le VM di Azure verranno attivate in Azure.
- Poiché l'app è a due livelli, l'azienda personalizza il piano di ripristino in modo che la VM dei dati (SQLVM) venga avviata prima della VM front-end (WEBVM).

1. In **Piani di ripristino (Site Recovery)** > **+Piano di ripristino**, crea un piano e vi aggiunge le macchine virtuali.

    ![Piano di ripristino](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Dopo aver creato il piano, lo personalizza (**Piani di ripristino** > **SmartHotelMigrationPlan** > **Personalizza**).
2.  WEBVM viene rimossa da **Gruppo 1: avvio**. In questo modo la prima azione di avvio avrà effetto solo su SQLVM.
3.  In **+Gruppo** > **Aggiungi elementi protetti** WEBVM viene aggiunta a Gruppo 2: avvio. Le VM devono essere in due gruppi diversi.


### <a name="migrate-the-vms"></a>Eseguire la migrazione delle macchine virtuali


Gli amministratori di Contoso possono ora eseguire un failover completo per completare la migrazione.

1. Seleziona il piano di ripristino > **Failover**.
2. Sceglie di effettuare il failover al punto di ripristino più recente e tenta di arrestare la VM locale tramite Site Recovery prima che venga attivato il failover. Contoso segue l'avanzamento del failover nella pagina **Processi**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Dopo il failover, verifica che la VM di Azure venga visualizzata come previsto nel portale di Azure.

    ![Failover](./media/contoso-migration-rehost-vm/failover2.png)

3. Dopo la verifica, completa la migrazione per ogni VM. Arresta la replica per la macchina virtuale e la relativa fatturazione di Site Recovery.

    ![Failover](./media/contoso-migration-rehost-vm/failover3.png)

**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come eseguire un failover di test.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come eseguire il failover in Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della migrazione i livelli dell'app SmartHotel360 saranno in esecuzione in VM di Azure.

Ora Contoso deve eseguire le operazioni di pulizia seguenti:

- Rimuovere la macchina WEBVM dall'inventario di vCenter.
- Rimuovere il computer SQLVM dall'inventario di vCenter.
- Rimuovere WEBVM e SQLVM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare la nuova posizione e gli indirizzi IP per le VM.
- Esaminare le risorse che interagiscono con le VM e aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Ora che l'app è in esecuzione, Contoso deve operazionalizzarla e proteggerla completamente in Azure.

### <a name="security"></a>Security

Il team di sicurezza Contoso esamina le VM di Azure per determinare eventuali problemi di sicurezza.

- Per controllare l'accesso, il team esamina i gruppi di sicurezza di rete (NSG) per le VM. I gruppi di sicurezza di rete fanno in modo che l'app possa essere raggiunta solo dal traffico consentito.
- Il team valuta inoltre l'opportunità di proteggere i dati sul disco usando Crittografia dischi di Azure e KeyVault.

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sulle procedure di sicurezza per le macchine virtuali.

## <a name="bcdr"></a>BCDR

Per la continuità aziendale e il ripristino di emergenza (BCDR), Contoso esegue le azioni seguenti:

- Proteggere i dati: Contoso esegue il backup dei dati nelle macchine virtuali usando il servizio Backup di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- Mantenere le app in esecuzione: Contoso esegue la replica delle macchine virtuali dell'app in Azure in un'area secondaria usando Site Recovery. [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)



### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

1. Contoso include licenze esistenti per le VM e sfrutta il Vantaggio Azure Hybrid. Convertirà le VM di Azure esistenti per usufruire di questi prezzi.
2. Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud. [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il rehosting dell'app SmartHotel360 in Azure migrando le VM dell'app alle VM di Azure mediante il servizio Site Recovery.


## <a name="next-steps"></a>Passaggi successivi

Nel [prossimo articolo](contoso-migration-rehost-vm-sql-ag.md) della serie verrà spiegato in che modo Contoso esegue il rehosting della VM front-end dell'app SmartHotel360 in una VM di Azure e migra il database a un gruppo di disponibilità AlwaysOn di SQL Server in Azure.

