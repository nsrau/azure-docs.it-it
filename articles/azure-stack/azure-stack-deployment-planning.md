---
title: Considerazioni sulla pianificazione per Azure Stack integrati sistemi | Documenti Microsoft
description: "Informazioni sulle operazioni per l'ora di pianificare e preparare per lo Stack di Azure a più nodi."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6ba6bed8321e1ffde8bc8959443682725da36827
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considerazioni sulla pianificazione per Azure Stack integrati sistemi
Se si è interessati in un sistema integrato dello Stack di Azure, è necessario comprendere alcune delle principali considerazioni sulla pianificazione per la distribuzione e sulle modalità di integrazione di sistema nel Data Center. In questo articolo fornisce una panoramica generale di queste considerazioni consentono di prendere decisioni importanti infrastruttura per il sistema a più nodi di Azure Stack. La comprensione di queste considerazioni è utile quando si distribuiscono dello Stack di Azure al Data Center funziona con il fornitore dell'hardware OEM.  

> [!NOTE]
> Sistemi a più nodi di Azure Stack possono solo essere acquistati da fornitori di hardware non autorizzato. 

Per distribuire Azure Stack esistono una serie di decisioni che è necessario apportare integrare correttamente Azure Stack con l'ambiente. È necessario fornire queste informazioni al provider di soluzioni durante il processo di pianificazione ed essere pronto per il fornitore dell'hardware prima di avvia la distribuzione semplificare il processo di passare rapidamente e senza problemi.

Le informazioni necessarie intervalli in rete, sicurezza e informazioni di identità con molti importanti decisioni che potrebbero richiedere informazioni da molte aree diverse e responsabili delle decisioni. Di conseguenza, potrebbe essere necessario inserire utenti da più team all'interno dell'organizzazione per assicurarsi di disporre di tutte le informazioni necessarie pronte prima di inizia la distribuzione. Questo consente di comunicare con il fornitore dell'hardware durante la raccolta di queste informazioni, come potrebbero avere consigli utili per prendere le decisioni.

Durante la ricerca e raccogliere le informazioni necessarie, potrebbe essere necessario apportare alcune modifiche di configurazione della pre-distribuzione nell'ambiente di rete. Ad esempio riservando spazi degli indirizzi IP per la soluzione di Stack di Azure, configurare i router, commutatori e i firewall per preparare per la connettività per le nuove opzioni della soluzione Azure Stack. Assicurarsi di avere l'esperto di area di interesse inline fino a utili per la pianificazione.

## <a name="management-considerations"></a>Considerazioni sulla gestione
Stack di Azure è un sistema bloccato, in cui l'infrastruttura è bloccato da delle autorizzazioni e prospettiva di rete. Gli elenchi di controllo di accesso (ACL) di rete vengono applicati per bloccare tutto il traffico in ingresso non autorizzato e a tutte le comunicazioni non necessarie tra i componenti dell'infrastruttura. Questo rende difficile per gli utenti non autorizzati di accedere al sistema.

