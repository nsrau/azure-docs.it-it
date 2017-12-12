---
title: Considerazioni sulla pianificazione per Azure Stack integrati sistemi | Documenti Microsoft
description: "Informazioni sulle operazioni per l'ora di pianificare e preparare per lo Stack di Azure a più nodi."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: b39b3b8f73f9deef48173c712d7966eb13c6ecc3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considerazioni sulla pianificazione per Azure Stack integrati sistemi

*Si applica a: Azure Stack integrate di sistemi*

Se si è interessati in un sistema integrato dello Stack di Azure, è opportuno conoscere alcune delle principali considerazioni sulla pianificazione per la distribuzione e sulle modalità di integrazione di sistema nel Data Center. In questo argomento fornisce una panoramica generale di queste considerazioni.

Se si decide di acquistare un sistema integrato, il fornitore dell'hardware (OEM) original equipment manufacturer consente una Guida per la maggior parte del processo di pianificazione in modo più dettagliato. Eseguono anche la distribuzione effettiva.

## <a name="management-considerations"></a>Considerazioni sulla gestione

Stack di Azure è un sistema bloccato, in cui l'infrastruttura è bloccato da delle autorizzazioni e prospettiva di rete. Gli elenchi di controllo di accesso (ACL) di rete vengono applicati per bloccare tutto il traffico in ingresso non autorizzato e a tutte le comunicazioni non necessarie tra i componenti dell'infrastruttura. Questo rende difficile per gli utenti non autorizzati di accedere al sistema.

Per gestione quotidiana e le operazioni, non è disponibile alcun accesso amministratore senza restrizioni all'infrastruttura. Gli operatori di Azure Stack devono gestire il sistema tramite il portale dell'amministratore o tramite Azure Resource Manager (tramite PowerShell o l'API REST). Non è possibile accedere al sistema tramite altri strumenti di gestione, ad esempio gestione di Hyper-V o gestione Cluster di Failover. Per proteggere il sistema, è Impossibile installare il software di terze parti (ad esempio, gli agenti) all'interno dei componenti dell'infrastruttura di Azure Stack. Interoperabilità con il software di gestione e sicurezza esterno avviene tramite PowerShell o l'API REST.

Quando un livello di accesso è necessaria per la risoluzione dei problemi non vengono risolti tramite i passaggi di mediazione avviso, è necessario collaborare con il supporto. Tramite il supporto, è disponibile un metodo per fornire l'accesso temporaneo amministratore completo al sistema per eseguire operazioni più avanzate. 

## <a name="deploy-connected-or-disconnected"></a>Distribuire connesso o disconnesso
 
È possibile scegliere di distribuire Azure Stack disconnessi o connessi a internet (e in Azure). Per ottenere il massimo vantaggio dallo Stack di Azure, inclusi gli scenari di ibrido tra Stack di Azure e Azure, si desidera distribuire connesso ad Azure. Nella tabella seguente consente di riepilogare le principali differenze tra modalità di distribuzione.

| Area | Modalità connessa | Modalità disconnessa |
| -------- | ------------- | ----------|
| Provider di identità | Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) | AD FS |
| Diffusione di Marketplace | Scaricare gli elementi direttamente da Azure Marketplace nel Marketplace nello Stack di Azure | Richiede la gestione manuale di marketplace nello Stack di Azure |
| Modello di licenze | Retribuzione come-si- o uso basati sulla capacità | Basato su capacità solo|
| Patch e aggiornamenti  | Scaricare i pacchetti di aggiornamento direttamente allo Stack di Azure | Richiede un supporto rimovibile e da un dispositivo connesso separato |
| | | |

È possibile modificare la modalità di distribuzione in un secondo momento senza ridistribuzione completa del sistema.

## <a name="identity-considerations"></a>Considerazioni sull'identità

### <a name="choose-identity-provider"></a>Scegliere il provider di identità

È necessario considerare quale provider di identità che si desidera utilizzare per la distribuzione dello Stack di Azure, Azure Active Directory o AD FS. Dopo la distribuzione senza ridistribuzione completa del sistema non è possibile passare i provider di identità.

La scelta di provider di identità non incide su macchine virtuali tenant, il sistema di identità e account che usano, se è possibile aggiungere un dominio di Active Directory e così via. È un aspetto separato.

