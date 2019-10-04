---
title: Architetture per la distribuzione di app Oracle in macchine virtuali di Azure | Microsoft Docs
description: Architetture delle applicazioni per la distribuzione di app Oracle, tra cui E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft, in Microsoft Azure macchine virtuali con database in Azure o in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100094"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architetture per la distribuzione di applicazioni Oracle in Azure

Microsoft e Oracle hanno collaborato per consentire ai clienti di distribuire applicazioni Oracle come Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft nel cloud. Con l'introduzione della versione di anteprima della [connettività di rete privata](configure-azure-oci-networking.md) tra Microsoft Azure e Oracle Cloud Infrastructure (OCI), le applicazioni Oracle possono essere ora distribuite in Azure con i relativi database back-end in Azure o OCI. Le applicazioni Oracle possono anche essere integrate con Azure Active Directory, consentendo di configurare Single Sign-On in modo che gli utenti possano accedere all'applicazione Oracle usando le credenziali Azure Active Directory (Azure AD).

OCI offre più opzioni di database Oracle per le applicazioni Oracle, tra cui DBaaS, il servizio cloud Exadata, Oracle RAC e l'infrastruttura distribuita come servizio (IaaS). Attualmente, il database autonomo non è un back-end supportato per le applicazioni Oracle.

Sono disponibili [più opzioni](oracle-overview.md) per la distribuzione di applicazioni Oracle in Azure, tra cui in modo estremamente disponibile e sicuro. Azure offre anche [Immagini di VM del database Oracle](oracle-vm-solutions.md) che è possibile distribuire se si sceglie di eseguire le applicazioni Oracle interamente in Azure.

Le sezioni seguenti descrivono le raccomandazioni relative all'architettura sia per Microsoft che per Oracle per distribuire Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft in una configurazione tra cloud o interamente in Azure. Microsoft e Oracle hanno testato queste applicazioni e hanno confermato che le prestazioni soddisfano gli standard impostati da Oracle per queste applicazioni.

## <a name="architecture-considerations"></a>Considerazioni sull'architettura

Le applicazioni Oracle sono costituite da più servizi, che possono essere ospitati nello stesso o in più macchine virtuali in Azure e, facoltativamente, in OCI. 

Le istanze dell'applicazione possono essere configurate con endpoint privati o pubblici. Microsoft e Oracle consigliano di configurare una *macchina virtuale host Bastion* con un indirizzo IP pubblico in una subnet separata per la gestione dell'applicazione. Assegnare quindi solo indirizzi IP privati agli altri computer, incluso il livello database. 

Quando si configura un'applicazione in un'architettura tra cloud, è necessaria una pianificazione per garantire che lo spazio di indirizzi IP nella rete virtuale di Azure non si sovrappongano allo spazio di indirizzi IP privato nella rete del cloud virtuale OCI.

Per una maggiore sicurezza, configurare gruppi di sicurezza di rete a livello di subnet per garantire che sia consentito solo il traffico su porte e indirizzi IP specifici. Ad esempio, i computer nel livello intermedio dovrebbero ricevere solo traffico dalla rete virtuale. Nessun traffico esterno deve raggiungere direttamente i computer di livello intermedio.

Per la disponibilità elevata, è possibile configurare istanze ridondanti dei diversi server nello stesso set di disponibilità o in diverse zone di disponibilità. Le zone di disponibilità consentono di ottenere un contratto di contratto di tempo di esecuzione del 99,99%, mentre i set di disponibilità consentono di ottenere un contratto di tempo di esecuzione del 99,95% in-Region. Le architetture di esempio illustrate in questo articolo vengono distribuite in due zone di disponibilità.

Quando si distribuisce un'applicazione usando l'interconnessione tra cloud, è possibile continuare a usare un circuito ExpressRoute esistente per connettere l'ambiente Azure alla rete locale. Tuttavia, è necessario un circuito ExpressRoute separato per l'interconnessione a OCI rispetto a quello che si connette alla rete locale.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) è una suite di applicazioni, tra cui Supply Chain Management (SCM) e Customer Relationship Management (CRM). Per sfruttare i vantaggi del portfolio di database gestiti di OCI, EBS può essere distribuito usando l'interconnessione tra cloud tra Microsoft Azure e OCI. In questa configurazione i livelli di presentazione e applicazione vengono eseguiti in Azure e nel livello database in OCI, come illustrato nel diagramma dell'architettura seguente (Figura 1).

