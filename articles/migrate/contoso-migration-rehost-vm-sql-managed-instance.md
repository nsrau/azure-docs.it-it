---
title: Eseguire il rehosting di un'app locale di Contoso tramite la migrazione in macchine virtuali di Azure e in Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app locale in macchine virtuali di Azure e tramite Istanza gestita di database SQL di Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215340"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migrazione Contoso: esecuzione del rehosting di un'app locale in una macchina virtuale di Azure e in Istanza gestita di database SQL

In questo articolo Contoso esegue la migrazione della macchina virtuale front-end della propria app SmartHotel in una macchina virtuale di Azure tramite il servizio Azure Site Recovery. Esegue anche la migrazione del database dell'app in Istanza gestita di database SQL di Azure.

> [!NOTE]
> Istanza gestita di database SQL di Azure è al momento in anteprima.

Questo articolo fa parte di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e vari scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Alla serie verranno aggiunti altri articoli in futuro.


Articolo | Dettagli | Status
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della strategia di migrazione di Contoso, della serie di articoli e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md) | Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le macchine virtuali dell'app tramite il servizio [Azure Migrate](migrate-overview.md). e il database di SQL Server dell'app tramite [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel locale. Esegue la migrazione della macchina virtuale front-end dell'app tramite [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). e del database dell'app in un'Istanza gestita di database SQL di Azure tramite il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Questo articolo
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel alle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Contoso completa una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure mediante Site Recovery. Esegue la migrazione del database dell'app in Database di Azure per MySQL tramite MySQL Workbench. | Disponibile
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel in un'app Web di Azure e del database dell'app in un'istanza di SQL Server di Azure. | Disponibile
[Articolo 10: Eseguire il refactoring di un'app Linux in un'app Web di Azure e Database di Azure per MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket Linux in un'app Web di Azure su più siti. L'app Web è integrata con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile
[Articolo 11: Eseguire il refactoring di Team Foundation Server su Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server locale in Visual Studio Team Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione l'app SmartHotel in Azure e quindi la riprogetta. Il livello dell'app Web viene riprogettato come contenitore di Windows e il database dell'app viene riprogettato tramite il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizio app di Azure, servizio Kubernetes di Azure, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile

È possibile scaricare l'app SmartHotel di esempio usata in questo articolo da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architettura locale


Questo diagramma illustra l'infrastruttura locale corrente di Contoso:

![Architettura corrente di Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso dispone di un data center principale che si trova nella città di New York, negli Stati Uniti orientali.
- Possiede anche tre succursali locali dislocate negli Stati Uniti.
- Il data center principale è connesso a Internet tramite connessione Metro Ethernet Fiber (500 MBps).
- Ogni succursale è connessa in locale a Internet tramite connessioni aziendali, con tunnel VPN IPSec in uscita nel data center principale. Questa configurazione consente all'intera rete Contoso di essere connessa in modo permanente e la connettività Internet risulta ottimizzata.
- Il data center principale è completamente virtualizzato con VMware. Contoso dispone di due host di virtualizzazione ESXi 6.5 gestiti dal server vCenter 6.5.
- Contoso usa Active Directory per la gestione delle identità e i server DNS nella rete interna.
- I controller di dominio nel data center vengono eseguiti in macchine virtuali VMware. Il controller di dominio nelle filiali locali vengono eseguiti in server fisici.

## <a name="business-drivers"></a>Driver di business

Il team dei responsabili IT di Contoso ha collaborato attivamente con i partner commerciali dell'azienda per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Affrontare la crescita del business**: Contoso è in crescita. Di conseguenza, la pressione è aumentata sui sistemi locali e l'infrastruttura dell'azienda.
- **Aumentare l'efficienza**: occorre rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti. L'azienda richiede un reparto IT rapido ed efficiente in termini di tempo e costi, per consentire di soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente dei cambiamenti nel marketplace, in modo da consentire all'azienda di raggiungere risultati positivi in un'economia globale. Il reparto IT di Contoso non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il reparto IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team dedicato al cloud di Contoso ha identificato alcuni obiettivi per la migrazione. L'azienda usa gli obiettivi della migrazione per determinare il metodo di migrazione migliore.

- Dopo la migrazione, l'app in Azure dovrà avere le stesse caratteristiche prestazionali di cui dispone attualmente nell'ambiente VMware locale di Contoso. Lo spostamento nel cloud non significa che le prestazioni delle app siano meno critiche.
- Contoso non intende investire nell'app. È importante per l'azienda, ma Contoso intende semplicemente spostarla nella sua forma corrente nel cloud.
- Le attività amministrative del database devono essere ridotte al minimo dopo la migrazione dell'app.
- Contoso non vuole usare un database SQL di Azure per questa app e sta cercando possibili alternative.

## <a name="proposed-architecture"></a>Architettura proposta

In questo scenario:

- Contoso desidera eseguire la migrazione della propria app di viaggio locale su due livelli.
- L'app è suddivisa in livelli in due macchine virtuali, **WEBVM** e **SQLVM**, e risiede nell'host VMware ESXi versione 6.5 **contosohost1.contoso.com**. 
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso esegue la migrazione del database dell'app **SmartHotelDB** in un'Istanza gestita di database SQL di Azure.
- Contoso esegue la migrazione delle macchine virtuali VMware locali in una macchina virtuale di Azure.
- Contoso ha un data center locale (**contoso-datacenter**) e un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.

![Architettura dello scenario](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Servizi di Azure

Service | DESCRIZIONE | Costi
--- | --- | ---
[Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview) | Il Servizio Migrazione del database consente di eseguire facilmente la migrazione di più origini di database nelle piattaforme dati di Azure, con tempi di inattività minimi. | Altre informazioni sulle [aree supportate](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) e sui [prezzi del Servizio Migrazione del database](https://azure.microsoft.com/pricing/details/database-migration/).
[Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | L'istanza gestita è un servizio di database gestito che rappresenta un'istanza completamente gestita di SQL Server nel cloud di Azure. Usa lo stesso codice della versione più recente del motore di database di SQL Server e dispone delle funzionalità, dei miglioramenti delle prestazioni e delle patch di sicurezza più recenti. | L'esecuzione di Istanza gestita di database SQL di Azure è soggetta a costi in base alla capacità. Altre informazioni sui [prezzi dell'istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. Altre informazioni sui [costi e prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Processo di migrazione

Contoso eseguirà la migrazione dei livelli Web e dati dell'app SmartHotel in Azure tramite la procedura seguente:

1. Poiché l'infrastruttura di Azure è già configurata, dovrà solo aggiungere un paio di componenti specifici di Azure per questo scenario.
2. La migrazione del livello dati verrà effettuata con il Servizio Migrazione del database. Il Servizio Migrazione del database di Azure si connette alla macchina virtuale di SQL Server locale attraverso una connessione VPN da sito a sito tra il data center di Contoso e Azure e quindi esegue la migrazione del database.
3. Il livello Web sarà sottoposto a una migrazione in modalità lift-and-shift tramite Site Recovery. Questo processo comporta la preparazione dell'ambiente VMware in locale, l'impostazione e l'abilitazione della replica e la migrazione delle macchine virtuali effettuandone il failover in Azure.

     ![Architettura di migrazione](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Prerequisiti

Contoso e gli altri utenti devono soddisfare i prerequisiti seguenti per questo scenario:

Requisiti | Dettagli
--- | ---
**Registrazione nell'anteprima di Istanza gestita** | È necessario essere registrati per l'anteprima pubblica limitata di Istanza gestita di database SQL. La [registrazione](https://portal.azure.com#create/Microsoft.SQLManagedInstance) richiede una sottoscrizione di Azure. Il completamento della registrazione può richiedere qualche giorno. Accertarsi pertanto di effettuare la registrazione prima di iniziare a distribuire questo scenario.
**Sottoscrizione di Azure** | La sottoscrizione dovrebbe essere già stata creata quando si è eseguita la valutazione nel primo articolo di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore per tale sottoscrizione, è necessario rivolgersi all'amministratore per farsi assegnare le autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più specifiche, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso a Site Recovery](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (locale)** | L'istanza del server vCenter locale deve eseguire la versione 5.5, 6.0 o 6.5<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configurazione supportata di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage).
**Servizio Migrazione del database** | Per il Servizio Migrazione del database è necessario un [dispositivo VPN locale compatibile](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> È necessario saper configurare il dispositivo VPN locale. Deve avere un indirizzo IPv4 pubblico con connessione esterna che non può trovarsi dietro un dispositivo NAT.<br/><br/> Accertarsi di avere accesso al database di SQL Server locale.<br/><br/> Windows Firewall deve essere in grado di accedere al motore di database di origine. Informazioni su come configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se il computer in cui si trova il database è protetto da un firewall, aggiungere regole per consentire l'accesso al database e ai file tramite la porta SMB 445.<br/><br/> Le credenziali usate per connettersi all'istanza di SQL Server di origine e che fanno riferimento all'istanza gestita di destinazione devono appartenere a membri del ruolo del server sysadmin.<br/><br/> Nel database locale deve essere presente una condivisione di rete che il Servizio Migrazione del database può usare per eseguire il backup del database di origine.<br/><br/> Verificare che l'account del servizio che esegue l'istanza di SQL Server di origine disponga delle autorizzazioni di scrittura per la condivisione di rete.<br/><br/> Prendere nota di un utente e una password di Windows con autorizzazioni di controllo completo sulla condivisione di rete. Il Servizio Migrazione del database rappresenta queste credenziali utente per caricare i file di backup nel contenitore di Archiviazione di Azure.<br/><br/> L'installazione di SQL Server Express imposta il protocollo TCP/IP su **Disabilitato** per impostazione predefinita. Assicurarsi che sia abilitato.

## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco come Contoso intende configurare la distribuzione:

> [!div class="checklist"]
> * **Passaggio 1: Configurare un'Istanza gestita di database SQL di Azure**. Contoso deve avere un'istanza gestita creata in precedenza in cui eseguire la migrazione del database di SQL Server locale.
> * **Passaggio 2: Preparare il Servizio Migrazione del database**. Contoso deve registrare il provider di migrazione del database, creare un'istanza e quindi creare un progetto di servizio di gestione del database. Contoso deve anche configurare un URI di firma di accesso condiviso per il Servizio Migrazione del database. Un URI di firma di accesso condiviso fornisce l'accesso delegato alle risorse dell'account di archiviazione di Contoso, in modo che Contoso possa concedere autorizzazioni limitate per gli oggetti di archiviazione. Contoso configura un URI di firma di accesso condiviso per consentire al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione in cui il servizio carica i file di backup di SQL Server.
> * **Passaggio 3: Preparare Azure per Site Recovery**. Contoso deve creare un account di archiviazione per contenere i dati replicati per Site Recovery. Deve anche creare un insieme di credenziali di Servizi di ripristino di Azure.
> * **Passaggio 4: Preparare VMware in locale per Site Recovery**. Contoso prepara gli account per l'individuazione delle macchine virtuali e l'installazione dell'agente per la connessione alle macchine virtuali di Azure dopo il failover.
> * **Passaggio 5: Replicare le macchine virtuali**. Per impostare la replica, Contoso configura l'ambiente di origine e di destinazione di Site Recovery, imposta i criteri di replica e avvia la replica delle macchine virtuali in Archiviazione di Azure.
> * **Passaggio 6: Eseguire la migrazione del database tramite il Servizio Migrazione del database**. Contoso esegue la migrazione del database.
> * **Passaggio 7: Eseguire la migrazione delle macchine virtuali usando Site Recovery**. Contoso esegue un failover di test per assicurarsi che tutto funzioni. Esegue quindi un failover completo per la migrazione delle macchine virtuali in Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Passaggio 1: Configurare un'Istanza gestita di database SQL

Per configurare un'Istanza gestita di database SQL di Azure, Contoso ha bisogno di una subnet che soddisfi i requisiti seguenti:

- La subnet deve essere dedicata. Deve essere vuota e non può contenere altri servizi cloud. Non può essere una subnet del gateway.
- Dopo la creazione dell'istanza gestita, Contoso non deve aggiungere risorse alla subnet.
- Alla subnet non deve essere associato alcun gruppo di sicurezza di rete.
- La subnet deve avere una tabella di routing definita dall'utente. L'unica route assegnata deve essere 0.0.0.0/0 hop successivo su Internet. 
- DNS personalizzato facoltativo: se per la rete virtuale di Azure sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco l'indirizzo IP dei resolver ricorsivi di Azure (ad esempio 168.63.129.16). Altre informazioni su come [configurare il DNS personalizzato per un'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Alla subnet non deve essere associato alcun endpoint di servizio (archiviazione o SQL). Gli endpoint di servizio devono essere disabilitati nella rete virtuale.
- La subnet deve avere un minimo di 16 indirizzi IP. Altre informazioni su come [dimensionare la subnet dell'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- Nell'ambiente ibrido di Contoso sono necessarie impostazioni DNS personalizzate. Contoso configura le impostazioni DNS per l'uso di uno o più server DNS di Azure aziendali. Altre informazioni sulla [personalizzazione del DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurare una rete virtuale per l'istanza gestita

Contoso configura la rete virtuale come segue: 

1. Crea una nuova rete virtuale (**VNET-SQLMI-EU2**) nell'area Stati Uniti orientali 2 primaria. Aggiunge la rete virtuale al gruppo di risorse **ContosoNetworkingRG**.
2. Contoso assegna lo spazio di indirizzi 10.235.0.0/24 e verifica che l'intervallo non si sovrapponga con altre reti dell'organizzazione.
2. Aggiunge quindi due subnet alla rete:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Questa subnet viene usata per collegare una directory all'istanza gestita.

    ![Istanza gestita: creare la rete virtuale](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Dopo che la rete virtuale e le subnet sono state distribuite, Contoso esegue il peering delle reti come indicato di seguito:

    - Esegue il peering di **VNET-SQLMI-EUS2** con **VNET-HUB-EUS2** (l'hub di rete virtuale per Stati Uniti orientali 2).
    - Esegue il peering di **VNET-SQLMI-EUS2** con **VNET-PROD-EUS2** (la rete di produzione).

    ![Peering di rete](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso imposta le impostazioni DNS personalizzate. Il DNS fa riferimento prima ai controller di dominio di Azure di Contoso. Il DNS di Azure è secondario. I controller di dominio di Azure di Contoso si trovano:

    - Nella subnet **PROD-DC-EUS2**, nella rete di produzione degli Stati Uniti orientali 2 (**VNET-PROD-EUS2**)
    - Indirizzo **CONTOSODC3**: 10.245.42.4
    - Indirizzo **CONTOSODC4**: 10.245.42.5
    - Risoluzione DNS di Azure: 168.63.129.16

     ![Server DNS di rete](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Ulteriore assistenza?*

- Panoramica di [Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Informazioni su come [creare una rete virtuale per un'Istanza gestita di database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Informazioni su come [eseguire il peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Informazioni su come [aggiornare le impostazioni DNS di Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Configurare il routing

L'istanza gestita viene inserita in una rete virtuale privata. Contoso necessita di una tabella di route per consentire alla rete virtuale di comunicare con il servizio di gestione di Azure. Se la rete virtuale non può comunicare con il servizio che la gestisce, diventa inaccessibile.

Contoso considera questi fattori:

- La tabella di route contiene un set di regole (route) che specifica come i pacchetti inviati dall'istanza gestita devono essere indirizzati nella rete virtuale.
- La tabella di route viene associata alle subnet in cui sono distribuite le istanze gestite. Ogni pacchetto proveniente da una subnet viene gestito in base alla tabella di route associata.
- Una subnet può essere associata a una sola tabella di route.
- Non sono previsti costi aggiuntivi per la creazione di tabelle di route in Microsoft Azure.

 Per configurare il routing:

1. Contoso crea una tabella di route definita dall'utente. La tabella di route viene creata nel gruppo di risorse **ContosoNetworkingRG**.

    ![Tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Per soddisfare i requisiti dell'istanza gestita, dopo la distribuzione della tabella di route (**MIRouteTable**), Contoso aggiunge una route con un prefisso di indirizzo di 0.0.0.0/0. L'opzione **Tipo hop successivo** è impostata su **Internet**.

    ![Prefisso della tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associa la tabella di route con la subnet **SQLMI-DB-EUS2** (nella rete **VNET-SQLMI-EUS2**). 

    ![Subnet della tabella di route](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Ulteriore assistenza?*

Altre informazioni sulla [configurazione delle route per un'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Creare un'istanza gestita

Ora Contoso può eseguire il provisioning di un'istanza gestita di database SQL di Azure:

1. Poiché l'istanza gestita viene usata da un'applicazione aziendale, Contoso la distribuisce nell'area geografica Stati Uniti orientali 2 primaria dell'azienda e la aggiunge al gruppo di risorse **ContosoRG**.
2. Seleziona un piano tariffario e calcola le dimensioni di calcolo e di archiviazione per l'istanza. Altre informazioni sui [prezzi dell'istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Istanza gestita](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Dopo aver distribuito l'istanza gestita, vengono visualizzate due nuove risorse nel gruppo di risorse **ContosoRG**:

    - Un cluster virtuale nel caso in cui siano presenti più istanze gestite e
    - l'istanza gestita di database SQL Server. 

    ![Istanza gestita](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Ulteriore assistenza?*

Altre informazioni su come [effettuare il provisioning di un'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-management-service"></a>Passaggio 2: Preparare il Servizio Migrazione del database

Per preparare il Servizio Migrazione del database, Contoso deve eseguire alcune operazioni:

- Registrare il provider del Servizio Migrazione del database in Azure.
- Fornire al Servizio Migrazione del database l'accesso ad Archiviazione di Azure per caricare i file di backup usati per eseguire la migrazione di un database. Per fornire l'accesso ad Archiviazione di Azure, Contoso crea un contenitore di archiviazione BLOB di Azure. Genera quindi un URI di firma di accesso condiviso per il contenitore di archiviazione BLOB, 
- crea un progetto di Servizio Migrazione del database,

e infine completa i passaggi seguenti:

1. Registra il provider di migrazione del database nella propria sottoscrizione.
    ![Servizio Migrazione del database: registrazione](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Crea un contenitore di archiviazione BLOB. Genera un URI di firma di accesso condiviso in modo che il Servizio Migrazione del database possa accedervi.

    ![Servizio Migrazione del database: generazione di un URI di firma di accesso condiviso](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Crea un'istanza del Servizio Migrazione del database. 

    ![Servizio Migrazione del database: creazione di un'istanza](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Inserisce l'istanza del Servizio Migrazione del database nella subnet **PROD-DC-EUS2** della rete virtuale **VNET-PROD-DC-EUS2**.
    - Contoso sceglie questa posizione per il Servizio Migrazione del database perché il servizio deve trovarsi in una rete virtuale in grado di accedere alla macchina virtuale di SQL Server locale tramite un gateway VPN.
    - Viene eseguito il peering di **VNET-PROD-EUS2** con **VNET-HUB-EUS2** e la rete può usare i gateway remoti. L'opzione **Usa gateway remoti** assicura che il Servizio Migrazione del database sia in grado di comunicare come necessario.

        ![Servizio Migrazione del database: configurazione della rete](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Ulteriore assistenza?*

- Altre informazioni su come [configurare il Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Altre informazioni su come [creare e usare la firma di accesso condiviso](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Passaggio 3: Preparare Azure per il servizio Site Recovery

Numerosi elementi di Azure sono necessari per Contoso per configurare Site Recovery per la migrazione delle macchine virtuali di livello Web (**WEBMV**):

- Una rete virtuale in cui si trovano le risorse di cui è stato effettuato il failover.
- Un account di archiviazione per i dati replicati. 
- Un insieme di credenziali dei Servizi di ripristino in Azure.

Contoso configura Site Recovery come segue:

1. Poiché la macchina virtuale è un front-end Web per l'app SmartHotel, viene eseguito il failover della macchina virtuale nella rete di produzione esistente (**VNET-PROD-EUS2**) e nella subnet (**PROD FE EUS2**), nell'area Stati Uniti orientali 2 primaria. Contoso ha configurato la rete durante la [distribuzione dell'infrastruttura di Azure](contoso-migration-infrastructure.md).
2. Contoso crea un account di archiviazione (**contosovmsacc20180528**). Usa un account per utilizzo generico. Seleziona l'opzione di archiviazione standard e la replica di archiviazione con ridondanza locale.

    ![Site Recovery: creazione di un account di archiviazione](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Una volta creati gli account di archiviazione e di rete, Contoso crea un insieme di credenziali (**ContosoMigrationVault**). Inserisce quindi l'insieme di credenziali nel gruppo di risorse **ContosoFailoverRG**, nell'area Stati Uniti orientali 2 primaria.

    ![Servizi di ripristino: creazione di un insieme di credenziali](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Ulteriore assistenza?*

Altre informazioni sulla [configurazione di Azure per Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 4: Preparare l'ambiente VMware locale per Site Recovery

Per preparare VMware per Site Recovery, Contoso deve completare queste operazioni:

- Preparare un account nell'istanza del server vCenter o nell'host ESXi vSphere. L'account rende automatica l'individuazione delle macchine virtuali.
- Preparare un account che consenta l'installazione automatica del servizio Mobility nelle macchine virtuali VMware che vuole replicare.
- Preparare le macchine virtuali locali in modo che si connettano alle macchine virtuali di Azure create dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. Contoso necessita di un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Contoso configura l'account tramite queste attività:

1. Crea un ruolo a livello di vCenter.
2. Assegna al ruolo le autorizzazioni necessarie.

*Ulteriore assistenza?*

Altre informazioni su come [creare e assegnare un ruolo per l'individuazione automatica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato nella macchina virtuale che Contoso vuole replicare. Contoso considera i seguenti fattori relativi al servizio Mobilità:

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando Contoso abilita la replica per la macchina virtuale.
- Per l'installazione push automatica è necessario preparare un account che Site Recovery usa per accedere alla macchina virtuale.
- Questo account viene specificato durante la replica nella console di Azure.
- Contoso deve disporre di un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.

*Ulteriore assistenza*

Altre informazioni su come [creare un account per l'installazione push del servizio Mobility](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure, Contoso intende connettersi alle macchine virtuali di Azure replicate. Per connettersi alle macchine virtuali replicate in Azure, Contoso deve completare alcune operazioni nella macchina virtuale locale prima della migrazione: 

1. Per accedere tramite Internet, abilita RDP nella macchina virtuale locale prima del failover. Verifica che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
2. Per accedere tramite la VPN da sito a sito, abilita RDP nel computer locale. Contoso consente RDP in **Windows Firewall** > **App e funzionalità consentite** per le reti di **dominio e private**.
3. I criteri SAN del sistema operativo nella macchina virtuale locale vengono impostati su **OnlineAll**.

Contoso deve inoltre verificare i seguenti elementi quando esegue un failover:

- Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se ci sono aggiornamenti di Windows in sospeso, Contoso non può accedere alla macchina virtuale fino al termine dell'aggiornamento.
- Dopo il failover, Contoso deve selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile visualizzare i dati di diagnostica di avvio, deve verificare che la macchina virtuale sia in esecuzione e quindi esaminare i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>Passaggio 5: Replicare le macchine virtuali locali in Azure tramite Site Recovery

Prima di eseguire una migrazione in Azure, Contoso deve impostare e abilitare la replica per la macchina virtuale locale.

### <a name="set-a-replication-goal"></a>Impostare un obiettivo di replica

1. Nell'insieme di credenziali, sotto il nome visualizzato (**ContosoVMVault**), Contoso imposta un obiettivo di replica (**Introduzione** > **Site Recovery** > **Preparare l'infrastruttura**).
2. Specifica che i propri computer si trovano in locale, che sono macchine virtuali VMware e che occorre eseguirne la replica in Azure.

    ![Preparare l'infrastruttura: obiettivo di protezione](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Per continuare, Contoso deve verificare di aver completato la pianificazione della distribuzione. Per confermare, seleziona **Operazione completata**. In questa distribuzione Contoso esegue la migrazione di una sola macchina virtuale, pertanto non occorre pianificare la distribuzione.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Ora Contoso deve configurare il proprio ambiente di origine. Per impostare l'ambiente di origine, scarica un modello OVF, che usa per distribuire il server di configurazione e i componenti associati come una macchina virtuale VMware locale a disponibilità elevata. I componenti del server includono:

- Il server di configurazione che coordina le comunicazioni tra l'infrastruttura locale e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Il server di elaborazione:
    - Riceve i dati di replica.
    - Ottimizza i dati di replica tramite la memorizzazione nella cache, la compressione e la crittografia.
    - Invia i dati di replica in Archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility sulle macchine virtuali di cui Contoso vuole eseguire la replica. Il server di elaborazione esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Dopo la creazione e l'avvio della macchina virtuale del server di configurazione, Contoso registra il server nell'insieme di credenziali.

Per configurare l'ambiente di origine:

1. Contoso scarica il modello OVF dal portale di Azure (**Preparare l'infrastruttura** > **Origine** > **Server di configurazione**).
    
    ![Aggiungere un server di configurazione](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso importa il modello in VMware per creare e distribuire la macchina virtuale.

    ![Distribuire il modello OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Al primo avvio della macchina virtuale, viene visualizzata un'esperienza di installazione di Windows Server 2016. Contoso accetta il contratto di licenza e immette una password amministratore.
4. Al termine dell'installazione, accede alla macchina virtuale come amministratore. La prima volta che Contoso accede alla macchina virtuale, lo strumento di configurazione di Azure Site Recovery viene eseguito automaticamente.
5. Nello strumento di configurazione di Site Recovery, Contoso immette un nome da usare per registrare il server di configurazione nell'insieme di credenziali.
6. Lo strumento verifica la connessione della macchina virtuale a Azure. Dopo aver stabilito la connessione, Contoso seleziona **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui è registrato il server di configurazione. 

    ![Registrare il server di configurazione](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio. Contoso accede di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente.
8. Nella procedura guidata seleziona la scheda NIC per ricevere il traffico di replica. Questa impostazione non può essere modificata dopo che è stata configurata.
9. Contoso seleziona la sottoscrizione, il gruppo di risorse e l'insieme di credenziali di Servizi di ripristino in cui registrare il server di configurazione.

    ![Selezionare l'insieme di credenziali di Servizi di ripristino](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso scarica e installa il server MySQL e VMware PowerCLI, quindi convalida le impostazioni del server.
11. Dopo la convalida, specifica l'indirizzo IP o il nome di dominio completo dell'istanza del server vCenter o dell'host vSphere. Lascia la porta predefinita e immette un nome visualizzato per l'istanza del server vCenter in Azure.
12. Deve specificare l'account creato in precedenza, in modo che Site Recovery possa individuare automaticamente le macchine virtuali VMware disponibili per la replica. 
13. Contoso immette le credenziali per l'installazione automatica del servizio Mobility quando la replica è abilitata. Per le macchine Windows, l'account deve avere autorizzazioni di amministratore locale nelle macchine virtuali. 

    ![Configurare il server vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Al termine della registrazione, nel portale di Azure Contoso verifica di nuovo che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. L'individuazione può richiedere circa 15 minuti. 
8. Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le macchine virtuali.

### <a name="set-up-the-target"></a>Configurare la destinazione

Ora Contoso deve configurare l'ambiente di replica di destinazione:

1. In **Preparare l'infrastruttura** > **Destinazione** seleziona le impostazioni di destinazione.
2. Site Recovery verifica che nella destinazione specificata siano disponibili un account di archiviazione e una rete.

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

Una volta impostate l'origine e la destinazione, Contoso crea i criteri di replica e li associa al server di configurazione:

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** >  **Crea e associa** crea i criteri **ContosoMigrationPolicy**.
2. Contoso mantiene le impostazioni predefinite:
    - **Soglia RPO**. Impostazione predefinita di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
    - **Conservazione del punto di ripristino**: valore predefinito di 24 ore. Questo valore specifica il periodo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
    - **Frequenza snapshot coerenti con l'app**: valore predefinito 1 ora. Questo valore specifica la frequenza di creazione di snapshot coerenti con l'applicazione.
 
    ![Criteri di replica: creazione](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. I criteri vengono automaticamente associati al server di configurazione. 

    ![Criteri di replica: associazione](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Ulteriore assistenza?*

- Una procedura dettagliata completa di questi passaggi è disponibile in [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Sono disponibili istruzioni dettagliate per [configurare l'ambiente di origine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuire il server di configurazione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) e [configurare le impostazioni di replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Abilitare la replica

Ora Contoso può iniziare a replicare WebVM.

1. In **Eseguire la replica dell'applicazione** > **Origine** > **Replica** seleziona le impostazioni di origine.
2. Indica che vuole abilitare le macchine virtuali, seleziona l'istanza del server vCenter e imposta il server di configurazione.

    ![Abilitare l'origine della replica](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso specifica le impostazioni di destinazione, inclusi il gruppo di risorse e la rete in cui la macchina virtuale di Azure sarà disponibile dopo il failover. Specifica l'account di archiviazione in cui verranno archiviati i dati replicati.

    ![Abilitare la destinazione della replica](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso seleziona **WebVM** per la replica. Quando viene abilitata la replica, Site Recovery installa il servizio Mobility in ogni macchina virtuale. 

    ![Abilitare la replica: selezione della macchina virtuale](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso controlla che sia selezionato il criterio di replica corretto. Abilita quindi la replica per **WEBVM**. Contoso tiene traccia dell'avanzamento della replica in **Processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
6. In **Informazioni di base** nel portale di Azure Contoso può visualizzare la struttura per le macchine virtuali con replica in Azure:

    ![Visualizzazione dell'infrastruttura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Ulteriore assistenza?*

È possibile leggere una procedura dettagliata completa di tutti questi in [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>Passaggio 6: Eseguire la migrazione del database tramite il Servizio Migrazione del database

Contoso deve creare un progetto di Servizio Migrazione del database e quindi eseguire la migrazione del database.

### <a name="create-a-database-management-service-project"></a>Creare un progetto di Servizio Migrazione del database

1. Contoso crea un progetto del Servizio Migrazione del database. Seleziona il tipo di server di origine **SQL Server**. Seleziona **Istanza gestita di database SQL di Azure** come destinazione.

     ![Servizio Migrazione del database: nuovo progetto di migrazione](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Viene aperta la Migrazione guidata.

### <a name="migrate-the-database"></a>Eseguire la migrazione del database 

1. Nella Migrazione guidata, Contoso specifica la macchina virtuale di origine in cui si trova il database locale e le credenziali per accedervi.

    ![Servizio Migrazione del database: dettagli dell'origine](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Seleziona quindi il database di cui eseguire la migrazione (**SmartHotel.Registration**):

    ![Servizio Migrazione del database: selezione dei database di origine](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Per la destinazione, Contoso immette il nome dell'istanza gestita in Azure e inserisce le credenziali di accesso per l'istanza gestita.

    ![Servizio Migrazione del database: dettagli della destinazione](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. In **Nuova attività** > **Esegui migrazione** Contoso specifica le impostazioni per eseguire la migrazione:
    - Credenziali di origine e di destinazione.
    - Il database di cui eseguire la migrazione.
    - La condivisione di rete creata da Contoso nella macchina virtuale locale. Il Servizio Migrazione del database sposta i backup dell'origine in questa condivisione. 
        - L'account del servizio che esegue l'istanza di SQL Server di origine deve disporre delle autorizzazioni di scrittura per questa condivisione.
        - È necessario usare il percorso FQDN per la condivisione.
    - L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup per la migrazione.

        ![Servizio Migrazione del database: configurazione delle impostazioni di migrazione](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso salva la migrazione e quindi la esegue.
6. In **Panoramica** monitora lo stato della migrazione.

    ![Servizio Migrazione del database: monitoraggio](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Al termine della migrazione, Contoso verifica che i database di destinazione siano presenti nell'istanza gestita.

    ![Servizio Migrazione del database: verifica della migrazione del database](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>Passaggio 7: Eseguire la migrazione della macchina virtuale con Site Recovery

Contoso effettua un rapido failover di test e poi esegue la migrazione della macchina virtuale.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

Prima della migrazione di WEBVM, l'esecuzione di un failover di test consente di verificare che tutto funzioni come previsto. Contoso completa i passaggi seguenti:

1. Contoso esegue un failover di test al punto di ripristino più recente disponibile (**Elaborato più recente**).
2. Seleziona **Arrestare la macchina prima di iniziare il failover**. Con questa opzione selezionata, Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. 
3. Viene eseguito il failover di test: 
    1. Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
    2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
    3.  Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.
3. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Contoso verifica che tutto funzioni correttamente: la macchina virtuale deve essere delle dimensioni appropriate, deve essere connessa alla rete corretta e deve essere in esecuzione. 
4. Dopo aver verificato il failover di test, Contoso lo elimina e registra e salva eventuali osservazioni. 

### <a name="migrate-the-vm"></a>Eseguire la migrazione della macchina virtuale

1. Dopo aver verificato che il failover di test funzioni come previsto, Contoso crea un piano di ripristino per la migrazione. Contoso aggiunge WEBVM al piano:

     ![Creare un piano di ripristino: selezione egli elementi](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso esegue un failover nel piano. Seleziona il punto di ripristino più recente. Contoso specifica che Site Recovery deve provare ad arrestare la macchina virtuale locale prima di attivare il failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Dopo il failover, si verifica che la macchina virtuale di Azure venga visualizzata come previsto nel portale di Azure.

   ![Dettagli del piano di ripristino](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Dopo aver verificato la macchina virtuale in Azure, Contoso completa il processo di migrazione, arresta la replica per la macchina virtuale e quindi la fatturazione di Site Recovery per la macchina virtuale.

    ![Failover: completamento della migrazione](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Aggiornare la stringa di connessione

Come passaggio finale del processo di migrazione, Contoso aggiorna la stringa di connessione dell'applicazione in modo che punti al database migrato in esecuzione sull'istanza gestita di database SQL.

1. Nel portale di Azure Contoso individua la stringa di connessione selezionando **Impostazioni** > **Stringhe di connessione**.

    ![Stringhe di connessione](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso aggiorna la stringa con il nome utente e la password dell'Istanza gestita di database SQL.
3. Dopo la configurazione della stringa, sostituisce la stringa di connessione corrente nel file web.config dell'applicazione.
4. Dopo l'aggiornamento e il salvataggio del file, riavvia IIS su WEBVM. Per riavviare IIS, Contoso esegue `IISRESET /RESTART` in una finestra del prompt dei comandi.
5. Dopo il riavvio di IIS, l'app usa il database in esecuzione nell'Istanza gestita di database SQL.
6. A questo punto, Contoso può arrestare l'istanza locale della macchina SQLVM. La migrazione è stata completata.

*Ulteriore assistenza?*

- Altre informazioni su come [eseguire un failover di test](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Altre informazioni su come [creare un piano di ripristino](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Altre informazioni su come [eseguire il failover in Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Al termine della migrazione, l'app SmartHotel è in esecuzione su una macchina virtuale di Azure e il database di SmartHotel si trova nell'Istanza gestita di database SQL di Azure.  

A questo punto Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere il computer WEBVM dall'inventario del server vCenter.
- Rimuovere il computer SQLVM dall'inventario del server vCenter.
- Rimuovere WEBVM e SQLVM dai processi di backup locali.
- Aggiornare la documentazione interna con la nuova posizione e l'indirizzo IP di WEBVM.
- Rimuovere SQLVM dalla documentazione interna. In alternativa, Contoso può rivedere la documentazione in modo da mostrare SQLVM come eliminata e non più presente nell'inventario delle macchine virtuali.
- Esaminare tutte le risorse che interagiscono con le macchine virtuali rimosse. Aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Sicurezza

Il team responsabile della sicurezza di Contoso esamina le macchine virtuali di Azure e l'istanza gestita di database SQL per determinare eventuali problemi di sicurezza con l'implementazione:

- Il team esamina i gruppi di sicurezza di rete usati per controllare l'accesso per la macchina virtuale. I gruppi di sicurezza di rete garantiscono che solo il traffico consentito per l'app possa passare.
- Il team responsabile della sicurezza di Contoso considera inoltre l'opportunità di proteggere i dati sul disco usando Crittografia dischi di Azure e Azure Key Vault.
- Il team abilita il rilevamento delle minacce nell'istanza gestita. Il servizio invia un avviso al team responsabile della sicurezza/service desk di Contoso per aprire un ticket se viene rilevata una minaccia. Altre informazioni sul [rilevamento delle minacce per l'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Sicurezza dell'istanza gestita: rilevamento delle minacce](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Per altre informazioni sulle procedure di sicurezza per le macchine virtuali, vedere [Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Backup

Contoso esegue il backup dei dati in WEBVM mediante il servizio Backup di Azure. Altre informazioni su [Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Contoso ha una licenza esistente per WEBVM. Per poter usufruire dei prezzi con Vantaggio Azure Hybrid, Contoso converte la macchina virtuale di Azure esistente.
- Abilita quindi Gestione costi di Azure, concesso in licenza da Cloudyn, una affiliata Microsoft. Gestione costi è una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud. Altre informazioni su [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso esegue il rehosting dell'app SmartHotel in Azure tramite la migrazione della macchina virtuale front-end dell'app ad Azure con il servizio Site Recovery. Esegue quindi la migrazione del database in un'Istanza gestita di database SQL di Azure tramite il Servizio Migrazione del database di Azure.

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo successivo della serie, Contoso esegue il [rehosting dell'app SmartHotel nelle macchine virtuali di Azure](contoso-migration-rehost-vm.md) usando il servizio Azure Site Recovery.

