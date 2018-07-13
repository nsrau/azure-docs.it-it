---
title: Rehosting di un'app di Contoso in Azure eseguendo la migrazione a una macchina virtuale di Azure e all'istanza gestita di SQL di Azure | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app locale in macchine virtuali di Azure e nell'istanza gestita di SQL di Azure
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723613"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migrazione Contoso: esecuzione del rehosting di un'app locale in macchine virtuali di Azure e nell'istanza gestita di SQL di Azure

Questo articolo illustra come Contoso esegue la migrazione della VM front-end dell'app SmartHotel alle macchine virtuali di Azure usando il servizio Azure Site Recovery e la migrazione del database dell'app a un'istanza gestita di SQL di Azure.

> [!NOTE]
> L'istanza gestita di SQL di Azure è attualmente in anteprima.

Questo documento è il quarto di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e una serie di scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.


**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
Articolo 4: Eseguire il rehosting in VM di Azure e in un'istanza gestita di SQL (questo articolo) | Spiega in che modo Contoso migra l'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale front-end dell'app mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il servizio [Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel usando solo il servizio Site Recovery.
[Articolo 6: Eseguire il rehosting in VM di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso migra l'app osTicket di Linux alle VM di Azure mediante Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e a un'istanza del server MySQL di Azure mediante MySQL Workbench. | Disponibile

Questa app SmartHotel di esempio è disponibile per l'uso e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architettura locale

Ecco un diagramma che illustra l'infrastruttura locale corrente di Contoso.

![Architettura di Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso dispone di un data center principale nella città di New York, negli Stati Uniti orientali.
- Possiede anche tre filiali locali dislocate in tre località negli Stati Uniti.
- Il data center principale è connesso a Internet tramite connessione Metro Ethernet Fiber (500 Mbps).
- Ogni filiale è connessa in locale a Internet tramite connessioni aziendali, con tunnel VPN IPSec in uscita nel data center principale. In questo modo l'intera rete è connessa in modo permanente e la connettività Internet risulta ottimizzata.
- Il data center principale è completamente virtualizzato con VMware. Due host di virtualizzazione ESXi 6.5 sono gestiti dal server vCenter 6.5.
- Contoso usa Active Directory per la gestione delle identità e server DNS nella rete interna.
- I controller di dominio nel data center vengono eseguiti in macchine virtuali VMware. Il controller di dominio nelle filiali locali vengono eseguiti in server fisici.



## <a name="business-drivers"></a>Driver di business

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: occorre rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale:

- Dopo la migrazione, l'app in Azure dovrà mantenere le stesse caratteristiche prestazionali attualmente disponibili nell'ambiente VMware locale.  Lo spostamento nel cloud non significa che le prestazioni delle app siano meno critiche.
- Contoso non intende investire in questa app.  È importante per l'azienda, ma nella sua forma corrente intende semplicemente migrarla in modo sicuro al cloud.
- Le attività amministrative del database devono essere ridotte al minimo dopo la migrazione dell'app.
- Contoso non vuole usare un database SQL di Azure per questa app e sta cercando possibili alternative.

## <a name="proposed-architecture"></a>Architettura proposta

In questo scenario:

- Contoso desidera eseguire la migrazione della propria app di viaggio locale su due livelli.
- L'app è suddivisa in livelli in due macchine virtuali, WEBVM e SQLVM, e risiede nell'host VMware ESXi **contosohost1.contoso.com** versione 6.5.
- L'ambiente VMware è gestito da vCenter Server 6.5 (**vcenter.contoso.com**) in esecuzione in una VM.
- Viene eseguita la migrazione del database dell'app (SmartHotelDB) a un'istanza gestita di SQL di Azure.
- Viene eseguita la migrazione delle macchine virtuali VMware locali a una macchina virtuale di Azure.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview) | Il Servizio Migrazione del database consente di eseguire facilmente la migrazione di più origini di database nelle piattaforme dati di Azure, con tempi di inattività minimi. | Per informazioni, vedere le [aree supportate](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) per il Servizio Migrazione del database e i [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/database-migration/).
[Istanza gestita di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | L'istanza gestita è un servizio di database gestito che rappresenta un'istanza completamente gestita di SQL Server nel cloud di Azure. Usa lo stesso codice della versione più recente del motore di database di SQL Server e dispone delle funzionalità, dei miglioramenti delle prestazioni e delle patch di sicurezza più recenti. | L'esecuzione in Azure di istanze gestite di database SQL di Azure è soggetta a costi in base alla capacità. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.

 

## <a name="migration-process"></a>Processo di migrazione

Contoso eseguirà la migrazione dei livelli Web e di dati dell'app SmartHotel in Azure.

1. Poiché l'infrastruttura di Azure è già configurata, Contoso dovrà solo aggiungere un paio di componenti specifici di Azure per questo scenario.
2. Il livello dati sarà migrato con il Servizio Migrazione del database (DMS).  Il Servizio Migrazione del database si connette alla VM di Server SQL locale attraverso una connessione VPN da sito a sito tra il data center di Contoso e Azure e quindi esegue la migrazione del database.
3. Il livello web sarà migrato con una migrazione in modalità lift-and-shift con Azure Site Recovery. Ciò comporterà preparazione dell'ambiente VMware in locale, l'impostazione e l'abilitazione della replica e la migrazione delle macchine virtuali effettuandone il failover in Azure.

     ![Architettura di migrazione](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>prerequisiti

Di seguito vengono indicati i requisiti necessari a Contoso per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Registrazione in anteprima** | È necessario essere registrati per l'anteprima pubblica limitata dell'istanza gestita di SQL. La [registrazione](https://portal.azure.com#create/Microsoft.SQLManagedInstance) richiede una sottoscrizione di Azure. Il completamento della registrazione può richiedere qualche giorno. Accertarsi pertanto di provvedere per tempo prima di iniziare a distribuire questo scenario.
**Sottoscrizione di Azure** | La sottoscrizione dovrebbe essere già stata creata quando si è eseguita la valutazione nel primo articolo di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (locale)** | I server vCenter locali devono eseguire la versione 5.5, 6.0 o 6.5.<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configurazione supportata di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage).
**Servizio Migrazione del database** | Per il Servizio Migrazione del database è necessario un [dispositivo VPN locale compatibile](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> È necessario saper configurare il dispositivo VPN locale. Il dispositivo deve avere un indirizzo IPv4 pubblicamente accessibile, che non può trovarsi dietro un dispositivo NAT.<br/><br/> Accertarsi di avere accesso al database di SQL Server locale.<br/><br/> Windows Firewall deve essere in grado di accedere al motore di database di origine. [Altre informazioni](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se il computer dove si trova il database è protetto da un firewall, aggiungere regole per consentire l'accesso al database e ai file tramite la porta SMB 445.<br/><br/> Le credenziali usate per connettersi all'istanza di SQL Server di origine e all'istanza gestita di destinazione devono appartenere a membri del ruolo sysadmin del server.<br/><br/> Nel database locale deve essere presente una condivisione di rete che il Servizio Migrazione del database può usare per eseguire il backup del database di origine.<br/><br/> Verificare che l'account del servizio che esegue l'istanza di SQL Server di origine abbia privilegi di scrittura nella condivisione di rete.<br/><br/> Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete. Il Servizio Migrazione del database di Azure rappresenta queste credenziali utente per caricare i file di backup nel contenitore di archiviazione di Azure.<br/><br/> L'installazione di SQL Server Express imposta il protocollo TCP/IP su **Disabilitato** per impostazione predefinita. Assicurarsi che sia abilitato.


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco come Contoso configura la distribuzione:

> [!div class="checklist"]
> * **Passaggio 1: Configurare un'istanza gestita di SQL di Azure**. È necessario avere un'istanza gestita creata in precedenza dove eseguire la migrazione del database di SQL Server locale.
> * **Passaggio 2: Preparare il Servizio Migrazione del database**. È necessario registrare il provider di migrazione del database, si crea un'istanza e quindi si crea un progetto di Servizio Migrazione del database. È anche necessario configurare l'associazione di protezione URI per il Servizio Migrazione del database. Un URI (Uniform Resource Identifier) di firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nel proprio account di archiviazione, che consente di concedere autorizzazioni limitate per gli oggetti di archiviazione. Viene configurato un URI di firma di accesso condiviso per consentire al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione dove il servizio carica i file di backup di SQL Server.
> * **Passaggio 3: Preparare Azure per Site Recovery**: per Site Recovery è necessario creare un account di archiviazione di Azure in cui memorizzare i dati replicati e un insieme di credenziali dei Servizi di ripristino.
> * **Passaggio 4: Preparare VMware in locale per Site Recovery**. Contoso prepara gli account per l'individuazione di macchine virtuali e l'installazione dell'agente, quindi si prepara la connessione alle macchine virtuali di Azure dopo il failover.
> * **Passaggio 5: Replicare le macchine virtuali**: Per impostare la replica, viene configurato l'ambiente di origine e di destinazione di Site Recovery, si impostano i criteri di replica e si avvia la replica delle macchine virtuali nell'archiviazione di Azure.
> * **Passaggio 6: Eseguire la migrazione del database con il Servizio Migrazione del database**: È ora possibile eseguire la migrazione del database.
> * **Passaggio 7: eseguire la migrazione delle VM con Site Recovery**. Viene effettuato un failover di test per verificare che tutto funzioni come previsto e un failover completo per migrare le VM ad Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Passaggio 1: Preparare un'istanza gestita di SQL di Azure

Per impostare un'istanza gestita di SQL di Azure, Contoso deve disporre di una subnet:

- La subnet deve essere dedicata. Deve essere vuota, non contenere altri servizi cloud e non essere una subnet gateway.
- Dopo la creazione dell'istanza gestita, non si devono aggiungere risorse.
- Alla subnet non deve essere associato un gruppo di sicurezza di rete.
- La subnet deve avere una tabella di route definita dall'utente con hop successivo di tipo Internet 0.0.0.0/0 come unica route assegnata. 
- DNS personalizzato facoltativo: se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco l'indirizzo IP dei resolver ricorsivi di Azure (ad esempio 168.63.129.16). [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Alla subnet non deve essere associato alcun endpoint di servizio (archiviazione o SQL). Gli endpoint di servizio devono essere disabilitati nella rete virtuale.
- La subnet deve avere un minimo di 16 indirizzi IP. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sul dimensionamento della subnet dell'istanza gestita.
- Nel proprio ambiente ibrido, sono necessarie le impostazioni DNS personalizzate. Contoso configurerà le impostazioni DNS per l'uso di uno o più server DNS di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) sulla personalizzazione DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurare una rete virtuale per l'istanza gestita

Contoso configura la rete virtuale come segue: 

1. Crea una nuova rete virtuale (VNET-SQLMI-EU2) nell'area primaria, Stati Uniti orientali 2 nel gruppo di risorse ContosoNetworkingRG.
2. Assegna uno spazio indirizzi di 10.235.0.0/24, assicurando che l'intervallo non si sovrapponga con qualsiasi altra rete nell'organizzazione Contoso.
2. Aggiunge due subnet alla rete:
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Questa subnet sarà usata per collegare la directory all'istanza gestita (SQLMI).

    ![Rete dell'istanza gestita](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Dopo che la rete virtuale e le subnet sono state distribuite, Contoso esegue il peering delle reti come indicato di seguito:

    - Esegue il peering di VNET-SQLMI-EUS2 con VNET-HUB-EUS2 (l'hub di rete virtuale per Stati Uniti orientali 2).
    - Esegue il peering di VNET-SQLMI-EUS2 con VNET-PROD-EUS2 (la rete di produzione).

    ![Peering di rete](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso imposta le impostazioni DNS personalizzate. DNS punterà per prima cosa ai relativi controller di dominio di Azure. DNS di Azure sarà secondario. I controller di dominio di Azure di Contoso si trovano in:

    - Nella subnet PROD-DC-EUS2, nella rete di produzione degli Stati Uniti orientali 2 (VNET-PROD-EUS2).
    - Indirizzo CONTOSODC3: 10.245.42.4
    - Indirizzo CONTOSODC4: 10.245.42.5
    - Risoluzione DNS di Azure: 168.63.129.16

     ![DNS di rete](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Ulteriore assistenza?**

- [Panoramica](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) delle istanze gestite di SQL di Azure.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) sulla creazione di una rete virtuale per l'istanza gestita di SQL.
- [Informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) sull'impostazione del peering.
- [Informazioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) sull'aggiornamento delle impostazioni DNS Azure AD.



### <a name="set-up-routing"></a>Configurare il routing

L'istanza gestita viene inserita in una rete virtuale privata e Contoso deve disporre di una tabella di route per la comunicazione con il servizio di gestione di Azure. Se non può comunicare con il servizio che lo gestisce, diventa inaccessibile.

- La tabella di route contiene un set di regole (route) che specifica come i pacchetti inviati dall'istanza gestita devono essere indirizzati nella rete virtuale.
- La tabella di route viene associata alle subnet in cui sono distribuite le istanze gestite. Ogni pacchetto proveniente da una subnet viene gestito in base alla tabella di route associata.
- Una subnet può essere associata solo a una singola tabella di route.
- Non sono previsti costi aggiuntivi per la creazione di tabelle di route in Microsoft Azure.

1. Contoso crea una tabella di route definita dall'utente. La tabella di route viene creata nel gruppo di risorse ContosoNetworkingRG.

    ![Tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Per soddisfare i requisiti dell'istanza gestita di database SQL, dopo la distribuzione della tabella di route (MIRouteTable), Contoso aggiunge una route con un prefisso di indirizzo di 0.0.0.0/0, e **Tipo hop successivo** impostato su **Internet**.

    ![Prefisso della tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associa la tabella di route con la subnet SQLMI-DB-EUS2 (nella rete VNET-SQLMI-EUS2). 

    ![Subnet della tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Ulteriore assistenza?**

[Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) sulla configurazione delle route per un'istanza gestita.

### <a name="create-a-managed-instance"></a>Creare un'istanza gestita

Ora Contoso può eseguire il provisioning di un'istanza gestita di database SQL di Azure.

1. Poiché l'istanza gestita viene usata da un'applicazione aziendale, Contoso la distribuisce nella relativa area geografica Stati Uniti orientali 2 primaria, nel gruppo di risorse ContosoRG 
2. che seleziona un piano tariffario e calcola le dimensioni di calcolo e di archiviazione per l'istanza. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/) sui prezzi.

    ![Istanza gestita](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Dopo aver distribuito l'istanza gestita di database SQL, vengono visualizzate due nuove risorse nel gruppo di risorse ContosoRG:

    - Un cluster virtuale nel caso in cui sono presenti più istanze gestite e
    - l'istanza gestita di SQL Server. 

    ![Istanza gestita](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Ulteriore assistenza?**

[Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) sul provisioning di un'istanza gestita di database SQL.

## <a name="step-2-prepare-dms"></a>Passaggio 2: Preparare il Servizio Migrazione del database

Per preparare il Servizio Migrazione del database Contoso deve eseguire alcune operazioni:

- Registrare il provider del Servizio Migrazione del database in Azure
- Fornire al Servizio Migrazione del database l'accesso ad Archiviazione di Azure per caricare i file di backup usati per eseguire la migrazione di un database. Viene creato un contenitore di archiviazione BLOB e per esso viene generato un URI della firma di accesso condiviso (SAS). 
- Creare un progetto del Servizio Migrazione del database di Azure.


Seguire la procedura seguente:

1. Contoso registra il provider di migrazione del database nella propria sottoscrizione.
    ![Registrazione Servizio Migrazione del database di Azure](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Viene creato un contenitore di archiviazione BLOB e generato un URI della firma di accesso condiviso in modo che il Servizio Migrazione del database di Azure possa accedervi.

    ![URI della firma di accesso condiviso](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Infine viene creata un'istanza del Servizio Migrazione del database di Azure. 

    ![Istanza del Servizio Migrazione del database di Azure](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso inserisce l'istanza del Servizio Migrazione del database di Azure nella subnet PROD-DC-EUS2 della rete virtuale VNET-PROD-DC-EUS2.
    - L'istanza viene posizionata in questo modo perché deve trovarsi in una rete virtuale che possa accedere alla VM di SQL Server locale tramite un gateway VPN.
    - Viene eseguito il peering di VNET-PROD-EUS2 a-VNET-HUB-EUS2 e può usare i gateway remoti.  In questo modo viene garantito che il Servizio Migrazione del database di Azure possa comunicare in base alle esigenze.

        ![Rete del Servizio Migrazione del database](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Ulteriore assistenza?**
- [Informazioni](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) sull'impostazione del Servizio Migrazione del database di Azure.
- Sono disponibili [altre informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) sulla creazione e l'uso della firma di accesso condiviso.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Passaggio 3: Preparare Azure per il servizio Site Recovery

Esistono numerosi di elementi Azure necessari a Contoso per configurare Site Recovery per la migrazione delle VM di livello Web (WEBMV)

- Una rete virtuale in cui si trovano le risorse di cui è stato effettuato il failover.
- Un account di archiviazione di Azure per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino.

Site Recovery viene impostato come segue:

1. Poiché la macchina virtuale è un front-end Web per l'app SmartHotel, verrà eseguito il failover della macchina virtuale alla rete di produzione esistente (VNET-PROD-EUS2) e alla subnet (PROD FE EUS2), nell'area Stati Uniti orientali 2 primaria. Questa rete viene impostata durante la [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md)
2. Viene creato un account di archiviazione di Azure (contosovmsacc20180528). Viene usato un account di utilizzo generico con archiviazione standard e replica dell'archiviazione con ridondanza locale.

    ![Archiviazione di Site Recovery](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Dopo avere configurato la rete e l'account di archiviazione, Contoso può creare un insieme di credenziali (ContosoMigrationVault) e lo inserisce nel gruppo di risorse ContosoFailoverRG nell'area primaria Stati Uniti orientali 2.

    ![Insieme di credenziali dei servizi di ripristino](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Ulteriore assistenza?**

Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) sulla configurazione di Azure per Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 4: Preparare l'ambiente VMware locale per Site Recovery

Per preparare VMware per Site Recovery è necessario:

- Preparare un account nel server vCenter o nell'host ESXi vSphere per rendere automatica l'individuazione delle macchine virtuali.
- Preparare un account che consente l'installazione automatica del servizio Mobility nelle macchine virtuali VMware che si desidera replicare.
- Preparare le macchine virtuali locali in modo che possano connettersi alle macchine virtuali di Azure create dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Per configurare l'account, Contoso segue questa procedura:

1. Crea un ruolo a livello di vCenter.
2. Assegna al ruolo le autorizzazioni necessarie.

**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) sulla creazione e sull'assegnazione di un ruolo per l'individuazione automatica.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

È necessario installare il servizio Mobility nella macchina virtuale da replicare.

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando si abilita la replica per la macchina virtuale.
- Per l'installazione push automatica è necessario preparare un account che Site Recovery userà per accedere alla macchina virtuale.
- Si specifica questo account quando si configura la replica nella console di Azure.
- È necessario un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.

**Ulteriore assistenza**

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) sulla creazione di un account per l'installazione push del servizio Mobility.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure, Contoso intende connettersi alle macchine virtuali di Azure replicate. A tale scopo, è necessario effettuare due operazioni sulla VM locale prima di eseguire la migrazione: 

1. Per l'accesso tramite Internet, viene abilitato RDP sulla macchina virtuale prima del failover e Contoso si assicura che vengano aggiunte regole TCP e UDP per il profilo **Pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** > **App e funzionalità consentite** per tutti i profili.
2. Per l'accesso tramite una VPN da sito a sito, viene abilitato RDP nel computer locale e viene consentito il protocollo RDP in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
3. Vengono impostati i criteri SAN del sistema operativo nella VM locale su **OnlineAll**.

Inoltre, quando si effettua un failover, si deve verificare quanto segue:

- Quando si attiva un failover, nella VM non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento.
- Dopo il failover, si deve selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se l'operazione non funziona, verifica che la macchina virtuale sia in esecuzione e rivede questi [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Passaggio 5: Replicare le VM locali in Azure con Site Recovery

Prima di eseguire una migrazione in Azure, Contoso deve impostare e abilitare la replica per la macchina virtuale locale.

### <a name="set-a-replication-goal"></a>Impostare un obiettivo di replica

1. Nel nome dell'insieme di credenziali (ContosoVMVault) si seleziona un obiettivo di replica (**Introduzione** > **Site Recovery** > **Preparare l'infrastruttura**).
2. Specifica che i computer si trovano in locale, che sono macchine virtuali VMware e che occorre eseguirne la replica in Azure.

    ![Obiettivo di replica](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, è necessario confermare di aver completato la pianificazione della distribuzione selezionando **Operazione completata**. In questa distribuzione Contoso esegue la migrazione di una sola macchina virtuale, pertanto non occorre pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Ora occorre configurare l'ambiente di origine. A tale scopo, viene scaricato un modello OVF che viene usato per distribuire il server di configurazione e i componenti associati come macchina virtuale VMware locale a disponibilità elevata. I componenti del server includono:

- Il server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Dopo la creazione e l'avvio della VM del server di configurazione, Contoso può registrarla nell'insieme di credenziali.


Vengono eseguiti questi passaggi come indicato di seguito:

1. Viene scaricato il modello OVF dal portale di Azure (**Preparare l'infrastruttura** > **Origine** > **Server di configurazione**).
    
    ![Scaricare OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Importa il modello in VMware per creare e distribuire la VM.

    ![Modello OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Al primo avvio della VM, viene visualizzata un'esperienza di installazione di Windows Server 2016. Accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, si accede alla macchina virtuale come amministratore. Per impostazione predefinita, al primo accesso viene avviato lo strumento di configurazione di Azure Site Recovery.
5. Nello strumento si specifica un nome per la registrazione del server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, viene selezionato **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui verrà registrato il server di configurazione. 

    [Registrare il server di configurazione](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio. Contoso accede nuovamente alla macchina. Viene avviata automaticamente la gestione guidata del server di configurazione.
8. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
9. Si seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali in cui registrare il server di configurazione.
        ![vault](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. A questo punto scarica e installa il server MySQL e VMware PowerCLI. Quindi si convalidano le impostazioni del server.
11. Dopo la convalida, si specificano l'indirizzo IP o l'FQDN dell'host vSphere o del server vCenter. Si mantiene la porta predefinita e si specifica un nome descrittivo per il server vCenter in Azure.
12. È necessario specificare l'account creato in precedenza, in modo che Site Recovery possa individuare automaticamente le macchine virtuali VMware che sono disponibili per la replica. 
13. Vengono specificate le credenziali per l'installazione automatica del servizio Mobility quando la replica è abilitata. Per le macchine Windows, l'account deve avere privilegi di amministratore locale sulle VM. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Al termine della registrazione, Contoso verifica nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere almeno circa 15 minuti. 
8. Site Recovery si connette quindi ai server VMware usando le impostazioni specificate e individua le VM.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora Contoso deve configurare l'ambiente di replica di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** vengono selezionate le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione di Azure e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Una volta impostate l'origine e la destinazione, è possibile creare i criteri di replica e associarli al server di configurazione.

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** vengono creati i criteri **ContosoMigrationPolicy**.
2. Usa le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**. Impostazione predefinita di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**. Impostazione predefinita di un'ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
        ![Creare criteri di replica](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Ulteriore assistenza?**

- Una procedura dettagliata completa di tutti questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Abilitare la replica

Ora Contoso può iniziare a replicare la WebVM.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **+Replica** seleziona le impostazioni di origine.
2. Indica che intende abilitare le macchine virtuali, seleziona il server vCenter e il server di configurazione.

 ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Ora vengono indicate le impostazioni di destinazione, inclusi il gruppo di risorse e la rete in cui la macchina virtuale di Azure verrà posizionata dopo il failover e l'account di archiviazione in cui verranno memorizzati i dati replicati.

     ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso seleziona la WebVM per la replica. Site Recovery installa il servizio Mobility in ogni macchina virtuale per cui si è abilitata la replica. 

    ![Abilitare la replica](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso controlla che sia selezionato il criterio di replica corretto e abilita la replica per WEBVM. Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
6. In **Informazioni di base** nel portale di Azure Contoso può visualizzare la struttura per le VM con replica in Azure.

    ![Visualizzazione dell'infrastruttura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Ulteriore assistenza?**

È possibile leggere una procedura dettagliata completa di tutti questi passaggi in [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Passaggio 6: Eseguire la migrazione del database con il Servizio Migrazione del database

È necessario creare un progetto del Servizio Migrazione del database di Azure e la migrazione del database.

### <a name="create-a-dms-project"></a>Creare un progetto del Servizio Migrazione del database di Azure
1. Viene creato un progetto del Servizio Migrazione del database di Azure. Viene specificato il tipo di server di origine come SQL Server e la destinazione come istanza gestita di database SQL di Azure.

     ![Progetto del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Dopo la creazione del progetto, si apre la Migrazione guidata.

### <a name="migrate-the-database"></a>Eseguire la migrazione del database 

1. Nella Migrazione guidata, Contoso specifica la macchina virtuale di origine in cui si trova il database locale e le credenziali per accedervi.

    ![Origine del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Viene selezionano il database da migrare (SmartHotel.Registration).

    ![Database di origine del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Come destinazione viene specificato il nome dell'istanza gestita in Azure e le credenziali di accesso.

    ![Destinazione Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Quindi, in **+Nuova attività** > **Esegui migrazione** vengono specificate le impostazioni per eseguire la migrazione:
    - Credenziali di origine e di destinazione.
    - Database da migrare.
    - La condivisione di rete creata nella macchina virtuale locale. Il Servizio Migrazione del database copierà i backup di origine in questa condivisione.
        - L'account del servizio che esegue l'istanza di SQL Server di origine deve disporre dei privilegi di scrittura in questa condivisione.
        - Specificare il percorso FQDN per la condivisione.
    - L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup per la migrazione.

        ![Impostazioni del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. La migrazione viene salvata ed eseguita.
6. In **Panoramica** viene monitorato lo stato della migrazione.

    ![Monitoraggio del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Al termine della migrazione viene verificato che i database di destinazione esistano nell'istanza gestita.

    ![Monitoraggio del Servizio Migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Passaggio 7: Eseguire la migrazione della VM con Site Recovery

Contoso effettua un rapido failover di test e poi si esegue la migrazione della macchina virtuale.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

Prima della migrazione di una WEBVM, l'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto. 

1. Si effettua un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) in modo che Site Recovery arresti la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene eseguito il failover di test: 

    - Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    - Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    - Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.
3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Viene verificato che tutto funzioni correttamente. Che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. 
4. Dopo aver verificato il failover di test, Contoso lo elimina e registra e salva eventuali osservazioni. 

### <a name="migrate-the-vm"></a>Eseguire la migrazione della macchina virtuale

1. Dopo aver verificato che il failover di test funzioni come previsto, Contoso crea un piano di ripristino per la migrazione. Aggiunge WEBVM al piano.

     ![Piano di ripristino](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Esegue il failover del piano. Viene selezionato il punto di ripristino più recente e si specifica che Site Recovery deve tentare di arrestare la macchina virtuale locale prima di avviare il failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Dopo il failover, Contoso verifica che la macchina virtuale di Azure venga visualizzata come previsto nel portale di Azure.

   ![Piano di ripristino](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Dopo aver verificato la VM in Azure, completa il processo di migrazione, arresta la replica per la VM e quindi la fatturazione di Site Recovery per la VM.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Aggiornare la stringa di connessione

Come passaggio finale del processo di migrazione Contoso aggiorna la stringa di connessione dell'applicazione in modo che punti al database in esecuzione sull'istanza gestita di database SQL dopo la migrazione.

1. Nel portale di Azure viene individuata la stringa di connessione facendo clic su **Impostazioni** > **Stringhe di connessione**.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. La stringa viene aggiornata con il nome utente e la password dell'istanza gestita di database SQL.
3. Dopo la configurazione della stringa, sostituire la stringa di connessione corrente nel file web.config dell'applicazione.
4. Dopo l'aggiornamento e il salvataggio del file, riavvia IIS su WEBVM. Per il riavvio usare il comando **IISRESET /RESTART** da un prompt dei comandi.
5. Dopo il riavvio di IIS, l'applicazione userà il database in esecuzione nell'istanza gestita di database SQL.
6. A questo punto è possibile arrestare il computer SQLVM locale: la migrazione è completata.

**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) su come eseguire un failover di test. 
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) su come creare un piano di ripristino.
- Sono disponibili [informazioni](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) su come eseguire il failover in Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della la migrazione, l'app SmartHotel è in esecuzione su una VM di Azure e il database di SmartHotel si trova nell'istanza gestita di database SQL di Azure.  

Ora Contoso deve fare un po' di pulizia:  

- Rimuovere il computer WEBVM dall'inventario di vCenter.
- Rimuovere il computer SQLVM dall'inventario di vCenter.
- Rimuovere WEBVM e SQLVM dai processi di backup locali.
- Aggiornare la documentazione interna in modo da mostrare il nuovo percorso e i indirizzo IP della WEBVM.
- Rimuovere SQLVM dalla documentazione. In alternativa, è possibile contrassegnarla come eliminata e non più presente nell'inventario delle macchine virtuali.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Sicurezza

Il team responsabile della sicurezza di Contoso esamina le macchine virtuali di Azure e l'istanza gestita di database SQL per determinare eventuali problemi di sicurezza con l'implementazione.

- Per controllare l'accesso, esamina i gruppi di sicurezza di rete (NSG) per la VM. I gruppi di sicurezza di rete fanno in modo che l'app possa essere raggiunta solo dal traffico consentito.
- Considera inoltre l'opportunità di proteggere i dati sui dischi usando la Crittografia dischi di Azure e Azure KeyVault.
- Viene abilitato il rilevamento delle minacce su l'istanza gestita di database SQL (SQLMI). Verranno inviati avvisi al team responsabile della sicurezza/service desk per aprire un ticket se viene rilevata una minaccia. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Sicurezza dell'istanza gestita](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sulle procedure di sicurezza per le macchine virtuali.

### <a name="backups"></a>Backup
Contoso esegue il backup dei dati nelle VM mediante il servizio Backup di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

1. Contoso dispone di licenze esistenti per WEBVM e sfrutterà il Vantaggio Azure Hybrid.  Convertirà la VM di Azure esistente per usufruire di questi prezzi.
2. Verrà abilitata Gestione costi di Azure, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure. 


## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il rehosting dell'app SmartHotel in Azure tramite la migrazione della VM front-end dell'app ad Azure con il servizio Site Recovery. È stata eseguita la migrazione del database locale a un'istanza gestita di SQL di Azure con il Servizio Migrazione del database.

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo successivo della serie, viene illustrato come Contoso esegue il rehosting dell'app SmartHotel alle macchine virtuali di Azure usando solo il servizio Azure Site Recovery.