Per gestione quotidiana e le operazioni, non è disponibile alcun accesso amministratore senza restrizioni all'infrastruttura. Gli operatori di Azure Stack devono gestire il sistema tramite il portale dell'amministratore o tramite Azure Resource Manager (tramite PowerShell o l'API REST). Non è possibile accedere al sistema tramite altri strumenti di gestione, ad esempio gestione di Hyper-V o gestione Cluster di Failover. Per proteggere il sistema, è Impossibile installare il software di terze parti (ad esempio, gli agenti) all'interno dei componenti dell'infrastruttura di Azure Stack. Interoperabilità con il software di gestione e sicurezza esterno avviene tramite PowerShell o l'API REST.

Quando un livello di accesso è necessaria per la risoluzione dei problemi non vengono risolti tramite i passaggi di mediazione avviso, è necessario collaborare con il supporto. Tramite il supporto, è disponibile un metodo per fornire l'accesso temporaneo amministratore completo al sistema per eseguire operazioni più avanzate. 

## <a name="identity-considerations"></a>Considerazioni sull'identità

### <a name="choose-identity-provider"></a>Scegliere il provider di identità
È necessario considerare quale provider di identità che si desidera utilizzare per la distribuzione dello Stack di Azure, Azure Active Directory o AD FS. Dopo la distribuzione senza ridistribuzione completa del sistema non è possibile passare i provider di identità.

La scelta di provider di identità non incide su macchine virtuali tenant, il sistema di identità e account che usano, se è possibile aggiungere un dominio di Active Directory e così via. È un aspetto separato.

Per ulteriori informazioni sulla scelta di un provider di identità in informazioni di [decisioni di distribuzione per l'articolo sistemi integrati Azure Stack](.\azure-stack-deployment-decisions.md).

### <a name="ad-fs-and-graph-integration"></a>Integrazione di AD FS e grafico
Se si sceglie di distribuire Azure Stack con ADFS come provider di identità, è necessario integrare l'istanza di AD FS nello Stack di Azure con un'istanza di AD FS esistente mediante una relazione di trust federativa. Questo consente alle identità in una foresta di Active Directory esistente per l'autenticazione con le risorse nello Stack di Azure.

È inoltre possibile integrare il servizio grafico nello Stack di Azure con Active Directory esistente. Ciò consente di gestire in base al ruolo controllo di accesso (RBAC) nello Stack di Azure. Quando viene delegata l'accesso a una risorsa, il componente grafico Cerca l'account utente nella foresta Active Directory esistente tramite il protocollo LDAP.

Il diagramma seguente mostra integrato AD FS e grafico il flusso del traffico.
![Diagramma di flusso del traffico di ADFS e di grafico](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modello di licenze

È necessario decidere quale modello di licenze che si desidera utilizzare. Per una distribuzione connessa, è possibile scegliere di retribuzione-come-si-utilizza o basato su capacità di licenze. Retribuzione come--uso richiede una connessione in Azure per l'utilizzo di report, quindi viene fatturato tramite Azure commerce. Licenze basate solo su capacità è supportata se si distribuisce disconnesso da internet. Per ulteriori informazioni sui modelli di gestione delle licenze, vedere [assemblaggio e prezzi di Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisioni di denominazione

È necessario preoccuparsi di come si desidera pianificare lo Stack di Azure spazio dei nomi, in particolare il nome dell'area e il nome di dominio esterno. Il nome di dominio completo (FQDN) della distribuzione di Azure Stack per gli endpoint pubblico è la combinazione di questi due nomi, &lt; *area*&gt;&lt;*FQDN_esterno*  &gt;, ad esempio, *east.cloud.fabrikam.com*. In questo esempio, i portali di Stack Azure sarà disponibili nei seguenti URL:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

Nella tabella seguente sono riepilogate queste decisioni di nomi di dominio.

| NOME | DESCRIZIONE | 
| -------- | ------------- | 
|Nome dell'area | Il nome dell'area dello Stack di Azure prima. Questo nome viene utilizzato come parte del nome FQDN per i pubblici indirizzi IP virtuali (VIP) che gestisce dello Stack di Azure. In genere, il nome dell'area sarebbe un identificatore di posizione fisica, ad esempio un'ubicazione del Data Center. | 
| Nome di dominio esterno | Il nome della zona sistema DNS (Domain Name) per gli endpoint con gli indirizzi VIP esterna. Utilizzata nel FQDN per i VIP pubblici. | 
| Nome di dominio (interna) privato | Il nome del dominio (e zona DNS interno) creato nello Stack di Azure per la gestione dell'infrastruttura. 
| | |

## <a name="certificate-requirements"></a>Requisiti dei certificati

Per la distribuzione, è necessario fornire un certificato Secure Sockets Layer (SSL) per gli endpoint pubblici. In generale, i certificati sono i seguenti requisiti:

- È possibile utilizzare un certificato con caratteri jolly singolo o è possibile utilizzare un set di certificati dedicati e utilizzare i caratteri jolly solo per gli endpoint, come archiviazione e l'insieme di credenziali chiave.
- I certificati possono essere emessi da un'autorità di certificazione pubblica (CA) o da una CA gestita dal cliente.

Per ulteriori informazioni su quali infrastruttura a chiave pubblica per distribuire Azure Stack e come ottenerle, vedere, sono necessari certificati [requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Le informazioni del certificato PKI fornite devono essere utilizzate come guida generale. Prima di acquistare i certificati PKI per lo Stack di Azure, lavorare con i partner hardware OEM. Utenti forniranno più dettagliato i requisiti e le indicazioni di certificato.



## <a name="time-synchronization"></a>Sincronizzazione degli orari
È necessario scegliere un'ora specifica server con viene utilizzato per sincronizzare dello Stack di Azure.  Symbolization tempo è fondamentale dello Stack di Azure e i relativi ruoli di infrastruttura, perché è usato per generare il ticket Kerberos che vengono utilizzati per autenticare i servizi interni tra loro.

È necessario specificare che un indirizzo IP per server di sincronizzazione, anche se la maggior parte dei componenti dell'infrastruttura possono risolvere un URL, alcuni può supportare solo gli indirizzi IP. Se si sono usando l'opzione di distribuzione disconnesso, è necessario specificare un server nella rete aziendale a cui si è assicurarsi che sia raggiungibile dalla rete dell'infrastruttura nello Stack di Azure.


## <a name="network-connectivity"></a>Connettività di rete
In questa sezione fornisce informazioni sull'infrastruttura di rete dello Stack di Azure consente di effettuare importanti decisioni relative alla migliore integrano dello Stack di Azure nell'ambiente di rete esistente. 

> [!NOTE]
> Per risolvere i nomi DNS esterni dallo Stack di Azure (ad esempio, www.bing.com), è necessario fornire i server DNS che Azure Stack può utilizzare per inoltrare le richieste DNS per il quale non è autorevole dello Stack di Azure. Per ulteriori informazioni su Azure Stack DNS requisiti, vedere [integrazione con Data Center di Azure Stack - DNS](azure-stack-integrate-dns.md).

### <a name="physical-network-design"></a>Progettazione di rete fisica
La soluzione Azure Stack richiede un'infrastruttura fisica resiliente e a disponibilità elevata per supportare il funzionamento e i servizi. Di seguito è riportato un diagramma della progettazione consigliato:

![Progettazione di rete consigliato Azure Stack](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>Reti logiche
Le reti logiche rappresentano un'astrazione dell'infrastruttura di rete fisica sottostante. Vengono utilizzati per organizzare e semplificare le assegnazioni di rete per host, macchine virtuali e servizi. Come parte della creazione di reti logiche, siti di rete vengono creati per definire le reti locali virtuali (VLAN), subnet IP e coppie subnet IP/VLAN associate alla rete logica in ogni posizione fisica.
L'infrastruttura di rete per lo Stack di Azure Usa le seguenti reti logiche configurate nei commutatori:

### <a name="network-infrastructure"></a>Infrastruttura di rete
L'infrastruttura di rete per lo Stack di Azure è costituita da più reti logiche configurate nei commutatori. Il diagramma seguente mostra le reti logiche e come integrarlo di top-of-rack (TOR), baseboard management controller (BMC) e il bordo switch (rete cliente).

![Connessioni di diagramma e il commutatore di rete logica](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>Rete BMC
Questa rete è dedicata per tutti i baseboard management controller (noto anche come servizio processori, ad esempio, iDRAC, iLO, iBMC, ecc.) la connessione alla rete di gestione. Se presente, l'host del ciclo di vita di hardware (HLH) si trovino su questa rete e può costituire software specifico OEM per la manutenzione dell'hardware e/o di monitoraggio. 

#### <a name="private-network"></a>Rete privata
Questo /24 254 host della rete IP) (è privato per l'area dello Stack di Azure (non superare i dispositivi a sensore bordo dell'area dello Stack di Azure) ed è suddivisa in due subnet:

- **Rete di archiviazione**. Migrazione in tempo reale un /25 126 host della rete IP) (utilizzata per supportare l'utilizzo di traffico di archiviazione diretta di spazi e Server Message Block (SMB) e la macchina virtuale. 
- **Rete IP virtuale interna**. A/25 dedicata all'interno solo per gli indirizzi VIP per il bilanciamento del carico software di rete.

#### <a name="azure-stack-infrastructure-network"></a>Rete infrastruttura di Azure Stack
Questo/24 rete dedicato per i componenti interni dello Stack di Azure in modo che possano comunicare e scambiare dati tra loro. Questa subnet richiede gli indirizzi IP instradabili, ma è privata per la soluzione tramite elenchi di controllo di accesso (ACL), non è previsto l'indirizzamento oltre le opzioni di bordo, ad eccezione di un intervallo molto piccolo uguali a quelle di un /27 rete utilizzata da alcune di queste servizi quando richiedono l'accesso alle risorse esterne e/o internet. 

#### <a name="public-infrastructure-network"></a>Rete pubblica infrastruttura
Questo/27 rete è molto piccolo compresi tra la subnet dell'infrastruttura Azure Stack indicato in precedenza, non richiede indirizzi IP pubblici, ma richiede l'accesso a internet tramite un dispositivo NAT o un Proxy trasparente. Questa rete verrà allocata per l'emergenza ripristino Console di sistema (ERCS), la VM ERCS richiede l'accesso a internet durante la registrazione in Azure e deve essere instradabile alla rete di gestione per la risoluzione dei problemi.

#### <a name="public-vip-network"></a>Rete pubblica di indirizzi VIP
Rete pubblica VIP viene assegnata al controller di rete nello Stack di Azure. Non è una rete logica del commutatore. Il SLB utilizza il pool di indirizzi e assegna/32 di reti per carichi di lavoro tenant. Nella tabella di routing di commutatore, questi indirizzi IP di 32 vengono annunciate come non disponibili tramite il protocollo BGP. Questa rete contiene gli indirizzi IP pubblici o esterni accessibili. L'infrastruttura di Azure Stack utilizza almeno 8 indirizzi da questa rete VIP pubblico, mentre il resto viene utilizzato da macchine virtuali tenant. Le dimensioni della subnet di rete possono variare da un minimo di /26 (64 host) a un massimo di /22 (1022 host), è consigliabile pianificare un /24 rete.

#### <a name="switch-infrastructure-network"></a>Rete infrastruttura commutatore
Questo/26 rete è la subnet contenente subnet di /30 IP instradabile Point to Point (IP host 2) e i loopback di cui dedicati/32 subnet per in banda passare gestione e l'ID del router BGP. Questo intervallo di indirizzi IP deve essere instradabile dall'esterno della soluzione dello Stack di Azure al Data Center, possono essere indirizzi IP pubblici o privati.

#### <a name="switch-management-network"></a>Rete di gestione commutatore
Questo /29 (host 6 indirizzi IP) rete dedicata per le porte di gestione delle opzioni di connessione. Consente l'accesso fuori banda per la distribuzione, gestione e risoluzione dei problemi. Viene calcolato dalla rete dell'infrastruttura commutatore indicata in precedenza.

### <a name="transparent-proxy"></a>PROXY TRASPARENTI
La soluzione di Stack di Azure non supporta proxy web normale. Se il Data Center richiede tutto il traffico di usare un proxy, è necessario configurare un proxy trasparente per l'elaborazione di tutto il traffico dal rack da gestire in base ai criteri, separare il traffico tra le aree della rete. Un proxy trasparente (noto anche come un'intercettazione inline o proxy forzato) intercetta le normali comunicazioni nel livello rete senza richiedere alcuna configurazione speciale client. I client non devono essere consapevoli dell'esistenza del proxy.

### <a name="publish-azure-stack-services"></a>La pubblicazione dei servizi Azure Stack

È necessario rendere disponibili i servizi di Azure Stack agli utenti dallo Stack all'esterno di Azure. Stack di Azure consente di impostare vari endpoint per i ruoli di infrastruttura. Questi endpoint vengono assegnati indirizzi IP virtuali dal pool di indirizzi IP pubblici. Una voce DNS viene creata per ogni endpoint nella zona DNS esterna, che è stata specificata in fase di distribuzione. Ad esempio, il portale per gli utenti viene assegnato alla voce host DNS del portale.  *&lt;area >.&lt; nome di dominio completo >*.

#### <a name="ports-and-urls"></a>Porte e gli URL

Per rendere i servizi di Azure Stack (ad esempio i portali, Gestione risorse di Azure, DNS e così via) disponibile per le reti esterne, è necessario consentire il traffico in ingresso con questi endpoint per gli URL, porte e protocolli specifici.
 
In una distribuzione in cui un uplink proxy trasparente a un server proxy tradizionali, è necessario consentire determinate porte e gli URL per le comunicazioni in uscita. Tra le porte e gli URL per identità, diffusione di marketplace, patch e aggiornamento, registrazione e dati di utilizzo.

Per altre informazioni, vedere:
- [Protocolli e porte in ingresso](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Porte in uscita e gli URL](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Stack di Azure di connettersi a Azure

Per gli scenari di cloud ibrido, è necessario pianificare la modalità di connessione dello Stack di Azure ad Azure. Esistono due metodi supportati per la connessione di reti virtuali in Azure Stack alle reti virtuali in Azure: 
- **Site-to-site**. Una connessione di rete privata virtuale (VPN) tramite IPsec (IKE v1 e IKE v2). Questo tipo di connessione richiede un dispositivo VPN o Routing e accesso remoto (RRAS). Per ulteriori informazioni sui gateway VPN in Azure, vedere [sul Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). La comunicazione su questo tunnel viene crittografata e protetto. Tuttavia, la larghezza di banda è limitata dalla velocità effettiva massima del tunnel (100-200 Mbps).
- **NAT in uscita**. Per impostazione predefinita, tutte le macchine virtuali nello Stack di Azure avrà la connettività a reti esterne tramite NAT in uscita. Ogni rete virtuale che viene creato nello Stack di Azure Ottiene un indirizzo IP pubblico assegnato. Se la macchina virtuale è stata direttamente assegnata un indirizzo IP pubblico o si trova dietro un bilanciamento del carico con un indirizzo IP pubblico, si otterrà l'accesso in uscita tramite NAT in uscita utilizzando l'indirizzo VIP della rete virtuale. Questo procedimento funziona solo per le comunicazioni che vengano avviata dalla macchina virtuale e destinata a reti esterne (internet o intranet). E non può essere utilizzato per comunicare con la macchina virtuale dall'esterno.

#### <a name="hybrid-connectivity-options"></a>Opzioni di connettività ibrida

Per la connettività ibrida, è importante considerare il tipo di distribuzione a cui si desidera offrire e in cui verrà distribuito. È necessario considerare se è necessario isolare il traffico di rete per ogni tenant e se è necessario una distribuzione intranet o internet.

- **Single-tenant Azure Stack**. Una distribuzione di Stack di Azure che esegue una ricerca, almeno da una prospettiva di rete, come se fosse un tenant. Possono esistere che molte sottoscrizioni del tenant, ma come qualsiasi servizio di rete intranet, tutto il traffico viene trasmessa le stesse reti. Il traffico di rete da una sottoscrizione viene trasmessa la stessa connessione di rete di un'altra sottoscrizione e non desidera essere isolati tramite un tunnel crittografato.

- **Stack di Azure multi-tenant**. Una distribuzione di Azure Stack in cui il traffico della sottoscrizione di ogni tenant associato per le reti esterne allo Stack di Azure deve essere isolato dal traffico di rete degli altri tenant.
 
- **Distribuzione Intranet**. Una distribuzione di Azure Stack che si trova in una rete intranet aziendale, in genere in spazio di indirizzi IP privato e protetti da firewall uno o più. Gli indirizzi IP pubblici non sono realmente pubblici, non possono essere instradati direttamente sulla rete internet pubblica.

- **Distribuzione in Internet**. Una distribuzione di Azure Stack che è connesso al pubblico internet e utilizza IP instradabile su internet pubblico indirizzi per l'intervallo di indirizzi VIP pubblico. La distribuzione ancora può trovarsi dietro un firewall, ma l'intervallo di indirizzi VIP pubblico sia raggiungibile direttamente da internet e Azure pubblica.
 
Nella tabella seguente sono riepilogati gli scenari di connettività ibrida con casi di utilizzo, i professionisti e gli svantaggi.

| Scenario | Metodo di connettività | Vantaggi | Svantaggi | Adatto per |
| -- | -- | --| -- | --|
| Single-tenant Azure Stack, distribuzione intranet | NAT in uscita | Migliore della larghezza di banda per il trasferimento più veloce. Semplice da implementare; Nessun gateway necessario. | Traffico non crittografato. Nessun isolamento o crittografia oltre TOR. | Distribuzioni aziendali in cui tutti i tenant sono ugualmente attendibili.<br><br>Organizzazioni che dispongono di un circuito ExpressRoute di Azure in Azure. |
| Stack di Azure multi-tenant, distribuzione intranet | Da sito a VPN | Il traffico proveniente da tenant rete virtuale di destinazione è protetto. | Larghezza di banda è limitata dal tunnel VPN da sito a sito.<br><br>Richiede un gateway nella rete virtuale e un dispositivo VPN della rete di destinazione. | Le distribuzioni aziendali in cui alcuni traffico del tenant devono essere protetto da altri tenant. |
| Single-tenant di Azure, Stack di distribuzione in internet | NAT in uscita | Migliore della larghezza di banda per il trasferimento più veloce. | Traffico non crittografato. Nessun isolamento o crittografia oltre TOR. | Scenari in cui il tenant Ottiene la distribuzione di Azure Stack e un circuito dedicato per l'ambiente dello Stack di Azure di hosting. Ad esempio, ExpressRoute e Multiprotocol Label cambio MPLS ().
| Stack di Azure multi-tenant, distribuzione in internet | Da sito a VPN | Il traffico proveniente da tenant rete virtuale di destinazione è protetto. | Larghezza di banda è limitata dal tunnel VPN da sito a sito.<br><br>Richiede un gateway nella rete virtuale e un dispositivo VPN della rete di destinazione. | Hosting di scenari in cui il provider desidera realizzare un cloud multi-tenant, in cui i tenant non attendibili tra loro e il traffico deve essere crittografato.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Utilizzo di ExpressRoute

È possibile connettersi dello Stack di Azure in Azure tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per scenari multi-tenant e single-tenant intranet. È necessario un circuito ExpressRoute di provisioning tramite [un provider di connettività](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Il diagramma seguente mostra ExpressRoute per uno scenario single-tenant (in cui "Connessione del cliente" è il circuito ExpressRoute).

![Scenario di ExpressRoute di diagramma che mostra single-tenant](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

Il diagramma seguente mostra ExpressRoute per uno scenario di multi-tenant.

![Scenario di ExpressRoute di diagramma che mostra multi-tenant](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitoraggio esterno
Per ottenere una singola visualizzazione di tutti gli avvisi dai dispositivi e la distribuzione di Azure Stack e integrare gli avvisi in IT servizio Gestione flussi di lavoro esistenti per la creazione di ticket, è possibile integrare Azure Stack con Data Center esterno soluzioni di monitoraggio.

Inclusi nella soluzione Azure Stack, l'host del ciclo di vita di hardware è un computer all'esterno dello Stack di Azure che esegue gli strumenti di gestione fornito dal fornitore OEM per hardware. È possibile utilizzare questi strumenti o altre soluzioni che integrano direttamente con le soluzioni di monitoraggio esistenti nel Data Center.

La tabella seguente riepiloga l'elenco delle opzioni disponibili.

| Area | Soluzione di monitoraggio esterno |
| -- | -- |
| Software di Azure Stack | - [Stack Azure Management Pack per Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios plug-in](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-Le chiamate API basato su REST | 
| Server fisici (BMC tramite IPMI) | -Management pack fornitore di operations Manager<br>-Soluzione fornita dal produttore di hardware OEM<br>-Fornitore di hardware Nagios plug-in | OEM (incluso) soluzione di monitoraggio supportati da partner | 
| Dispositivi di rete (SNMP) | -Individuazione dei dispositivi di rete operations Manager<br>-Soluzione fornita dal produttore di hardware OEM<br>-Switch Nagios plug-in |
| Il monitoraggio dello stato di sottoscrizione di tenant | - [System Center Management Pack per Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Tenere presenti i requisiti seguenti:
- La soluzione che è utilizzare deve essere senza agente. È possibile installare agenti di terze parti all'interno dei componenti dello Stack di Azure. 
- Se si desidera utilizzare System Center Operations Manager, richiede Operations Manager 2012 R2 o Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Backup e ripristino di emergenza

Pianificazione di backup e ripristino di emergenza consiste nel pianificare per entrambi Azure Stack infrastruttura sottostante che ospita macchine virtuali IaaS e PaaS servizi e per tenant applicazioni e dati. È necessario pianificare per questi separatamente.

### <a name="protect-infrastructure-components"></a>Proteggere i componenti dell'infrastruttura

Stack di Azure esegue il backup componenti dell'infrastruttura in una condivisione specificati.

- È necessario che una condivisione di file SMB esterna in un server basato su Windows file esistente o un dispositivo di terze parti.
- Utilizzare la stessa condivisione per il backup dei commutatori di rete e l'host del ciclo di vita di hardware. Il fornitore dell'hardware OEM fornirà indicazioni per il backup e ripristino di questi componenti in cui sono esterni allo Stack di Azure. Si è responsabile per l'esecuzione di flussi di lavoro di backup in base alle raccomandazioni del fornitore OEM.

Se si verifica una perdita irreversibile di dati, è possibile utilizzare il backup di infrastruttura per i dati di distribuzione, ad esempio distribuzione input e gli identificatori, gli account del servizio, certificato radice CA, risorse federative (nelle distribuzioni disconnesse), i piani, offerte, reseed le sottoscrizioni, le quote, le assegnazioni di ruolo e criteri RBAC e segreti insieme di credenziali chiave.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteggere le applicazioni di tenant in macchine virtuali IaaS

Stack di Azure non esegue il backup di dati e applicazioni tenant. È necessario pianificare per la protezione di ripristino di emergenza e backup a una destinazione esterna allo Stack di Azure. Protezione di tenant è un'attività basate su tenant. Per le macchine virtuali IaaS, i tenant possono usare le tecnologie di guest per proteggere le cartelle dei file, i dati dell'applicazione e lo stato del sistema. Tuttavia, come un'organizzazione o provider di servizi, è opportuno offrire una soluzione di backup e ripristino dello stesso Data Center o esternamente in un cloud.

Per eseguire il backup di macchine virtuali Linux o Windows IaaS, è necessario utilizzare i prodotti per il backup con accesso al sistema operativo guest per proteggere file, cartella, lo stato del sistema operativo e dati dell'applicazione. È possibile utilizzare Backup di Azure, System Center Data Center Protection Manager, o supportate di prodotti di terze parti.

Per replicare i dati in una posizione secondaria e orchestrare il failover dell'applicazione se si verifica un'emergenza, è possibile utilizzare Azure Site Recovery o prodotti di terze parti supportati. (Nella versione iniziale di sistemi integrati, Azure Site Recovery non supporterà il failback. Tuttavia, è possibile ottenere il failback tramite un processo manuale.) Inoltre, le applicazioni che supportano la replica nativa (ad esempio Microsoft SQL Server) possono replicare i dati in un'altra posizione in cui è in esecuzione l'applicazione.

> [!IMPORTANT]
> Nella versione iniziale di sistemi integrati, è necessario supportare le tecnologie di protezione che funzionano a livello di guest di una macchina virtuale IaaS. È possibile installare gli agenti nei server di infrastruttura sottostante.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui casi di utilizzo, acquisto, partner e fornitori di hardware OEM, vedere il [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e geografica disponibilità per lo Stack di Azure sistemi integrati, vedere il white paper: [dello Stack di Azure: un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
