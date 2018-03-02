---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web su tre livelli UK-OFFICIAL
description: Azure Security and Compliance Blueprint - Automazione di applicazioni Web su tre livelli UK-OFFICIAL
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9d95ccdd536efbff1540fab2b564e7745f5ac397
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Azure Security and Compliance Blueprint - Automazione di applicazioni Web su tre livelli UK-OFFICIAL

## <a name="overview"></a>Panoramica

 Questo articolo fornisce indicazioni e script di automazione per la distribuzione di un'architettura a tre livelli basata sul Web per Microsoft Azure ottimale per la gestione di molti carichi di lavoro classificati come "OFFICIAL" nel Regno Unito.

 Usando un approccio di tipo "infrastruttura come codice", il set di modelli di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) distribuisce un ambiente conforme ai [principi per la sicurezza cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) di UK National Cyber Security Centre (NCSC) 14 e ai [controlli di sicurezza critici](https://www.cisecurity.org/critical-controls.cfm) del Center for Internet Security (CIS).

 NCSC consiglia l'adozione dei principi per la sicurezza cloud da parte dei clienti per valutare le proprietà di sicurezza del servizio e per contribuire alla comprensione della suddivisione delle responsabilità tra cliente e fornitore. Sono disponibili informazioni relative a ogni principio per semplificare la comprensione della suddivisione delle responsabilità.

 Questa architettura e i modelli corrispondenti di Azure Resource Manager sono supportati dal white paper Microsoft, [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1) (14 controlli di sicurezza del cloud per il cloud del Regno Unito con Microsoft Azure). Questo documento illustra il modo in cui i servizi di Azure rispettano i principi per la sicurezza cloud di UK NCSC 14, consentendo quindi alle organizzazioni ottenere rapidamente l'idoneità ai requisiti di conformità tramite servizi basati sul cloud a livello globale e nel Regno Unito sul cloud Microsoft Azure.

 Questo modello distribuisce l'infrastruttura per il carico di lavoro. Il codice dell'applicazione e il software per il livello aziendale di supporto e per il livello dati devono essere installati e configurati. Per istruzioni dettagliate sulla distribuzione, vedere [qui](https://aka.ms/ukwebappblueprintrepo).

 Se non si ha una sottoscrizione di Azure, è possibile iscriversi in modo semplice e rapido. Vedere [Inizia a usare Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagramma dell'architettura e componenti

 I modelli di Azure offrono un'architettura a tre livelli per un'applicazione Web in un ambiente cloud di Azure che supporta carichi di lavoro UK-OFFICIAL. L'architettura offre un ambiente ibrido sicuro che estende una rete locale in Azure, consentendo l'accesso sicuro ai carichi di lavoro basati sul Web da parte di utenti aziendali o da Internet.

![alt text](images/diagram.png?raw=true "Architettura a tre livelli di Azure per UK-OFFICIAL")

 Questa soluzione usa i servizi di Azure seguenti. Per dettagli dell'architettura di distribuzione, vedere la sezione relativa all'[architettura di distribuzione](#deployment-architecture).

(1) Rete virtuale /16 - Rete virtuale operativa
- (3) Subnet /24 - A 3 livelli (Web, business, dati)
- (1) Subnet /27 - AD DS
- (1) Subnet /27 - Subnet del gateway
- (1) Subnet /29 - Subnet del gateway applicazione
- Usa il DNS predefinito (fornito da Azure)
- Peering abilitato per la rete virtuale di gestione
- Gruppo di sicurezza di rete per la gestione del flusso di traffico

(1) Rete virtuale /24 - Rete virtuale di gestione
- (1) Subnet /27
- Usa (2) DNS di AD DS e (1) voci DNS di Azure
- Peering abilitato per la rete virtuale operativa
- Gruppo di sicurezza di rete per la gestione del flusso di traffico

(1) Gateway applicazione
- WAF - Abilitato
- Modalità WAF - Prevenzione
- Set di regole: OWASP 3.0
- Listener HTTP sulla porta 80
- Connettività/Traffico regolato tramite il gruppo di sicurezza di rete
- Endpoint definito per l'indirizzo IP pubblico (Azure)

(1) VPN - Tunnel VPN IPSec da sito a sito basato su route
- Endpoint definito per l'indirizzo IP pubblico (Azure)
- Connettività/Traffico regolato tramite il gruppo di sicurezza di rete
- (1) Gateway di rete locale (endpoint locale)
- (1) Gateway di rete di Azure (endpoint di Azure)

(9) Macchine virtuali - Tutte le VM vengono distribuite con le impostazioni DSC dell'antimalware IaaS di Azure

- (2) Controller di dominio di Active Directory Domain Services (Windows Server 2012 R2)
  - (2) Ruoli del server DNS - 1 per VM
  - (2) Schede di interfaccia di rete connesse alla rete virtuale operativa - 1 per VM
  - Entrambe sono aggiunte al dominio definito nel modello
    - Dominio creato come parte della distribuzione


- (1) VM di gestione jumpbox (bastion host)
  - 1 scheda di interfaccia di rete nella rete virtuale di gestione con indirizzo IP pubblico
    - Il gruppo di sicurezza di rete viene usato per limitare il traffico (ingresso/uscita) a origini specifiche
  - Non aggiunto a un dominio


- (2) VM di livello Web
  - (2) Ruoli del server IIS - 1 per VM
  - (2) Schede di interfaccia di rete connesse alla rete virtuale operativa - 1 per VM
  - Non aggiunto a un dominio


- (2) VM di livello aziendale
  - (2) Schede di interfaccia di rete connesse alla rete virtuale operativa - 1 per VM
  - Non aggiunto a un dominio


- (2) VM di livello dati
  - (2) Schede di interfaccia di rete connesse alla rete virtuale operativa - 1 per VM
  - Non aggiunto a un dominio

SET DI DISPONIBILITÀ
- (1) Set di VM di controller di dominio di Active Directory - 2 VM
- (1) Set di VM di livello Web - 2 VM
- (1) Set di VM di livello aziendale - 2 VM
- (1) Set di VM di livello dati - 2 VM

Bilanciamento del carico
- (1) Servizio di bilanciamento del carico di livello Web
- (1) Servizio di bilanciamento del carico di livello aziendale
- (1) Servizio di bilanciamento del carico di livello dati

Archiviazione
- (14) Totale account di archiviazione
  - Set di disponibilità dei controller di dominio di Active Directory
    - (2) Account di archiviazione con ridondanza locale primari - 1 per ogni VM  
    - (1) Account di archiviazione con ridondanza locale di diagnostica per il set di disponibilità di AD DS
  - VM jumpbox di gestione
    - (1) Account di archiviazione con ridondanza locale primario per la VM jumpbox
    - (1) Account di archiviazione con ridondanza locale di diagnostica per la VM jumpbox
  - VM di livello Web
    - (2) Account di archiviazione con ridondanza locale primari - 1 per ogni VM  
    - (1) Account di archiviazione con ridondanza locale di diagnostica per il set di disponibilità di livello Web
  - VM di livello aziendale
    - (2) Account di archiviazione con ridondanza locale primari - 1 per ogni VM  
    - (1) Account di archiviazione con ridondanza locale di diagnostica per il set di disponibilità di livello aziendale
  - VM di livello dati
    - (2) Account di archiviazione con ridondanza locale primari - 1 per ogni VM  
    - (1) Account di archiviazione con ridondanza locale di diagnostica per il set di disponibilità di livello dati

### <a name="deployment-architecture"></a>Architettura di distribuzione:

**Rete locale**: rete locale privata implementata in un'organizzazione.

**Rete virtuale di produzione**: la [rete virtuale](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) di produzione ospita l'applicazione e altre risorse operative in esecuzione in Azure. Ogni rete virtuale può contenere alcune subnet, che vengono usate per l'isolamento e la gestione del traffico di rete.

**Livello Web**: gestisce le richieste HTTP in ingresso. Le risposte vengono restituite tramite questo livello.

**Livello aziendale**: implementa i processi aziendali e altra logica funzionale per il sistema.

**Livello database**: fornisce l'archiviazione persistente dei dati tramite i [gruppi di disponibilità AlwaysOn di SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) per la disponibilità elevata. I clienti possono usare il [database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) come alternativa PaaS.

**Gateway**: il [gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) offre la connettività tra i router nella rete locale e la rete virtuale di produzione.

**Gateway Internet e indirizzo IP pubblico**: il gateway Internet espone i servizi dell'applicazione agli utenti tramite Internet. Il traffico che accede a tali servizi viene protetto tramite un [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) che offre funzionalità di routing di livello 7 e di bilanciamento del carico con la protezione del web application firewall.

**Rete virtuale di gestione**: questa [rete virtuale](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) contiene risorse che implementano funzionalità di gestione e monitoraggio per i carichi di lavoro in esecuzione nella rete virtuale di produzione.

**Jumpbox**: definito anche [bastion host](https://en.wikipedia.org/wiki/Bastion_host), è una VM protetta in rete usata dagli amministratori per connettersi a macchine virtuali nella rete virtuale di produzione. Il jumpbox ha un gruppo di sicurezza di rete (NSG) che consente il traffico remoto solo da Indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete. La gestione delle risorse di produzione viene eseguita tramite RDP con una VM jumpbox protetta.

**Route definite dall'utente**: le [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) vengono usate per definire il flusso del traffico IP nelle reti virtuali di Azure.

**Reti virtuali con peering di rete**: le reti virtuali di produzione e di gestione sono connesse tramite [peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Queste reti virtuali vengono gestite comunque come risorse separate, ma vengono visualizzate come un elemento unico per tutte le finalità di connettività da queste macchine virtuali. Queste reti comunicano tra loro direttamente tramite indirizzi IP privati. Il peering reti virtuali è soggetto alle reti virtuali che si trovano nella stessa area di Azure.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che consentono o negano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM.

**Active Directory Domain Services (AD DS)**: questa architettura offre una distribuzione dedicata di [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx).

**Registrazione e controllo**: [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) acquisisce operazioni eseguite nelle risorse della sottoscrizione, inclusi dati quali l'utente che ha avviato l'operazione, il momento in cui è stata eseguita l'operazione, lo stato dell'operazione e i valori di altre proprietà che potrebbero essere utili per eseguire ricerche sull'operazione. Log attività di Azure è un servizio piattaforma di Azure che acquisisce tutte le azioni eseguite in una sottoscrizione. I log possono essere archiviati o esportati, se necessario.

**Monitoraggio e avvisi di rete**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) è un servizio piattaforma che fornisce funzionalità di acquisizione di pacchetti di rete, registrazione dei flussi, strumenti per la topologia e diagnostica per traffico di rete nelle reti virtuali.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: i carichi di lavoro sono raggruppati in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per consentire di assicurare la disponibilità elevata delle macchine virtuali in Azure. Questa configurazione consente di assicurare che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure.

### <a name="logging-and-audit"></a>Registrazione e controllo

**Monitoraggio**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) è il servizio piattaforma che fornisce un'origine singola per il monitoraggio del log attività, delle metriche e dei log di diagnostica di tutte le risorse di Azure. Monitoraggio di Azure può essere configurato in modo da visualizzare, eseguire query, indirizzare, archiviare ed eseguire operazioni relative alle metriche e ai log provenienti dalle risorse di Azure. È consigliabile usare il controllo di accesso in base alle risorse per proteggere l'audit trail e assicurare che gli utenti non possano modificare i log.

**Log attività**: configurare i [log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) per fornire informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.

**Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono tutti i log generati da una risorsa. Questi log possono includere log eventi del sistema di Windows, BLOB, tabelle e log delle code.

**Log del firewall**: il gateway applicazione fornisce log completi relativi a diagnostica e accesso. I registri firewall sono disponibili per le risorse del gateway applicazione con WAF abilitato.

**Archiviazione di log**: l'archiviazione dei dati dei log può essere configurata per la scrittura in un account di archiviazione di Azure centralizzato per archiviazione e per un periodo di conservazione specifico. I log possono essere elaborati tramite Azure Log Analytics o tramite sistemi SIEM di terze parti.

### <a name="identity"></a>Identità

**Active Directory Domain Services**: questa architettura offre una distribuzione di Active Directory Domain Services in Azure. Per consigli specifici sull'implementazione di Active Directory in Azure, vedere gli articoli seguenti:

[Estensione di Active Directory Domain Services in Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integrazione in Active Directory**: come alternativa a un'architettura dedicata di AD DS, i clienti possono usare l'integrazione in [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) o [Active Directory in Azure aggiunto a una foresta locale](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Sicurezza

**Sicurezza della gestione**: questo progetto consente agli amministratori di connettersi alla rete virtuale di gestione e al jumpbox tramite RDP da un'origine attendibile. Il traffico di rete per la rete virtuale di gestione viene controllato tramite gruppi di sicurezza di rete. L'accesso alla porta 3389 è limitato al traffico da un intervallo di indirizzi IP attendibile, che può accedere alla subnet contenente il jumpbox.

I clienti possono prendere in considerazione l'uso di un [modello amministrativo di sicurezza avanzata](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) per proteggere l'ambiente in caso di connessione alla rete virtuale di gestione e al jumpbox. Per una sicurezza avanzata è consigliabile che i clienti usino una [workstation di accesso con privilegi](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) e una configurazione di tipo RDGateway. L'uso delle appliance di rete virtuali e di reti perimetrali pubbliche/private offrirà miglioramenti aggiuntivi della sicurezza.

**Protezione della rete**: i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sono consigliati per ogni subnet per offrire un secondo livello di protezione dal traffico in ingresso, ignorando un gateway non configurato correttamente o disabilitato. Esempio - [Modello di Resource Manager per la distribuzione di un gruppo di sicurezza di rete](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Protezione di endpoint pubblici**: il gateway Internet espone i servizi dell'applicazione agli utenti tramite Internet. Il traffico che accede a tali servizi viene protetto tramite un [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), che offre un web application firewall e la gestione del protocollo HTTPS.

**Intervalli di indirizzi IP**: gli intervalli di indirizzi IP nell'architettura sono intervalli suggeriti. È consigliabile che i clienti esaminino i rispettivi ambienti e usino intervalli appropriati.

**Connettività ibrida**: i carichi di lavoro basati sul cloud sono connessi al data center locale tramite rete privata virtuale IPSec con gateway VPN di Azure. I clienti devono assicurarsi di usare un gateway VPN appropriato per la connessione ad Azure. Esempio: [Modello di Resource Manager per un gateway VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). È consigliabile che i clienti che eseguono carichi di lavoro su larga scala e critici con requisiti per Big Data prendano in considerazione un'architettura di rete ibrida con [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) per la connettività di rete privata verso i servizi cloud Microsoft.

**Separazione dei compiti**: questa architettura di riferimento separa le reti virtuali per operazioni di gestione e operazioni di business. La separazione di reti virtuali e subnet consente la gestione del traffico, incluse le limitazioni al traffico in ingresso e in uscita, tramite gruppi di sicurezza di rete tra segmenti di rete in base alle procedure consigliate illustrate in [Servizi cloud Microsoft e sicurezza di rete](https://docs.microsoft.com/azure/best-practices-network-security).

**Gestione delle risorse**: le risorse di Azure, ad esempio le macchine virtuali, le reti virtuali e i servizi di bilanciamento del carico, vengono gestite tramite il raggruppamento in [gruppi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). I ruoli di controllo di accesso in base alle risorse possono essere quindi assegnati a ogni gruppo di risorse per limitare l'accesso ai soli utenti autorizzati.

**Restrizioni di controllo di accesso**: usare il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) per gestire le risorse nell'applicazione tramite [ruoli personalizzati](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). È possibile usare il controllo degli accessi in base al ruolo per limitare le operazioni consentite a DevOps su ogni livello. Quando si concedono autorizzazioni, usare il [principio dei privilegi minimi necessari](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Registrare tutte le operazioni amministrative ed eseguire controlli regolari per assicurare che eventuali modifiche alla configurazione siano state pianificate.

**Accesso a Internet**: questa architettura di riferimento usa il [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) come gateway Internet e servizio di bilanciamento del carico. È consigliabile che alcuni clienti prendano in considerazione anche l'uso di appliance di rete virtuali di terze parti per livelli aggiuntivi di sicurezza di rete come alternativa al [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Centro sicurezza di Azure**: il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornisce un punto di vista centralizzato dello stato della sicurezza delle risorse nella sottoscrizione e indicazioni che aiutano a evitare di compromettere le risorse. Può essere usato anche per abilitare criteri più granulari. È ad esempio possibile applicare i criteri a gruppi di risorse specifici, consentendo all'azienda di personalizzare il proprio approccio al rischio. È consigliabile che i clienti abilitino il Centro sicurezza di Azure nella propria sottoscrizione di Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentazione relativa alla conformità ai principi per la sicurezza cloud NCSC

Il Crown Commercial Service, un'agenzia che si impegna per migliorare le attività commerciali e di approvvigionamento da parte del governo, ha rinnovato la classificazione dei servizi cloud aziendali Microsoft specifici su G-Cloud v6, coprendo tutte le offerte Microsoft per il livello OFFICIAL. Per informazioni dettagliate su Azure e G-Cloud, vedere il [riepilogo della valutazione della sicurezza UK G-Cloud per Azure](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Questo progetto è conforme ai 14 principi per la sicurezza del cloud documentati nei [principi per la sicurezza del cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) di NCSC per contribuire a creare un ambiente che supporta carichi di lavoro classificati come UK-OFFICIAL.

La [matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm) (cartella di lavoro di Excel) elenca tutti i 14 principi per la sicurezza cloud e indica per ogni principio o sottoparte di principio se l'implementazione del principio è a carico di Microsoft, del cliente o è condivisa tra entrambi.

La [matrice di implementazione dei principi](https://aka.ms/ukwebappblueprintpim) (cartella di lavoro di Excel) elenca tutti i 14 principi per la sicurezza cloud e indica per ogni principio o sottoparte di principio per cui è designata una responsabilità del cliente nella matrice di responsabilità dei clienti 1) se l'automazione del progetto implementa il principio e 2) una descrizione del modo in cui l'implementazione risulta conforme ai requisiti del principio. Queste informazioni sono disponibili anche [qui](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Cloud Security Alliance (CSA) ha inoltre pubblicato la matrice di controllo cloud per supportare i clienti nella valutazione dei provider cloud e per identificare le domande per cui sono necessarie risposte prima del passaggio ai servizi cloud. Microsoft Azure ha a sua volta risposto al questionario CSA CAIQ ([CSA Consensus Assessment Initiative Questionnaire](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), che illustra il modo in cui Microsoft gestisce i principi suggeriti.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Sono disponibili due metodi che gli utenti della distribuzione possono usare per distribuire questa automazione del progetto. Il primo metodo usa uno script di PowerShell, mentre il secondo metodo usa il portale di Azure per distribuire l'architettura di riferimento. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