**Motivi per considerare l'uso di Azure AD**

- Hai già gli investimenti esistenti in Azure Active Directory (ad esempio Azure o Office 365).
- Si desidera utilizzare la stessa identità cloud Azure e Azure Stack.
- Si desidera supportare scenari multi-tenancy, in cui è possibile ospitare diverse organizzazioni nella stessa istanza dello Stack di Azure.
- Si desidera utilizzare la gestione di directory basato su REST mediante Azure AD Graph per il provisioning degli utenti, gruppi, e così via. attraverso le API.

> [!NOTE]
> È possibile connettersi a una distribuzione di Azure Stack per un'istanza di Azure AD e un'istanza esistente di AD FS nello stesso momento. Se si distribuisce con Azure AD e si desidera utilizzare un'istanza di ADFS esistente, è possibile attuare la federazione locale istanza di ADFS con Azure AD.

**Motivi per considerare l'utilizzo di ADFS**

- Non esiste alcun o solo la connettività internet parziale.
- Esistono requisiti normativi/sovranità.
- Si desidera utilizzare il proprio sistema di identità (ad esempio Active Directory aziendale) per gli account utente e l'operatore. A tale scopo, è possibile attuare la federazione con ADFS istanza esistente (in Windows Server 2012, 2012 R2 o 2016) supportato da Active Directory.
- Non si dispone di alcun investimento Azure esistente e non si desidera usare Azure AD.

> [!NOTE]
> Si può creare una federazione di Azure Stack solo con un'altra istanza di AD FS supportato da Active Directory. Altri provider di identità non sono supportati. Se si dispone di altri provider di identità che si desidera utilizzare con lo Stack di Azure, provare a utilizzare la distribuzione di Azure basato su Active Directory.

### <a name="ad-fs-and-graph-integration"></a>Integrazione di AD FS e grafico

Se si sceglie di distribuire Azure Stack con ADFS come provider di identità, è necessario integrare l'istanza di AD FS nello Stack di Azure con un'istanza di AD FS esistente mediante una relazione di trust federativa. Questo consente alle identità in una foresta di Active Directory esistente per l'autenticazione con le risorse nello Stack di Azure.

È inoltre possibile integrare il servizio grafico nello Stack di Azure con Active Directory esistente. Ciò consente di gestire in base al ruolo controllo di accesso (RBAC) nello Stack di Azure. Quando viene delegata l'accesso a una risorsa, il componente grafico Cerca l'account utente nella foresta Active Directory esistente tramite il protocollo LDAP.

