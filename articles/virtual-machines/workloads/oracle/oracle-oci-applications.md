---
title: Architetture per la distribuzione di app Oracle in Macchine virtuali di Azure Documenti Microsoft
description: Architetture di applicazioni per distribuire app Oracle, tra cui E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft nelle macchine virtuali di Microsoft Azure con database in Azure o in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: f36dfe0092e3447053871ee0e5b4d659bb443779
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687475"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architetture per distribuire applicazioni Oracle in Azure

Microsoft e Oracle hanno collaborato per consentire ai clienti di distribuire applicazioni Oracle come Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft nel cloud. Con l'introduzione [dell'interconnettività](configure-azure-oci-networking.md) della rete privata di anteprima tra Microsoft Azure e Oracle Cloud Infrastructure (OCI), le applicazioni Oracle possono ora essere distribuite in Azure con i relativi database back-end in Azure o OCI. Le applicazioni Oracle possono anche essere integrate con Azure Active Directory, consentendo di configurare l'accesso Single Sign-On in modo che gli utenti possano accedere all'applicazione Oracle usando le credenziali di Azure Active Directory (Azure AD).

OCI offre diverse opzioni di database Oracle per le applicazioni Oracle, tra cui DBaaS, Exadata Cloud Service, Oracle RAC e Infrastructure-as-a-Service (IaaS). Attualmente, il database autonomo non è un back-end supportato per le applicazioni Oracle.

Sono disponibili [più opzioni](oracle-overview.md) per la distribuzione di applicazioni Oracle in Azure, anche in modo altamente disponibile e sicuro. Azure offre anche immagini di macchine virtuali del database Oracle che è possibile distribuire se si sceglie di eseguire le applicazioni Oracle interamente in Azure.Azure also offers [Oracle database VM images](oracle-vm-solutions.md) that you can deploy if you choose to run your Oracle applications entirely on Azure.

Nelle sezioni seguenti vengono descritti i consigli relativi all'architettura di Microsoft e Oracle per distribuire Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft in una configurazione cross-cloud o interamente in Azure. Microsoft e Oracle hanno testato queste applicazioni e confermato che le prestazioni soddisfano gli standard stabiliti da Oracle per queste applicazioni.

## <a name="architecture-considerations"></a>Considerazioni sull'architettura

Le applicazioni Oracle sono costituite da più servizi, che possono essere ospitati nella stessa o più macchine virtuali in Azure e, facoltativamente, in OCI. 

Le istanze dell'applicazione possono essere configurate con endpoint privati o pubblici. Microsoft e Oracle consigliano di configurare una *macchina virtuale bastion host* con un indirizzo IP pubblico in una subnet separata per la gestione dell'applicazione. Assegnare quindi solo indirizzi IP privati agli altri computer, incluso il livello di database. 

Quando si configura un'applicazione in un'architettura cross-cloud, è necessaria la pianificazione per garantire che lo spazio di indirizzi IP nella rete virtuale di Azure non si sovrapponga allo spazio di indirizzi IP privati nella rete cloud virtuale OCI.

Per una maggiore sicurezza, configurare i gruppi di sicurezza di rete a livello di subnet per garantire che sia consentito solo il traffico su porte e indirizzi IP specifici. Ad esempio, i computer nel livello intermedio devono ricevere traffico solo dall'interno della rete virtuale. Nessun traffico esterno deve raggiungere direttamente le macchine di livello intermedio.

Per la disponibilità elevata, è possibile configurare istanze ridondanti dei diversi server nello stesso set di disponibilità o in zone di disponibilità diverse. Le zone di disponibilità consentono di ottenere un servizio di servizio con tempi di attività del 99,99%, mentre i set di disponibilità consentono di ottenere un servizio di servizio con tempi di attività del 99,95% nell'area. Le architetture di esempio illustrate in questo articolo vengono distribuite in due zone di disponibilità.

Quando si distribuisce un'applicazione usando l'interconnessione tra cloud, è possibile continuare a usare un circuito ExpressRoute esistente per connettere l'ambiente Azure alla rete locale. Tuttavia, è necessario un circuito ExpressRoute separato per l'interconnessione a OCI rispetto a quello che si connette alla rete locale.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) è una suite di applicazioni che include Supply Chain Management (SCM) e Customer Relationship Management (CRM). Per sfruttare il portfolio di database gestito da OCI, EBS può essere distribuito utilizzando l'interconnessione cross-cloud tra Microsoft Azure e OCI. In questa configurazione, i livelli di presentazione e applicazione vengono eseguiti in Azure e il livello database in OCI, come illustrato nel diagramma dell'architettura seguente (Figura 1).