![Architettura E-Business Suite tra cloud](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Architettura E-Business Suite tra cloud* 

In questa architettura la rete virtuale in Azure è connessa a una rete cloud virtuale in OCI usando l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è configurato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche in porte specifiche.

L'architettura può essere adattata anche per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati con Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 2) è un esempio di questo modello di architettura:

![Architettura solo Azure E-Business Suite](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Architettura solo Azure E-Business Suite*

Nelle sezioni seguenti vengono descritti i diversi componenti di livello superiore.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Livello applicazione (intermedia)

Il livello applicazione è isolato nella propria subnet. Sono disponibili più macchine virtuali configurate per la tolleranza di errore e la gestione delle patch semplificata. Queste macchine virtuali possono essere supportate da una risorsa di archiviazione condivisa, offerta da Azure NetApp Files e SSD ultra. Questa configurazione consente una distribuzione più semplice delle patch senza tempi di inattività. I computer del livello applicazione devono essere gestiti da un servizio di bilanciamento del carico pubblico in modo che le richieste al livello applicazione EBS vengano elaborate anche se un computer nel livello è offline a causa di un errore.

### <a name="load-balancer"></a>Servizio di bilanciamento del carico

Un servizio di bilanciamento del carico di Azure consente di distribuire il traffico tra più istanze del carico di lavoro per garantire la disponibilità elevata. In questo caso, viene configurato un servizio di bilanciamento del carico pubblico perché gli utenti sono autorizzati ad accedere all'applicazione EBS sul Web. Il servizio di bilanciamento del carico distribuisce il carico a entrambi i computer del livello intermedio. Per una maggiore sicurezza, consentire il traffico solo dagli utenti che accedono al sistema dalla rete aziendale usando una VPN da sito a sito o ExpressRoute e gruppi di sicurezza di rete.

### <a name="database-tier"></a>Livello database

Questo livello ospita il database Oracle ed è suddiviso in una propria subnet. È consigliabile aggiungere gruppi di sicurezza di rete che consentano solo il traffico dal livello applicazione al livello database sulla porta del database specifica di Oracle 1521.

Microsoft e Oracle consigliano una configurazione a disponibilità elevata. È possibile ottenere la disponibilità elevata in Azure impostando due database Oracle in due zone di disponibilità con Oracle Data Guard oppure usando Oracle Database servizio cloud Exadata in OCI. Quando si usa Oracle Database servizio cloud Exadata, il database viene distribuito in due subnet. È anche possibile configurare Oracle Database in macchine virtuali in OCI in due domini di disponibilità con Oracle Data Guard.


### <a name="identity-tier"></a>Livello di identità

Il livello Identity contiene la VM dell'asserzione EBS. L'asserzione EBS consente di sincronizzare le identità da Oracle Identity Cloud Service (IDC) e Azure AD. L'asserzione EBS è necessaria perché EBS non supporta protocolli Single Sign-On come SAML 2,0 o OpenID Connect. L'asserzione EBS usa il token OpenID Connect (generato da IDC), lo convalida e quindi crea una sessione per l'utente in EBS. 

Anche se questa architettura Mostra l'integrazione di IDC, Azure AD accesso unificato e Single Sign-On possono essere abilitati con Oracle Access Manager con Oracle Internet Directory o Oracle Unified directory. Per altre informazioni, vedere la pagina relativa alla [distribuzione di Oracle EBS con IDC](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) in white paper o alla [distribuzione di Oracle EBS con OAM Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Per la disponibilità elevata, è consigliabile distribuire i server ridondanti dell'asserzione EBS tra più zone di disponibilità con un servizio di bilanciamento del carico.

Dopo aver configurato l'infrastruttura, è possibile installare E-Business Suite seguendo la Guida all'installazione fornita da Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

JD Edwards EnterpriseOne di Oracle è una suite di applicazioni integrate di software completo per la pianificazione delle risorse aziendali. Si tratta di un'applicazione a più livelli che può essere configurata con un back-end del database Oracle o SQL Server. In questa sezione vengono illustrati i dettagli sulla distribuzione di JD Edwards EnterpriseOne con un back-end del database Oracle in OCI o in Azure.

Nell'architettura consigliata seguente (Figura 3), i livelli di amministrazione, presentazione e intermedio vengono distribuiti nella rete virtuale in Azure. Il database viene distribuito in una rete cloud virtuale in OCI.

Come per E-Business Suite, è possibile configurare un livello Bastion facoltativo per scopi amministrativi sicuri. Usare l'host della macchina virtuale Bastion come server di salto per accedere alle istanze dell'applicazione e del database.

![Architettura di JD Edwards EnterpriseOne tra cloud](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Architettura di JD Edwards EnterpriseOne tra cloud*

In questa architettura la rete virtuale in Azure è connessa alla rete cloud virtuale in OCI usando l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è configurato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche in porte specifiche.

L'architettura può essere adattata anche per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati con Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 4) è un esempio di questo modello di architettura:

![Architettura solo Azure di JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Architettura solo Azure di JD Edwards EnterpriseOne*

Nelle sezioni seguenti vengono descritti i diversi componenti di livello superiore.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Livello amministrativo

Come suggerisce il nome, questo livello viene usato per le attività amministrative. È possibile suddividere in una subnet separata per il livello amministrativo. I servizi e i server in questo livello vengono utilizzati principalmente per l'installazione e l'amministrazione dell'applicazione. Di conseguenza, sono sufficienti istanze singole di questi server. Le istanze ridondanti non sono necessarie per la disponibilità elevata dell'applicazione.

I componenti di questo livello sono i seguenti:
    
 - **Server** di provisioning: questo server viene usato per la distribuzione end-to-end dei diversi componenti dell'applicazione. Comunica con le istanze negli altri livelli, incluse le istanze nel livello database, sulla porta 22. Ospita la console di Server Manager per JD Edwards EnterpriseOne.
 - **Server di distribuzione** : questo server è principalmente necessario per l'installazione di JD Edwards EnterpriseOne. Durante il processo di installazione, questo server funge da repository centrale per i file e i pacchetti di installazione necessari. Il software viene distribuito o distribuito ad altri server e client da questo server.
 - **Client di sviluppo** : questo server contiene componenti eseguiti in un Web browser, nonché applicazioni native.

### <a name="presentation-tier"></a>Livello presentazione

Questo livello contiene diversi componenti, ad esempio Application Interface Services (AIS), Application Development Framework (ADF) e Java Application Servers (JAS). I server in questo livello comunicano con i server nel livello intermedio. Sono gestite da un servizio di bilanciamento del carico che instrada il traffico al server necessario in base al numero di porta e all'URL in cui viene ricevuto il traffico. Per la disponibilità elevata, è consigliabile distribuire più istanze di ogni tipo di server.

Di seguito sono riportati i componenti di questo livello:
    
- **Application Interface Services (AIS)** : il server AIS fornisce l'interfaccia di comunicazione tra JD Edwards EnterpriseOne Mobile Enterprise Applications e JD Edwards EnterpriseOne.
- **Server applicazioni Java (Jas)** : il Jas riceve le richieste dal servizio di bilanciamento del carico e le passa al livello intermedio per eseguire attività complesse. Il JAS è in grado di eseguire una logica di business semplice.
- **Server di pubblicazione bi (bip)** : questo server presenta report basati sui dati raccolti dall'applicazione JD Edwards EnterpriseOne. È possibile progettare e controllare il modo in cui il report presenta i dati in base a modelli diversi.
- **Server dei servizi aziendali (BSS)** : il BSS consente lo scambio di informazioni e l'interoperabilità con altre applicazioni Oracle.
- **Server eventi in tempo reale (RTE)** : il server RTE consente di impostare notifiche per sistemi esterni sulle transazioni che si verificano nel sistema JDE EnterpriseOne. Usa un modello di Sottoscrittore e consente ai sistemi di terze parti di sottoscrivere gli eventi. Per bilanciare il carico delle richieste a entrambi i server RTE, assicurarsi che i server si trovino in un cluster.
- **Server di Application Development Framework (ADF)** : il server ADF viene usato per eseguire le applicazioni JD Edwards EnterpriseOne sviluppate con Oracle ADF. Questa operazione viene distribuita in un server Oracle WebLogic con runtime di ADF.

### <a name="middle-tier"></a>Livello intermedio

Il livello intermedio contiene il server di logica e il server batch. In questo caso, entrambi i server sono installati nella stessa macchina virtuale. Per gli scenari di produzione, tuttavia, è consigliabile distribuire il server di logica e il server batch in server distinti. Più server vengono distribuiti nel livello intermedio tra due zone di disponibilità per una maggiore disponibilità. È necessario creare un servizio di bilanciamento del carico di Azure e inserire questi server nel pool back-end per assicurarsi che entrambi i server siano attivi e che elaborano le richieste.

I server nel livello intermedio ricevono le richieste dai server nel livello di presentazione e dal servizio di bilanciamento del carico pubblico. Le regole del gruppo di sicurezza di rete devono essere configurate per negare il traffico da qualsiasi indirizzo diverso dalla subnet del livello presentazione e dal servizio di bilanciamento del carico. Una regola NSG può essere configurata anche per consentire il traffico sulla porta 22 dall'host Bastion a scopo di gestione. Potrebbe essere possibile usare il servizio di bilanciamento del carico pubblico per bilanciare il carico delle richieste tra le macchine virtuali nel livello intermedio.

I due componenti seguenti si trovano nel livello intermedio:

- **Server logico** -contenere la logica di business o le funzioni di business.
- **Server batch** : usato per l'elaborazione batch

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

PeopleSoft Application Suite di Oracle contiene software per le risorse umane e la gestione finanziaria. La suite di applicazioni è a più livelli e le applicazioni includono i sistemi di gestione delle risorse umane (HRMS), la gestione delle relazioni con i clienti (CRM), Financials e Supply Chain Management (FSCM) e la gestione delle prestazioni aziendali (EPM).

Si consiglia di distribuire ogni livello della suite di software nella propria subnet. Un database o Microsoft SQL Server Oracle è necessario come database back-end per l'applicazione. In questa sezione vengono illustrati i dettagli sulla distribuzione di PeopleSoft con un back-end del database Oracle.

Di seguito è riportata un'architettura canonica per la distribuzione di PeopleSoft Application Suite in un'architettura tra cloud (Figura 5).

![Architettura tra cloud PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Architettura tra cloud PeopleSoft*

In questa architettura di esempio, la rete virtuale in Azure è connessa alla rete cloud virtuale in OCI usando l'interconnessione tra cloud. Il livello applicazione è configurato in Azure, mentre il database è configurato in OCI. È consigliabile distribuire ogni componente nella propria subnet con gruppi di sicurezza di rete per consentire il traffico solo da subnet specifiche in porte specifiche.

L'architettura può essere adattata anche per la distribuzione interamente in Azure con database Oracle a disponibilità elevata configurati con Oracle Data Guard in due zone di disponibilità in un'area. Il diagramma seguente (Figura 6) è un esempio di questo modello di architettura:

![Architettura solo Azure PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Architettura solo Azure PeopleSoft*

Nelle sezioni seguenti vengono descritti i diversi componenti di livello superiore.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Livello applicazione

Il livello applicazione contiene le istanze dei server applicazioni PeopleSoft, i server Web PeopleSoft, la ricerca elastica e l'utilità di pianificazione dei processi PeopleSoft. Un servizio di bilanciamento del carico di Azure viene configurato per accettare le richieste degli utenti indirizzate al server appropriato nel livello applicazione.

Per la disponibilità elevata, è consigliabile configurare istanze ridondanti di ogni server nel livello applicazione tra diverse zone di disponibilità. Il servizio di bilanciamento del carico di Azure può essere configurato con più pool back-end per indirizzare ogni richiesta al server corretto.

### <a name="peopletools-client"></a>Client PeopleTools

Il client PeopleTools viene usato per eseguire attività amministrative, ad esempio sviluppo, migrazione e aggiornamento. Poiché il client PeopleTools non è necessario per ottenere la disponibilità elevata dell'applicazione, non sono necessari server ridondanti del client PeopleTools.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Passaggi successivi

Usare gli [script di bonifica](https://github.com/microsoft/azure-oracle) per configurare le app Oracle in Azure e stabilire la connettività tra cloud con OCI.

Per ulteriori informazioni e white paper su OCI, vedere la documentazione sul [cloud Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