Il diagramma seguente mostra integrato AD FS e grafico il flusso del traffico.
![Diagramma di flusso del traffico di ADFS e di grafico](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modello di licenze

È necessario decidere quale modello di licenze che si desidera utilizzare. Per una distribuzione connessa, è possibile scegliere di retribuzione-come-si-utilizza o basato su capacità di licenze. Retribuzione come--uso richiede una connessione in Azure per l'utilizzo di report, quindi viene fatturato tramite Azure commerce. Licenze basate solo su capacità è supportata se si distribuisce disconnesso da internet. Per ulteriori informazioni sui modelli di gestione delle licenze, vedere [assemblaggio e prezzi di Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisioni di denominazione

È necessario preoccuparsi di come si desidera pianificare lo Stack di Azure spazio dei nomi, in particolare il nome dell'area e il nome di dominio esterno. Il nome di dominio completo (FQDN) della distribuzione di Azure Stack per gli endpoint pubblico è la combinazione di questi due nomi, &lt; *area*&gt;&lt;*FQDN_esterno*  &gt;, ad esempio, *east.cloud.fabrikam.com*. In questo esempio, i portali di Stack Azure sarà disponibili nei seguenti URL:

- https://Portal.East.cloud.fabrikam.com
- https://adminportal.East.cloud.fabrikam.com

Nella tabella seguente sono riepilogate queste decisioni di nomi di dominio.

| Nome | Descrizione | 
| -------- | ------------- | 
|Nome dell'area | Il nome dell'area dello Stack di Azure prima. Questo nome viene utilizzato come parte del nome FQDN per i pubblici indirizzi IP virtuali (VIP) che gestisce dello Stack di Azure. In genere, il nome dell'area sarebbe un identificatore di posizione fisica, ad esempio un'ubicazione del Data Center. | 
| Nome di dominio esterno | Il nome della zona sistema DNS (Domain Name) per gli endpoint con gli indirizzi VIP esterna. Utilizzata nel FQDN per i VIP pubblici. | 
| Nome di dominio (interna) privato | Il nome del dominio (e zona DNS interno) creato nello Stack di Azure per la gestione dell'infrastruttura. 
| | |

## <a name="certificate-requirements"></a>Requisiti dei certificati

Per la distribuzione, è necessario fornire un certificato Secure Sockets Layer (SSL) per gli endpoint pubblici. In generale, i certificati sono i seguenti requisiti:

> [!IMPORTANT]
> Le informazioni sul certificato in questo articolo viene forniti solo come guida generale. Prima di acquistare i certificati per lo Stack di Azure, lavorare con i partner hardware OEM. Utenti forniranno più dettagliato i requisiti e le indicazioni di certificato.

- È possibile utilizzare un certificato con caratteri jolly singolo o è possibile utilizzare un set di certificati dedicati e utilizzare i caratteri jolly solo per gli endpoint, come archiviazione e l'insieme di credenziali chiave.
- I certificati devono essere emesso da un'autorità di certificazione pubblica (CA) o da una CA gestita dal cliente.
 
La tabella seguente illustra i servizi e il numero di endpoint pubblici che richiedono certificati per la distribuzione iniziale dello Stack di Azure. 

| Utilizzo | Endpoint 
| -------- | ------------- | 
| Gestione risorse di Azure (amministratore) | adminmanagment. [region]. [external_domain] |
| Gestione risorse di Azure (utente) | gestione. [region]. [external_domain] |
| Portale (amministratore) | adminportal. [region]. [external_domain] |
| Portale (utente) | portale. [region]. [external_domain] |
| Chiave dell'insieme di credenziali (utente) | &#42;. insieme di credenziali. [region]. [external_domain] |
| Chiave dell'insieme di credenziali (amministratore) | &#42;. adminvault. [region]. [external_domain] |
| Archiviazione | &#42;. BLOB. [region]. [external_domain]<br>&#42;. tavolo. [region]. [external_domain]<br>&#42;. coda. [region]. [external_domain]  |
| Grafico * * | grafico. [region]. [external_domain] |
| AD FS * * | ADFS. [region]. [external_domain] |
| | |

* * I certificati per gli endpoint ADFS e di grafico sono necessari solo per le distribuzioni di AD FS.

Se si desidera utilizzare un certificato con caratteri jolly singolo, è necessario un totale di sei Subject Alternative Names (SAN) per la distribuzione iniziale dello Stack di Azure. Questi SAN sono: 

- &#42;. [region]. [external_domain]
- &#42;. insieme di credenziali. [region]. [external_domain]
- &#42;. adminvault. [region]. [external_domain]
- &#42;. BLOB. [region]. [external_domain]
- &#42;. tavolo. [region]. [external_domain] 
- &#42;. coda. [region]. [external_domain]

## <a name="time-synchronization"></a>Sincronizzazione degli orari

È necessario sincronizzare i server di Azure Stack con un server ora esterna che è possibile risolvere tramite indirizzo IP. Un server nella rete aziendale è necessario per una distribuzione disconnessa.

## <a name="network-connectivity"></a>Connettività di rete

### <a name="dns-integration"></a>Integrazione di DNS

Per risolvere i nomi DNS esterni dallo Stack di Azure (ad esempio, www.bing.com), è necessario fornire i server DNS che Azure Stack può utilizzare per inoltrare le richieste DNS per il quale non è autorevole dello Stack di Azure. Come input di distribuzione, è necessario specificare almeno due server da utilizzare come server d'inoltro DNS per la tolleranza di errore.

Per risolvere i nomi DNS di endpoint di Azure Stack dallo Stack all'esterno di Azure (ad esempio, dalla foresta azienda), è necessario integrare il server DNS che ospitano la zona DNS esterna per lo Stack di Azure con i server DNS che ospitano la zona padre che si desidera utilizzare. Ciò richiede la risoluzione dei nomi DNS tra Azure Stack e zone DNS esistenti nel Data Center. A tale scopo, si userà metodi, ad esempio la delega di inoltro o all'area condizionale. È consigliabile se si ha controllo diretto su server DNS che ospitano la zona padre allo spazio dei nomi DNS esterni Azure Stack di inoltro condizionale. (Se la zona DNS di Azure Stack esterna vengono visualizzate come un dominio figlio del nome di dominio aziendale (ad esempio, azurestack.contoso.com e contoso.com), è necessario utilizzare la delega delle zone invece.

### <a name="network-infrastructure"></a>Infrastruttura di rete

L'infrastruttura di rete per lo Stack di Azure è costituita da più reti logiche configurate nei commutatori. Il diagramma seguente mostra le reti logiche e come integrarlo di top-of-rack (TOR), baseboard management controller (BMC) e il bordo switch (rete cliente).

![Connessioni di diagramma e il commutatore di rete logica](media/azure-stack-planning-considerations/NetworkDiagram.png)

La tabella seguente illustra le reti logiche e associare gli intervalli di subnet IPv4 che è necessario pianificare.

| Rete logica | Descrizione | Dimensione | 
| -------- | ------------- | ------------ | 
| VIP pubblico | Indirizzi IP pubblici per un piccolo set di servizi di Azure Stack, con il resto venga utilizzato dalle macchine virtuali tenant. L'infrastruttura di Azure Stack utilizza 32 indirizzi da questa rete. Se si prevede di utilizzare il provider di risorse SQL e di servizio App, Usa più di 7. | / 26 (62 host) - /22 (1022 host)<br><br>Consigliato = /24 (254 host) | 
| Infrastruttura switch | Gli indirizzi IP Point to Point a scopo di routing, dedicato passare le interfacce di gestione e gli indirizzi di loopback assegnati al commutatore. | /26 | 
| Infrastruttura | Utilizzato per i componenti interni dello Stack di Azure per comunicare. | /24 |
| Privato | Utilizzato per la rete di archiviazione e gli indirizzi VIP privato. | /24 | 
| BMC | Utilizzato per comunicare con il BMC negli host fisici. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Uplink alle opzioni di bordo

È necessario un uplink configurato per le opzioni di bordo nel Data Center. È possibile indirizzare il traffico di livello 3 dai commutatori top-of-rack (TOR) che fanno parte di un sistema integrato dello Stack di Azure utilizzando uno dei metodi seguenti:

- Border Gateway Protocol (BGP) 
- routing statico

È consigliabile BGP perché consente l'aggiornamento automatico di route che vengono pubblicati per il Software carico bilanciamento del carico Multiplexer (MUX SLB) a reti esterne. Questo è importante se si aggiungono gli intervalli di indirizzi IP pubblici dopo la distribuzione. In caso di peer di BGP TOR passa all'opzione di aggregazione che sono connessi i commutatori TOR, gli intervalli di indirizzi IP pubblici aggiungere di post-distribuzione vengono annunciati automaticamente all'opzione di aggregazione senza alcun intervento manuale. Se si utilizza il routing statico, è necessario aggiornare manualmente le route per i nuovi intervalli di valori ogni volta che si aggiunge un blocco di indirizzi IP pubblico.

### <a name="proxy-server"></a>Server proxy

Stack di Azure supporta solo i server proxy trasparente. Un proxy trasparente intercetta le richieste a livello di rete senza richiedere alcuna configurazione speciale client.

### <a name="publish-azure-stack-services"></a>La pubblicazione dei servizi Azure Stack

È necessario rendere disponibili i servizi di Azure Stack agli utenti dallo Stack all'esterno di Azure. Stack di Azure consente di impostare vari endpoint per i ruoli di infrastruttura. Questi endpoint vengono assegnati indirizzi IP virtuali dal pool di indirizzi IP pubblici. Una voce DNS viene creata per ogni endpoint nella zona DNS esterna, che è stata specificata in fase di distribuzione. Ad esempio, il portale per gli utenti viene assegnato alla voce host DNS di "portale. <*area*>. <*FQDN_esterno*>." 

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

![Scenario di ExpressRoute di diagramma che mostra single-tenant](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

Il diagramma seguente mostra ExpressRoute per uno scenario di multi-tenant.

![Scenario di ExpressRoute di diagramma che mostra multi-tenant](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

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