![Architettura cross-cloud di E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Architettura cross-cloud di E-Business Suite* 

In questa architettura, la rete virtuale in Azure è connessa a una rete cloud virtuale in OCI tramite l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è impostato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche su porte specifiche.

L'architettura può anche essere adattata per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati utilizzando Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 2) è un esempio di questo modello architettonico:The following diagram (Figure 2) is an example of this architectural pattern:

![Architettura solo di E-Business Suite Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Architettura solo di E-Business Suite AzureFigure 2: E-Business Suite Azure-only architecture*

Nelle sezioni seguenti vengono descritti i diversi componenti a livello generale.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Livello applicazione (intermedio)

Il livello applicazione è isolato nella propria subnet. Sono state configurate più macchine virtuali per la tolleranza di errore e una facile gestione delle patch. Queste macchine virtuali possono essere supportate dall'archiviazione condivisa, offerta da File NetApp di Azure e SSD Ultra.These VMs can be backed by shared storage, which is offered by Azure NetApp Files and Ultra SSDs. Questa configurazione consente una distribuzione più semplice delle patch senza tempi di inattività. I computer nel livello applicazione devono essere sottoposti a un servizio di bilanciamento del carico pubblico in modo che le richieste al livello applicazione EBS vengano elaborate anche se un computer nel livello è offline a causa di un errore.

### <a name="load-balancer"></a>Bilanciamento del carico

Un servizio di bilanciamento del carico di Azure consente di distribuire il traffico tra più istanze del carico di lavoro per garantire la disponibilità elevata. In questo caso, viene configurato un servizio di bilanciamento del carico pubblico, poiché agli utenti è consentito accedere all'applicazione EBS tramite il Web. Il servizio di bilanciamento del carico distribuisce il carico a entrambi i computer nel livello intermedio. Per una maggiore sicurezza, consentire il traffico proveniente solo dagli utenti che accedono al sistema dalla rete aziendale usando una VPN da sito a sito o ExpressRoute e gruppi di sicurezza di rete.

### <a name="database-tier"></a>Livello database

Questo livello ospita il database Oracle ed è separato nella propria subnet. È consigliabile aggiungere gruppi di sicurezza di rete che consentono solo il traffico dal livello applicazione al livello di database sulla porta di database specifica di Oracle 1521.

Microsoft e Oracle consigliano una configurazione a disponibilità elevata. La disponibilità elevata in Azure può essere ottenuta impostando due database Oracle in due zone di disponibilità con Oracle Data Guard o usando Oracle Database Exadata Cloud Service in OCI. Quando si usa Oracle Database Exadata Cloud Service, il database viene distribuito in due subnet. È inoltre possibile configurare Oracle Database in macchine virtuali in OCI in due domini di disponibilità con Oracle Data Guard.You may also setup Oracle Database in VMs in OCI in two availability domains with Oracle Data Guard.


### <a name="identity-tier"></a>Livello di identità

The identity tier contains the EBS Asserter VM. EBS Asserter consente di sincronizzare le identità da Oracle Identity Cloud Service (IDCS) e Azure AD. EBS Asserter è necessario perché EBS non supporta protocolli Single Sign-On come SAML 2.0 o OpenID Connect. EBS Asserter utilizza il token di connessione OpenID (generato da IDCS), lo convalida e quindi crea una sessione per l'utente in EBS. 

Sebbene questa architettura mostri l'integrazione di IDCS, l'accesso unificato e l'accesso Single Sign-On di Azure AD possono essere abilitati anche con Oracle Access Manager con Oracle Internet Directory o Oracle Unified Directory. Per ulteriori informazioni, vedere i white paper sulla [distribuzione di Oracle EBS con l'integrazione di IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) o [sulla distribuzione di Oracle EBS con OAM Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Per garantire la disponibilità elevata, è consigliabile distribuire i server ridondanti di EBS Asserter in più zone di disponibilità con un servizio di bilanciamento del carico di fronte.

Una volta configurata l'infrastruttura, E-Business Suite può essere installato seguendo la guida all'installazione fornita da Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

JD Edwards EnterpriseOne di Oracle è una suite di applicazioni integrate di software completo per la pianificazione delle risorse aziendali. Si tratta di un'applicazione a più livelli che può essere configurata con un back-end di database Oracle o SQL Server.It is a multi-tiered application that can be set up with either an Oracle or SQL Server database backend. In questa sezione vengono illustrati i dettagli sulla distribuzione di JD Edwards EnterpriseOne con un database Oracle back-end in OCI o in Azure.This section discusses details on deploying JD Edwards EnterpriseOne with an Oracle database back-end either in OCI or in Azure.

In the following recommended architecture (Figure 3), the administration, presentation, and middle tiers are deployed to the virtual network in Azure. Il database viene distribuito in una rete cloud virtuale in OCI.

Come con E-Business Suite, è possibile configurare un livello bastion facoltativo per scopi amministrativi protetti. Usare l'host VM bastion come server di collegamento per accedere alle istanze dell'applicazione e del database.

![Architettura cross-cloud di JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Architettura cross-cloud di JD Edwards EnterpriseOne*

In questa architettura, la rete virtuale in Azure è connessa alla rete cloud virtuale in OCI tramite l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è impostato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche su porte specifiche.

L'architettura può anche essere adattata per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati utilizzando Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 4) è un esempio di questo modello architettonico:The following diagram (Figure 4) is an example of this architectural pattern:

![Architettura solo di JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Architettura solo di JD Edwards EnterpriseOne solo*

Nelle sezioni seguenti vengono descritti i diversi componenti a livello generale.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Livello amministrativo

Come suggerisce il nome, questo livello viene utilizzato per le attività amministrative. È possibile ritagliare una subnet separata per il livello amministrativo. I servizi e i server in questo livello vengono utilizzati principalmente per l'installazione e l'amministrazione dell'applicazione. Di conseguenza, le singole istanze di questi server sono sufficienti. Le istanze ridondanti non sono necessarie per la disponibilità elevata dell'applicazione.

I componenti di questo livello sono i seguenti:
    
 - **Server di provisioning:** questo server viene utilizzato per la distribuzione end-to-end dei diversi componenti dell'applicazione. Comunica con le istanze negli altri livelli, incluse le istanze nel livello di database, tramite la porta 22.It communicates with the instances in the other tiers, including the instances in the database tier, over port 22. Ospita la Console di server Manager per JD Edwards EnterpriseOne.
 - **Server di distribuzione:** questo server è necessario principalmente per l'installazione di JD Edwards EnterpriseOne. Durante il processo di installazione, questo server funge da archivio centrale per i file e i pacchetti di installazione necessari. Il software viene distribuito o distribuito ad altri server e client da questo server.
 - Client di **sviluppo:** questo server contiene componenti che vengono eseguiti in un Web browser e in applicazioni native.

### <a name="presentation-tier"></a>Livello di presentazione

Questo livello contiene vari componenti, ad esempio Application Interface Services (AIS), Application Development Framework (ADF) e Java Application Servers (JAS). I server di questo livello comunicano con i server del livello intermedio. Vengono indirizzati da un servizio di bilanciamento del carico che indirizza il traffico al server necessario in base al numero di porta e all'URL in cui viene ricevuto il traffico. È consigliabile distribuire più istanze di ogni tipo di server per la disponibilità elevata.

Di seguito sono riportati i componenti in questo livello:The following are the components in this tier:
    
- **Application Interface Services (AIS)** - Il server AIS fornisce l'interfaccia di comunicazione tra le applicazioni aziendali mobili JD Edwards EnterpriseOne e JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - Il sistema JAS riceve le richieste dal servizio di bilanciamento del carico e lo passa al livello intermedio per eseguire attività complesse. Il sistema JAS è in grado di eseguire una logica di business semplice.
- **BI Publisher Server (BIP):** questo server presenta report basati sui dati raccolti dall'applicazione JD Edwards EnterpriseOne. È possibile progettare e controllare il modo in cui il report presenta i dati in base a modelli diversi.
- **Business Services Server (BSS):** il BSS consente lo scambio di informazioni e l'interoperabilità con altre applicazioni Oracle.
- **Server eventi in tempo reale (RTE)** - Il server RTE consente di impostare notifiche a sistemi esterni sulle transazioni che si verificano nel sistema JDE EnterpriseOne. Utilizza un modello di sottoscrittore e consente ai sistemi di terze parti di sottoscrivere gli eventi. Per bilanciare il carico delle richieste a entrambi i server RTE, assicurarsi che i server siano in un cluster.
- **Server ADF (Application Development Framework):** il server ADF viene utilizzato per eseguire applicazioni JD Edwards EnterpriseOne sviluppate con Oracle ADF. Questo viene distribuito in un server Oracle WebLogic con runtime ADF.

### <a name="middle-tier"></a>Livello intermedio

Il livello intermedio contiene il server logico e il server batch. In questo caso, entrambi i server sono installati nella stessa macchina virtuale. Tuttavia, per gli scenari di produzione, è consigliabile distribuire il server logico e il server batch in server separati. Più server vengono distribuiti nel livello intermedio in due zone di disponibilità per una maggiore disponibilità. È necessario creare un servizio di bilanciamento del carico di Azure e inserire questi server nel pool back-end per garantire che entrambi i server siano attivi ed elaborazione delle richieste.

I server nel livello intermedio ricevono richieste dai server nel livello di presentazione e solo dal servizio di bilanciamento del carico pubblico. Le regole del gruppo di sicurezza di rete devono essere configurate per negare il traffico proveniente da qualsiasi indirizzo diverso dalla subnet del livello di presentazione e dal servizio di bilanciamento del carico.Network security group rules must be set up to deny traffic from any address other than the presentation tier subnet and the load balancer. È inoltre possibile impostare una regola del gruppo di sicurezza di rete per consentire il traffico sulla porta 22 dall'host bastion per scopi di gestione. Potrebbe essere possibile usare il servizio di bilanciamento del carico pubblico per bilanciare il carico delle richieste tra le macchine virtuali nel livello intermedio.

I due componenti seguenti si trovano nel livello intermedio:The following two components are in the middle tier:

- **Server logico:** contiene la logica di business o le funzioni aziendali.
- **Server batch** - Utilizzato per l'elaborazione batch

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

La suite di applicazioni PeopleSoft di Oracle contiene software per le risorse umane e la gestione finanziaria. La suite di applicazioni è a più livelli e le applicazioni includono sistemi di gestione delle risorse umane (HRMS), CRM (Customer Relationship Management), financials e supply chain management (FSCM) e Enterprise Performance Management (EPM).

È consigliabile distribuire ogni livello della suite software nella propria subnet. È necessario un database Oracle o Microsoft SQL Server come database back-end per l'applicazione. In questa sezione vengono illustrati i dettagli sulla distribuzione di PeopleSoft con un back-end del database Oracle.This section discusses details on deploying PeopleSoft with an Oracle database backend.

Di seguito è riportata un'architettura canonica per la distribuzione della suite di applicazioni PeopleSoft in un'architettura cross-cloud (Figura 5).

![Architettura cross-cloud PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Architettura cross-cloud PeopleSoft*

In questa architettura di esempio, la rete virtuale in Azure è connessa alla rete cloud virtuale in OCI tramite l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è impostato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche su porte specifiche.

L'architettura può anche essere adattata per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati utilizzando Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 6) è un esempio di questo modello architettonico:The following diagram (Figure 6) is an example of this architectural pattern:

![Architettura solo di PeopleSoft solo per Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Architettura solo di PeopleSoft solo per AzureFigure 6: PeopleSoft Azure-only architecture*

Nelle sezioni seguenti vengono descritti i diversi componenti a livello generale.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Livello applicazione

Il livello applicazione contiene istanze dei server applicazioni PeopleSoft, dei server Web PeopleSoft, della ricerca elastica e dell'utilità di pianificazione dei processi PeopleSoft. Un servizio di bilanciamento del carico di Azure è configurato per accettare le richieste degli utenti che vengono instradate al server appropriato nel livello applicazione.

Per la disponibilità elevata, è consigliabile configurare istanze ridondanti di ogni server nel livello applicazione in zone di disponibilità diverse. Il servizio di bilanciamento del carico di Azure può essere configurato con più pool back-end per indirizzare ogni richiesta al server corretto.

### <a name="peopletools-client"></a>PeopleTools Client

Il client PeopleTools viene utilizzato per eseguire attività di amministrazione, ad esempio sviluppo, migrazione e aggiornamento. Poiché il client PeopleTools non è necessario per ottenere un'elevata disponibilità dell'applicazione, i server ridondanti di PeopleTools Client non sono necessari.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Passaggi successivi

Usare [gli script Terraform](https://github.com/microsoft/azure-oracle) per configurare le app Oracle in Azure e stabilire la connettività cross-cloud con OCI.

Per altre informazioni e white paper su OCI, vedere la documentazione di [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
