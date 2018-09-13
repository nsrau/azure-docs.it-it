---
title: Eseguire il rehosting di un'app Contoso tramite la migrazione a VM di Azure e al gruppo di disponibilità AlwaysOn di SQL Server | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app locale tramite la migrazione a VM di Azure e al gruppo di disponibilità AlwaysOn di SQL Server
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: f744b9bacfb43b5cf4ba81e19d8e543561bcec61
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842754"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Migrazione Contoso: eseguire il rehosting di un'app locale in VM di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server

Questo articolo spiega in che modo Contoso esegue il rehosting dell'app SmartHotel360 in Azure. tramite la migrazione della VM front-end dell'app a una VM di Azure e del database dell'app a una VM di SQL Server in Azure, in esecuzione in un cluster di failover Windows Server con gruppi di disponibilità AlwaysOn di SQL Server.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire l'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant. | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile   
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 nelle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server | Contoso esegue la migrazione dell'app SmartHotel360. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Questo articolo
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery | Disponibile
[Articolo 8: Rehosting di un'app Linux in VM di Azure e Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant | Disponibile
[Articolo 10: Refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile 
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server locale a Visual Studio Team Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 ad Azure e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel360 usando una gamma di funzionalità e servizi di Azure, tra cui Servizio app di Azure, il servizio Kubernetes di Azure (AKS), Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile


In questo articolo Contoso esegue la migrazione in Azure dell'app a due livelli SmartHotel360 di Windows .NET in esecuzione nelle macchine virtuali VMware. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team dedicato al cloud di Contoso ha definito alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale:

- Dopo la migrazione, l'app in Azure dovrà offrire lo stesso livello di prestazioni attualmente disponibile in VMware.  L'app manterrà la stessa importanza critica nel cloud come la rispettiva versione in locale.
- Contoso non intende investire in questa app.  L'app è importante per l'azienda, ma Contoso vuole semplicemente eseguirne la migrazione sicura al cloud nella sua forma attuale.
- Il database locale per l'app ha presentato problemi di disponibilità. Contoso vuole quindi eseguire la distribuzione in Azure come cluster a disponibilità elevata, con funzionalità di failover.
- Contoso intende eseguire l'aggiornamento dalla piattaforma SQL Server 2008 R2 corrente a SQL Server 2017.
- Contoso non vuole usare un database SQL di Azure per questa app e sta cercando possibili alternative.


## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-architecture"></a>Architettura corrente

- L'app è suddivisa a livelli tra due VM (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).


### <a name="proposed-architecture"></a>Architettura proposta

In questo scenario:

- Contoso migrerà l'applicazione front-end WEBVM in una macchina virtuale IaaS di Azure.
    - La VM front-end di Azure verrà distribuita nel gruppo di risorse ContosoRG, usato per le risorse di produzione.
    -  Sarà ubicata nella rete di produzione Azure (VNET-PROD-EUS2) nell’area primaria degli Stati Uniti orientali 2.
- Il database di app verrà migrato a una VM di SQL Server di Azure.
    - Sarà ubicato nella rete di database Azure di Contoso (PROD-DB-EUS2) nell’area primaria degli Stati Uniti orientali 2.
    - Sarà posizionato in un cluster di failover di Windows Server a due nodi, che utilizza gruppi di disponibilità SQL Server Always On.
    - In Azure i due nodi VM di SQL Server nel cluster verranno distribuiti nel gruppo di risorse ContosoRG.
    - Il nodo VM sarà ubicato nella rete di produzione Azure (VNET-PROD-EUS2) nell’area primaria degli Stati Uniti orientali 2.
    - Le VM eseguiranno Windows Server 2016 con SQL Server 2017 Enterprise Edition. Contoso non dispone di licenze per questo sistema operativo e quindi userà un'immagine disponibile in Azure Marketplace che fornisce la licenza a un costo definito in base al Contratto Enterprise di Azure.
    - Fatta eccezione per i nomi univoci, le due VM usano le stesse impostazioni.
- Contoso installerà un sistema di bilanciamento del carico interno che intercetta il traffico sul cluster e lo indirizza al nodo di cluster appropriato.
    - Il servizio di bilanciamento del carico interno verrà distribuito nel gruppo di risorse ContosoNetworkingRG, usato per le risorse di rete.
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>Considerazioni sul database

Come parte del processo di progettazione della soluzione, Contoso ha eseguito un confronto delle funzionalità tra Database SQL di Azure e SQL Server. Le considerazioni seguenti hanno portato alla decisione di utilizzare un server SQL con in esecuzione una macchina virtuale Iaas di Azure:

 - L'uso di una macchina virtuale di Azure con SQL Server sembra essere una soluzione ottimale se Contoso ha l'esigenza di personalizzare il sistema operativo o il server di database oppure se vuole posizionare ed eseguire app di terze parti nella stessa macchina virtuale.
 - Utilizzando il Data Migration Assistant, Contoso può facilmente valutare e migrare in un database SQL di Azure.
 

### <a name="solution-review"></a>Revisione della soluzione

Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | WEBVM verrà spostata in Azure senza modifiche, semplificando così la migrazione.<br/><br/> Il livello SQL Server verrà eseguito su SQL Server 2017 e Windows Server 2016. In questo modo viene ritirato il sistema operativo Windows Server 2008 R2 e l'esecuzione di SQL Server 2017 supporta i requisiti tecnici e gli obiettivi di Contoso. L’IT garantisce compatibilità al 100% per l’abbandono di SQL Server 2008 R2.<br/><br/> Contoso può sfruttare l'investimento in Software Assurance usando l'offerta Vantaggio Azure Hybrid.<br/><br/> Un'implementazione SQL Server ad alta disponibilità in Azure fornisce tolleranza agli errori in modo che il livello dei dati delle app non sia più un singolo punto di failover.
**Svantaggi** | WEBVM esegue Windows Server 2008 R2. Il sistema operativo è supportato da Azure per ruoli specifici (luglio 2018). [Altre informazioni](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> I livelli Web dell'app rimarranno come singolo punto di failover.</br><br/> Contoso dovrà continuare a supportare il livello Web come VM di Azure anziché passare a un servizio gestito come Servizio app di Azure.<br/><br/> Con la soluzione scelta, Contoso dovrà continuare a gestire due macchine virtuali SQL Server piuttosto che passare a una piattaforma gestita come Istanza gestita di SQL di Azure. Inoltre, con Software Assurance, Contoso ha potuto sostituire le licenze esistenti con licenze a tariffe scontate su Istanza gestita di SQL di Azure.


### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA viene eseguito dalla macchina SQL Server locale e migra il database su una VPN da sito a sito ad Azure. | DMA è uno strumento gratuito e scaricabile.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le VM di Azure e per le VM e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.

 

## <a name="migration-process"></a>Processo di migrazione

Gli amministratori Contoso eseguiranno la migrazione delle VM dell'app ad Azure.

- La migrazione della VM front-end alla VM di Azure verrà eseguita tramite Site Recovery:
    - In primo luogo, Contoso preparerà e configurerà i componenti di Azure e predisporrà l'infrastruttura VMware locale.
    - A questo punto potrà iniziare a replicare la VM.
    - Non appena la replica sarà abilitata e operativa, eseguirà la migrazione della VM eseguendone il failover in Azure.
- La migrazione del database a un cluster di SQL Server in Azure verrà eseguita tramite il Data Migration Assistant (DMA).
    - Prima di tutto, Contoso dovrà effettuare il provisioning delle VM di SQL Server in Azure, configurare il cluster e un servizio di bilanciamento del carico interno e configurare gruppi di disponibilità AlwaysOn.
    - A questo punto potrà eseguire la migrazione del database.
- Al termine della migrazione, Contoso abiliterà la protezione AlwaysOn per il database.

![Processo di migrazione](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha già creato una sottoscrizione in un articolo precedente di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.<br/><br/> Altre informazioni sui requisiti specifici di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) per Site Recovery.
**Site Recovery (locale)** | Il server vCenter locale deve eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configurazione supportata di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage).<br/><br/> Le VM di cui si vuole eseguire la replica devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Preparare un cluster**: creare un cluster per la distribuzione di due nodi VM di SQL Server in Azure.
> * **Passaggio 2: Distribuire e configurare il cluster**: preparare un cluster di Server SQL di Azure.  I database vengono migrati in questo cluster creato in precedenza.
> * **Passaggio 3: Distribuire il servizio di bilanciamento del carico**: distribuire un servizio di bilanciamento del carico per bilanciare il traffico verso i nodi di SQL Server.
> * **Passaggio 4: Preparare Azure per Site Recovery**. creare un account di archiviazione di Azure per contenere i dati replicati e un insieme di credenziali dei Servizi di ripristino. 
> * **Passaggio 5: Preparare VMware in locale per Site Recovery**. preparare gli account per l'individuazione delle macchine virtuali e l'installazione dell'agente. Prepara le macchine virtuali locali in modo che gli utenti possano connettersi alle macchine virtuali di Azure dopo la migrazione.
> * **Passaggio 6: Replicare le VM**: abilitare la replica della VM su Azure.
> * **Passaggio 7: Installazione di DMA**: scaricare e installare il Database Migration Assistant.
> * **Passaggio 7: Eseguire la migrazione del database con il Servizio Migrazione del database**: eseguire la migrazione del database in Azure.
> * **Passaggio 9: Proteggere il database**: creare un gruppo di disponibilità AlwaysOn per il cluster.
> * **Passaggio 10: Migrare l'applicazione Web VM**: eseguire quindi un failover di test per verificare che tutto funzioni come previsto. Eseguire quindi un failover completo in Azure. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Passaggio 1: Configurare un cluster con gruppi di disponibilità AlwaysOn di SQL Server

Per configurare il cluster, gli amministratori di Contoso seguono questa procedura:

1. Crea due VM di SQL Server selezionando l'immagine di SQL Server 2017 Enterprise Windows Server 2016 in Azure Marketplace. 

    ![SKU della VM SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. In **Creazione guidata macchina virtuale** > **Impostazioni di base** configura le impostazioni seguenti:

    - Definisce i nomi per le VM: **SQLAOG1** e **SQLAOG2**.
    - Poiché le macchine virtuali sono business critical, abilita le unità SSD per il tipo di disco delle VM.
    - Specifica le credenziali delle macchine virtuali.
    - Distribuisce le VM nell'area primaria Stati Uniti orientali 2, nel gruppo di risorse ContosoRG.

3. In **Dimensioni** sceglie di iniziare con SKU D2s_V3 per entrambe le VM. Potrà ridimensionarle in un secondo momento, in base alle esigenze.
4. In **Impostazioni** esegue queste operazioni:

    - Poiché queste VM sono database critici per l'app, Contoso usa dischi gestiti.
    - Posiziona le macchine nella rete di produzione nell'area primaria Stati Uniti orientali 2 (**VNET-PROD-EUS2**), nella subnet di database (**PROD-DB-EUS2**).
    - Crea un nuovo set di disponibilità, **SQLAOGAVSET**, con due domini di errore e cinque domini di aggiornamento.

    ![VM SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. In **Impostazioni di SQL Server** limita la connettività SQL alla rete virtuale (privata) sulla porta predefinita 1433. Per l'autenticazione specifica le stesse credenziali usate in locale (**contosoadmin**).

    ![VM SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Servono altre informazioni?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) per il provisioning di una VM di SQL Server.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) sulla configurazione di VM per SKU di SQL Server differenti.

## <a name="step-2-deploy-and-set-up-the-cluster"></a>Passaggio 2: Distribuire e configurare il cluster

Ecco in che modo gli amministratori Contoso configurano il cluster:

1. Configura un account di archiviazione di Azure come cloud di controllo.
2. Aggiunge le VM di SQL Server al dominio di Active Directory nel data center Contoso locale.
3. Crea il cluster in Azure.
4. Configura il cloud di controllo.
5. Abilita infine i gruppi di disponibilità AlwaysOn di SQL.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Configurare un account di archiviazione come cloud di controllo

Per configurare un cloud di controllo, Contoso necessita di un account di archiviazione di Azure che conterrà il file di BLOB usato per l'arbitraggio del cluster. Lo stesso account di archiviazione può essere usato per configurare il cloud di controllo per più cluster. 

Gli amministratori di Contoso creano un account di archiviazione e come indicato di seguito:

1. Specifica un nome riconoscibile per l'account (**contosocloudwitness**).
2. Distribuisce un account generico, con archiviazione con ridondanza locale.
3. Posiziona l'account in una terza area: Stati Uniti centro-meridionali. L'account viene posizionato all'esterno delle aree primaria e secondaria in modo che rimanga disponibile in caso di errore in una delle due aree.
4. Posiziona l'account all'interno del gruppo che include le risorse dell'infrastruttura, **ContosoInfraRG**.

    ![Cloud di controllo](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Quando viene creato l'account di archiviazione, per tale account vengono generate le chiavi di accesso primaria e secondaria. Per creare il cloud di controllo è necessaria la chiave di accesso primaria. La chiave viene visualizzata sotto il nome dell'account di archiviazione > **Chiavi di accesso**.

    ![Chiave di accesso](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Aggiungere le VM di SQL Server al dominio Contoso

1. Contoso aggiunge SQLAOG1 e SQLAOG2 al dominio contoso.com.
2. Installa quindi in ogni VM gli strumenti e le funzionalità per cluster di failover di Windows.

### <a name="set-up-the-cluster"></a>Configurazione del cluster

Prima di configurare il cluster, gli amministratori Contoso creano uno snapshot del disco del sistema operativo in ogni computer.

![snapshot](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Esegue quindi uno script definito per creare il cluster di failover di Windows.

    ![Creare cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Dopo aver creato il cluster, verifica che le VM vengano visualizzate come nodi del cluster.

     ![Creare cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Configurare il cloud di controllo

1. Gli amministratori Contoso configurano il cloud di controllo mediante la procedura guidata **Configurazione quorum** in Gestione cluster di failover.
2. Nella procedura guidata sceglie di creare un cloud di controllo con l'account di archiviazione.
3. Una volta configurato, il cloud di controllo viene visualizzato nello snap-in Gestione cluster di failover.

    ![Cloud di controllo](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>Abilitare i gruppi di disponibilità AlwaysOn di SQL Server

Gli amministratori Contoso possono ora abilitare AlwaysOn:

1. In Gestione configurazione SQL Server abilita **Gruppi di disponibilità AlwaysOn** per il servizio **SQL Server (MSSQLSERVER)**.

    ![Abilitare AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Riavvia quindi il servizio per applicare le modifiche.

Con AlwaysOn abilitato, Contoso può configurare il gruppo di disponibilità AlwaysOn che proteggerà il database di SmartHotel360.

**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) sul cloud di controllo e sulla configurazione dell'account di archiviazione correlato.
- Sono disponibili [istruzioni](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) per configurare un cluster e creare un gruppo di disponibilità.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Passaggio 3: Distribuire Azure Load Balancer

Gli amministratori Contoso vogliono ora distribuire un servizio di bilanciamento del carico interno posizionato davanti ai nodi del cluster. Il servizio di bilanciamento del carico rimane in ascolto del traffico e lo indirizza al nodo appropriato.

![Bilanciamento del carico.](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Per creare il servizio di bilanciamento del carico, Contoso segue questa procedura:

1. Nel portale di Azure > **Rete** > **Servizi di bilanciamento del carico** configura un nuovo servizio di bilanciamento del carico interno: **ILB-PROD-DB-EUS2-SQLAOG**.
2. Posiziona il servizio nella rete di produzione **VNET-PROD-EUS2**, nella subnet del database **PROD-DB-EUS2**.
3. Assegna a tale servizio l'indirizzo IP statico 10.245.40.100.
4. Come elemento di rete, distribuisce il servizio di bilanciamento del carico nel gruppo di risorse di rete **ContosoNetworkingRG**.

    ![Bilanciamento del carico.](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Una volta distribuito, il servizio di bilanciamento del carico interno deve essere configurato. Contoso crea quindi un pool di indirizzi back-end, imposta un probe di integrità e configura una regola di bilanciamento del carico.

### <a name="add-a-backend-pool"></a>Aggiungere un pool back-end

Per distribuire il traffico alle VM nel cluster, gli amministratori Contoso configurano un pool back-end che include gli indirizzi IP delle schede NIC per le VM che riceveranno il traffico di rete dal servizio di bilanciamento del carico.

1. Nelle impostazioni del servizio di bilanciamento del carico nel portale Contoso aggiunge un pool back-end: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Associa il pool al set di disponibilità SQLAOGAVSET. Le VM nel set (**SQLAOG1** e **SQLAOG2**) vengono aggiunte al pool.

    ![Pool back-end](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Gli amministratori Contoso creano un probe di integrità per consentire al servizio di bilanciamento del carico di monitorare lo stato di integrità dell'app. Il probe aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità.

Per creare il probe, Contoso segue questa procedura: 

1. Nelle impostazioni del servizio di bilanciamento del carico nel portale crea un probe di integrità: **SQLAlwaysOnEndPointProbe**.
2. Imposta il probe in modo da monitorare le VM sulla porta TCP 59999.
3. Imposta un intervallo di 5 secondi tra i probe e un valore soglia pari a 2. Se due probe restituiscono un errore, lo stato della VM verrà considerato non integro.

    ![Probe](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Configurare il servizio di bilanciamento del carico per ricevere il traffico


Gli amministratori Contoso devono ora creare una regola di bilanciamento del carico per definire il modo in cui il traffico viene distribuito alle VM.

- L'indirizzo IP front-end gestisce il traffico in ingresso.
- Il pool di indirizzi IP back-end riceve il traffico.

Per creare la regola, Contoso segue questa procedura:

1. Nelle impostazioni del servizio di bilanciamento del carico nel portale aggiunge una nuova regola di bilanciamento del carico: **SQLAlwaysOnEndPointListener**.
2. Viene impostato un listener front-end per ricevere il traffico dei client SQL in ingresso sulla porta TCP 1433.
3. Specifica il pool back-end a cui il traffico verrà indirizzato e la porta su cui le VM sono ascolto del traffico.
4. Abilitano un IP mobile (Direct Server Return). Questo è sempre necessario per SQL AlwaysOn.

    ![Probe](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Ulteriore assistenza?**

- È disponibile una [panoramica](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) di Azure Load Balancer.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) sulla creazione di un servizio di bilanciamento del carico.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Passaggio 4: Preparare Azure per il servizio Site Recovery

Ecco i componenti di Azure necessari a Contoso per distribuire Site Recovery:

- Una rete virtuale in cui verranno posizionate le VM di Azure al momento della creazione durante il failover.
- Un account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Per configurare questi elementi, gli amministratori Contoso seguono questa procedura:

1.  Contoso ha già creato una rete o una subnet che può usare per Site Recovery quando ha [distribuito l'infrastruttura di Azure](contoso-migration-rehost-vm-sql-ag.md).

    - SmartHotel360 è un'app di produzione e WEBVM verrà migrata alla rete di produzione di Azure (VNET-PROD-EUS2) nell'area primaria Stati Uniti orientali 2.
    - WEBVM verrà posizionata nel gruppo di risorse ContosoRG, usato per le risorse di produzione, e nella subnet di produzione (PROD-FE-EUS2).

2. Gli amministratori di Contoso creano un account di archiviazione di Azure (contosovmsacc20180528) nell'area primaria.

    - Usa un account generico con archiviazione standard e la replica dell'archiviazione con ridondanza locale.
    - L'account deve trovarsi nella stessa area dell'insieme di credenziali.

    ![Archiviazione di Site Recovery](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Dopo aver configurato la rete e l'account di archiviazione, Contoso crea un insieme di credenziali di Servizi di ripristino (**ContosoMigrationVault**) e lo posiziona nel gruppo di risorse **ContosoFailoverRG** nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 5: Preparare l'ambiente VMware locale per Site Recovery

Ecco gli elementi definiti dagli amministratori Contoso per preparare l'ambiente locale:

- Un account nel server vCenter o nell'host vSphere ESXi per l'individuazione automatica delle VM.
- Un account che consente l'installazione automatica del servizio Mobility nelle macchine virtuali VMware da replicare.
- Le impostazioni delle VM locali, in modo da connettersi alla VM replicata di Azure dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. 
- Controllare la replica, il failover e il failback.
- È necessario almeno un account di sola lettura. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Per configurare l'account, gli amministratori di Contoso seguono questa procedura:

1. Crea un ruolo a livello di vCenter.
2. Assegnano quindi al ruolo le autorizzazioni necessarie.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni VM.

- Site Recovery è in grado di eseguire un'installazione push automatica di questo componente quando è abilitata la replica per la VM.
- Per l'installazione push è necessario un account che possa essere usato da Site Recovery per accedere alla VM. Si specifica questo account quando si configura la replica nella console di Azure.
- L'account può essere di dominio o locale, con le autorizzazioni per l'installazione nella VM.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover, Contoso vuole essere in grado di connettersi alle VM di Azure. A tale scopo, gli amministratori di Contoso eseguono le operazioni seguenti prima della migrazione:

1. Per l'accesso tramite Internet:

 - Abilita RDP nella VM locale prima del failover.
 - Verifica che per il profilo **Pubblico** siano aggiunte le regole TCP e UDP.
 - Verifica che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
 
2. Per l'accesso tramite VPN da sito a sito:

 - Abilita RDP nella macchina virtuale locale.
 - Consente RDP in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
 - Imposta i criteri SAN del sistema operativo nella VM locale su **OnlineAll**.

Inoltre, quando esegue un failover, deve verificare quanto segue:

- Quando si attiva un failover, nella VM non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile agli utenti accedere alla VM finché questi non sono completati.
- Dopo il failover, può selezionare **Diagnostica di avvio** per visualizzare uno screenshot della VM. Se l'operazione non funziona, verifica che la VM sia in esecuzione e rivede questi [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Servono altre informazioni?**

- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.
- [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Passaggio 6: Replicare le VM locali in Azure con Site Recovery

Prima di poter eseguire una migrazione ad Azure, gli amministratori Contoso devono configurare e abilitare la replica.

### <a name="set-a-replication-goal"></a>Impostare un obiettivo di replica

1. Nell'insieme di credenziali, sotto il nome visualizzato (ContosoVMVault), Contoso seleziona un obiettivo di replica (**Introduzione** > **Site Recovery** > **Preparare l'infrastruttura**).
2. Specifica che le macchine virtuali si trovano in locale, vengono eseguite su VMware e replicano i dati in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, è necessario confermare di aver completato la pianificazione della distribuzione selezionando **Operazione completata**. In questo scenario Contoso esegue solo la migrazione di una VM e non ha bisogno di pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Gli amministratori Contoso devono configurare l'ambiente di origine. A tale scopo, scarica un modello OVF e lo usa per distribuire il server di configurazione di Site Recovery come macchina virtuale VMware locale a disponibilità elevata. Dopo aver attivato il server di configurazione, lo registra nell'insieme di credenziali.

Il server di configurazione esegue una serie di componenti:

- Il componente server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.

Gli amministratori di Contoso eseguono questi passaggi come indicato di seguito:


1. Nell'insieme di credenziali scarica il modello OVF da **Preparare l'infrastruttura** > **Origine** > **Server di configurazione**.
    
    ![Scaricare OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la VM.

    ![Modello OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Al primo avvio della VM, viene visualizzata un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento Contoso specifica un nome per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, esegue l'accesso alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.

    ![Registrare il server di configurazione](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
9. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
10. Si seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.
        ![vault](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. A questo punto scarica e installa il server MySQL e VMWare PowerCLI. 
11. Dopo la convalida, specifica l'indirizzo IP o il nome di dominio completo dell'host vSphere o del server vCenter. Mantiene la porta predefinita e specifica un nome descrittivo per il server vCenter.
12. Specifica l'account creato per l'individuazione automatica e le credenziali usate per installare automaticamente il servizio Mobility. Per le macchine Windows, l'account deve avere privilegi di amministratore locale sulle VM.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Al termine della registrazione, nel portale di Azure gli amministratori verificano che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
8. Site Recovery si connette quindi ai server VMware usando le impostazioni specificate e individua le VM.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora gli amministratori di Contoso specificano le impostazioni della replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** vengono selezionate le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione di Azure e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Ora gli amministratori di Contoso possono creare i criteri di replica.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** vengono creati i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
        ![Creare criteri di replica](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Abilitare la replica

Ora gli amministratori Contoso possono iniziare a replicare WebVM.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Indica che intende abilitare le VM, seleziona il server vCenter e il server di configurazione.

    ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. A questo punto specifica le impostazioni di destinazione, inclusi il gruppo di risorse e la rete virtuale, oltre all'account di archiviazione in cui verranno archiviati i dati replicati.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Viene selezionato WebVM per la replica, controlla i criteri di replica e abilita la replica. Quando viene abilitata la replica, Site Recovery installa il servizio Mobility nella macchina virtuale.
 
    ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
5. In **Informazioni di base** nel portale di Azure gli amministratori possono visualizzare la struttura per le VM con replica in Azure.

    ![Visualizzazione dell'infrastruttura](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Ulteriore assistenza?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Altre informazioni per [abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-7-install-the-database-migration-assistant-dma"></a>Passaggio 7: Installare Database Migration Assistant (DMA)

Gli amministratori Contoso eseguiranno la migrazione del database di SmartHotel360 alla macchina virtuale di Azure **SQLAOG1** usando Database Migration Assistant (DMA). Per configurare DMA, Contoso segue questa procedura:

1. Scarica lo strumento dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) nella VM di SQL Server locale (**SQLVM**).
2. Esegue il programma di installazione (DownloadMigrationAssistant.msi) nella VM.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

## <a name="step-8-migrate-the-database-with-dma"></a>Passaggio 8: Eseguire la migrazione del database tramite DMA

1. In DMA esegue una nuova migrazione: **SmartHotel**.
2. In **Tipo del server di destinazione** seleziona **SQL Server in macchine virtuali di Azure**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Nei dettagli della migrazione aggiunge **SQLVM** come server di origine e **SQLAOG1** come destinazione. Specifica quindi le credenziali per ogni macchina virtuale.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Crea una condivisione locale per le informazioni di configurazione e del database. Queste informazioni devono risultare accessibili in scrittura all'account del servizio SQL in SQLVM e SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso seleziona gli account di accesso che devono essere migrati e avvia la migrazione. Al termine dell'operazione, DMA mostra che la migrazione è stata completata.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Contoso verifica che il database sia in esecuzione su **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

Il Servizio Migrazione del database di Azure si connette alla VM di Server SQL locale attraverso una connessione VPN da sito a sito tra il data center di Contoso e Azure e quindi esegue la migrazione del database.

## <a name="step-7-protect-the-database-with-alwayson"></a>Passaggio 7: Proteggere il database con AlwaysOn

Con il database dell'app in esecuzione su **SQLAOG1**, gli amministratori Contoso possono ora proteggere il database usando gruppi di disponibilità AlwaysOn. Configura AlwaysOn tramite SQL Management Studio e quindi assegna un listener con il clustering di Windows. 

### <a name="create-an-alwayson-availability-group"></a>Creare un gruppo di disponibilità AlwaysOn

1. In SQL Management Studio fa clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn** per avviare la **Creazione guidata Gruppo di disponibilità**.
2. In **Specifica opzioni** assegna il nome **SHAOG** al gruppo di disponibilità. In **Seleziona database** seleziona il database SmartHotel360.

    ![Gruppo di disponibilità AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. In **Specifica repliche** aggiunge i due nodi SQL come repliche di disponibilità e li configura in modo da assicurare il failover automatico con commit sincrono.

     ![Gruppo di disponibilità AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Configura un listener per il gruppo (**SHAOG**) e la porta. L'indirizzo IP del servizio di bilanciamento del carico interno viene aggiunto come indirizzo IP statico (10.245.40.100).

    ![Gruppo di disponibilità AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. In **Seleziona sincronizzazione dei dati** abilita il seeding automatico. Con questa opzione, SQL Server crea automaticamente le repliche secondarie per ogni database del gruppo. In questo modo non è necessario eseguire manualmente il backup e il ripristino dei database. Dopo la convalida, viene creato il gruppo di disponibilità.

    ![Gruppo di disponibilità AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso ha riscontrato un problema durante la creazione del gruppo. Non sta usando la sicurezza integrata di Windows Active Directory e pertanto deve concedere autorizzazioni di accesso SQL per creare ruoli del cluster di failover di Windows.

    ![Gruppo di disponibilità AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Una volta creato, il gruppo può essere visualizzato in SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Configurare un listener nel cluster

Come ultimo passaggio nella configurazione della distribuzione SQL, gli amministratori Contoso configurano il servizio di bilanciamento del carico interno come listener nel cluster e porta il listener online. Per eseguire questa operazione usa uno script.

![Listener del cluster](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Verificare la configurazione

Al termine della configurazione, Contoso può ora disporre di un gruppo di disponibilità funzionale in Azure che usa il database migrato. Per verificarlo, gli amministratori si connettono al servizio di bilanciamento del carico interno in SQL Management Studio.

![Connessione al servizio di bilanciamento del carico](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Servono altre informazioni?**
- Sono disponibili informazioni sulla creazione di un [gruppo di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) e di un [listener](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Sono disponibili informazioni su come [configurare manualmente il cluster per l'uso dell'indirizzo IP del servizio di bilanciamento del carico](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- Sono disponibili [altre informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) sulla creazione e l'uso della firma di accesso condiviso.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Passaggio 8: Eseguire la migrazione della VM con Site Recovery

Gli amministratori Contoso effettuano un rapido failover di test e poi eseguono la migrazione della macchina virtuale.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

L'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto prima della migrazione. 

1. Gli amministratori effettuano un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene eseguito il failover di test: 

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Verifica che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo la verifica, elimina il failover, registra e salva eventuali osservazioni. 

### <a name="run-a-failover"></a>Eseguire un failover

1. Dopo aver verificato che il failover di test funzioni come previsto, gli amministratori Contoso creano un piano di ripristino per la migrazione e aggiungono WEBVM al piano.

     ![Piano di ripristino](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Esegue il failover nel piano. Viene selezionato il punto di ripristino più recente e si specifica che Site Recovery deve tentare di arrestare la macchina virtuale locale prima di avviare il failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Dopo il failover, verifica che la VM di Azure venga visualizzata come previsto nel portale di Azure.

    ![Piano di ripristino](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Dopo aver verificato la VM in Azure, completa il processo di migrazione, arresta la replica per la VM e quindi la fatturazione di Site Recovery per la VM.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Aggiornare la stringa di connessione

Come passaggio finale del processo di migrazione, gli amministratori Contoso aggiornano la stringa di connessione dell'applicazione in modo che punti al database in esecuzione sul listener SHAOG dopo la migrazione. Questa configurazione verrà modificata in WEBVM ora in esecuzione in Azure.  Questa configurazione si trova nel file web.config dell'applicazione ASP. 

1. Individuare il file in C:\inetpub\SmartHotelWeb\web.config.  Modificare il nome del server in base al nome di dominio completo del gruppo di disponibilità AlwaysOn: shaog.contoso.com.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Dopo l'aggiornamento e il salvataggio del file, riavvia IIS su WEBVM. Per il riavvio è possibile usare il comando IISRESET /RESTART da un prompt dei comandi.
2. Dopo il riavvio di IIS, l'applicazione usa il database in esecuzione nell'istanza gestita di SQL.


**Servono altre informazioni?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come eseguire un failover di test. 
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come eseguire il failover in Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Dopo la migrazione, l'app SmartHotel360 è in esecuzione su una VM di Azure e il database di SmartHotel360 si trova nel cluster SQL di Azure.

Ora Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere le VM locali dall'inventario vCenter.
- Rimuovere le VM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare le nuove posizioni e i nuovi indirizzi IP per le VM.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in base alla nuova configurazione.
- Aggiungere le due nuove VM (SQLAOG1 e SQLAOG2) ai sistemi di monitoraggio della produzione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Sicurezza

Il team di sicurezza Contoso esamina le VM di Azure WEBVM, SQLAOG1 e SQLAOG2 per determinare eventuali problemi di sicurezza. 

- Il team esamina i gruppi di sicurezza di rete (NSG) per le macchine virtuali per controllare l'accesso. I gruppi di sicurezza di rete vengono usati per assicurarsi che possa passare solo il traffico consentito all'applicazione.
- Il team valuta l'opportunità di proteggere i dati sul disco usando Crittografia dischi di Azure e KeyVault.
- Il team dovrebbe valutare l'uso della tecnologia TDE (Transparent Data Encryption) e quindi abilitarla nel database di SmartHotel360 in esecuzione sul nuovo gruppo di disponibilità AlwaysOn di SQL. [Altre informazioni](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sulle procedure di sicurezza per le macchine virtuali.


## <a name="bcdr"></a>BCDR

 Per la continuità aziendale e il ripristino di emergenza (BCDR), Contoso esegue le azioni seguenti:
 - Mantenere i dati al sicuro: Contoso esegue il backup dei dati nelle macchine virtuali WEBVM, SQLAOG1 e SQLAOG2 usando il servizio Backup di Azure. [Altre informazioni].
(https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Contoso apprenderà anche come usare Archiviazione di Azure per eseguire il backup di SQL Server direttamente in un archivio blob. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore).
- Mantenere le app in esecuzione: Contoso esegue la replica delle macchine virtuali dell'app in Azure in un'area secondaria usando Site Recovery. [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

1. Contoso dispone di licenze esistenti per WEBVM e sfrutterà il Vantaggio Azure Hybrid.  Convertirà le VM di Azure esistenti per usufruire di questi prezzi.
2. Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure. 

## <a name="conclusion"></a>Conclusioni

In questo articolo Contoso ha eseguito il rehosting dell'app SmartHotel360 in Azure tramite la migrazione della VM front-end dell'app ad Azure con il servizio Site Recovery. Il database dell'app è stato migrato da Contoso in un cluster di SQL Server di cui è stato effettuato il provisioning in Azure e protetto in un gruppo di disponibilità AlwaysOn di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo successivo della serie si mostrerà come Contoso esegue il rehosting dell'app osTicket per la gestione di ticket di assistenza che viene eseguita su Linux e distribuita con un database MySQL.


