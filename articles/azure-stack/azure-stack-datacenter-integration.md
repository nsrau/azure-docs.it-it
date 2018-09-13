---
title: I sistemi integrati di considerazioni relative all'integrazione di Data Center generale per Azure Stack | Microsoft Docs
description: Scopri cosa può fare per ora di pianificare e preparare l'integrazione di Data Center con Azure Stack con più nodi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 9e5a8cf59d4f1dc47495c5889f8ed4aae64f7ff7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720447"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Considerazioni relative all'integrazione di datacenter per i sistemi integrati di Azure Stack
Se è interessati a un sistema integrato Azure Stack, è necessario comprendere alcune delle principali considerazioni di pianificazione alla distribuzione e come il sistema si inserisce nel tuo Data Center. Questo articolo fornisce una panoramica generale di queste considerazioni che consentono di prendere decisioni importanti dell'infrastruttura per il sistema a più nodi di Azure Stack. Consente di comprendere queste considerazioni quando si lavora con il fornitore dell'hardware OEM quando si distribuisce Azure Stack nel tuo Data Center.  

> [!NOTE]
> Sistemi a più nodi di Azure Stack possono essere acquistati solo dai fornitori di hardware autorizzati. 

Per distribuire Azure Stack, è necessario fornire informazioni sulla pianificazione per il provider di soluzioni prima di passare rapidamente e senza problemi per il processo di avvio della distribuzione. Le informazioni necessarie gli intervalli in rete, sicurezza e le informazioni sull'identità con molte decisioni importanti che possono richiedere informazioni da molte aree differenti e i responsabili delle decisioni. Di conseguenza, potrebbe essere necessario eseguire il pull gli utenti da più team nell'organizzazione per assicurarsi di disporre di tutte le informazioni necessarie pronte prima dell'avvio della distribuzione. Consente di comunicare con il fornitore dell'hardware durante la raccolta di queste informazioni, perché potrebbero avere consigli utili per prendere le decisioni.

Durante la ricerca e raccogliere le informazioni necessarie, si potrebbe essere necessario apportare alcune modifiche di configurazione della pre-distribuzione nell'ambiente di rete. Ad esempio la prenotazione di spazi di indirizzi IP per la soluzione di Azure Stack, configurazione dei router, commutatori e i firewall per la preparazione per la connettività per le nuove opzioni di soluzione di Azure Stack. Assicurarsi di avere l'esperto di area soggetto inline fino a consentono la pianificazione della.

## <a name="capacity-planning-considerations"></a>Considerazioni sulla pianificazione della capacità
Durante la valutazione di una soluzione di Azure Stack per l'acquisizione, necessario effettuare le scelte di configurazione di hardware che hanno un impatto diretto sulla capacità complessiva della propria soluzione di Azure Stack. Ad esempio le scelte classiche di CPU, la densità di memoria, configurazione dell'archiviazione e scalabilità complessiva di soluzione (ad esempio, numero di server). A differenza di una soluzione di virtualizzazione tradizionale, non è applicabile l'aritmetica semplice di questi componenti per determinare la capacità utilizzabile. Il primo motivo è che Azure Stack è progettato per ospitare i componenti di infrastruttura o di gestione all'interno della soluzione stessa. Il secondo motivo è che alcune delle capacità della soluzione è riservato per il supporto di resilienza; l'aggiornamento del software della soluzione in modo da ridurre al minimo l'interruzione dei carichi di lavoro tenant. 

Il [foglio di calcolo di Azure Stack capacità planner](https://aka.ms/azstackcapacityplanner) aiuta a garantire informati decisioni rispetto alla pianificazione della capacità in due modi: entrambi il selezionando un'offerta di hardware e tentare di adattare una combinazione di risorse o definendo il carico di lavoro Azure Stack è destinato all'esecuzione per visualizzare l'hardware disponibile SKU che può supportare tale condizione. Infine, è destinato il foglio di calcolo come una guida utili per prendere decisioni relative alla pianificazione di Azure Stack e configurazione. 

Il foglio di calcolo non può essere usato come soluzione alternativa per il proprio analisi e l'analisi.  Microsoft non effettua alcuna dichiarazione o garanzia, esplicita o implicita, relativamente alle informazioni fornite all'interno del foglio di calcolo.



## <a name="management-considerations"></a>Considerazioni sulla gestione
Azure Stack è un sistema bloccato, in cui l'infrastruttura è bloccato entrambi gli elementi da delle autorizzazioni e prospettiva di rete. Elenchi di controllo di accesso di rete (ACL) vengono applicati per bloccare tutto il traffico in ingresso non autorizzato e tutte le comunicazioni non necessarie tra i componenti dell'infrastruttura. Questo rende difficile per gli utenti non autorizzati di accedere al sistema.

Per la gestione quotidiana e operazioni, non è possibile accedere senza restrizioni di amministratore per l'infrastruttura. Operatori di Azure Stack è necessario gestire il sistema tramite il portale dell'amministratore o tramite Azure Resource Manager (tramite PowerShell o l'API REST). Non è possibile accedere al sistema tramite altri strumenti di gestione, ad esempio Hyper-V Manager o gestione Cluster di Failover. Per proteggere il sistema, non può essere installato software di terze parti (ad esempio agenti) all'interno dei componenti dell'infrastruttura di Azure Stack. Si verifica l'interoperabilità con il software di gestione e sicurezza esterno tramite PowerShell o l'API REST.

Quando un livello superiore di accesso è necessaria per la risoluzione dei problemi che non vengono risolti tramite i passaggi di mediazione avviso, è necessario collaborare con il supporto tecnico Microsoft. Grazie al supporto, c'è un metodo per fornire l'accesso amministratore completo temporanee al sistema per eseguire operazioni più avanzate. 

## <a name="identity-considerations"></a>Considerazioni sull'identità

### <a name="choose-identity-provider"></a>Scegliere il provider di identità
È necessario prendere in considerazione quali provider di identità da usare per la distribuzione di Azure Stack, Azure AD o AD FS. Dopo la distribuzione senza ridistribuzione completa del sistema non è possibile passare i provider di identità. Se non si è proprietari di account di Azure AD e si usa un account fornito da Provider di servizi Cloud e, se si decide di passare i provider e usare un Azure AD diverso dell'account, a questo punto sarà necessario contattare il provider di soluzioni per ridistribuire la soluzione f oppure i costi.



La scelta di provider di identità non ha alcun effetto su macchine virtuali tenant, il sistema di identità e degli account che usano, se possano essere aggiunte a un dominio di Active Directory e così via. Ciò è separato.

Altre informazioni sulla scelta di un provider di identità nel [articolo di modelli di Azure Stack i sistemi integrati connessione](.\azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integrazione di AD FS e un grafo
Se si sceglie di distribuire Azure Stack tramite AD FS come provider di identità, è necessario integrare l'istanza di AD FS in Azure Stack con un'istanza di AD FS esistente mediante una relazione di trust federativa. In questo modo le identità in una foresta di Active Directory esistente per l'autenticazione con le risorse in Azure Stack.

È anche possibile integrare il servizio Graph in Azure Stack con Active Directory esistente. Ciò consente di gestire Role-Based Access Control (RBAC) in Azure Stack. Quando l'accesso a una risorsa viene delegata, il componente grafico Cerca l'account utente nella foresta Active Directory esistente tramite il protocollo LDAP.

Il diagramma seguente mostra integrato il flusso del traffico AD FS e Graph.
![Diagramma che mostra il flusso del traffico AD FS e un grafo](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modello di licenza
È necessario decidere quale modello di licenza da usare. Le opzioni disponibili variano a seconda se si distribuisce Azure Stack connesso a internet:
- Per un [connessi distribuzione](azure-stack-connected-deployment.md), è possibile scegliere come si a pagamento o basati su capacità di gestione delle licenze. Come si-pagamento richiede una connessione ad Azure report relativi all'uso, quindi fatturazione tramite e-commerce di Azure. 
- Solo licenze basate su capacità è supportata se si [Distribuisci disconnesso](azure-stack-disconnected-deployment.md) da internet. 

Per altre informazioni sui modelli di gestione delle licenze, vedere [assemblaggio e prezzi di Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Decisioni di denominazione

È necessario pensare al modo in cui si desidera pianificare di Azure Stack dello spazio dei nomi, in particolare il nome dell'area e il nome di dominio esterno. Il nome di dominio completo esterno (FQDN) della distribuzione di Azure Stack per endpoint pubblico è la combinazione di questi due nomi: &lt; *regione*&gt;.&lt; *fqdn*&gt;. Ad esempio, *east.cloud.fabrikam.com*. In questo esempio, i portali di Azure Stack saranno disponibili agli URL seguenti:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Il nome dell'area che scelta per la distribuzione di Azure Stack deve essere univoco e verrà visualizzato l'indirizzo di portale. 

Nella tabella seguente sono riepilogate queste decisioni dei nomi di dominio.

| NOME | DESCRIZIONE | 
| -------- | ------------- | 
|Nome area | Nome della propria area di Azure Stack prima. Questo nome viene usato come parte del nome FQDN per i pubblici indirizzi IP virtuali (VIP) che consente di gestire Azure Stack. In genere, il nome dell'area potrebbe essere un identificatore di posizione fisica, ad esempio un'ubicazione del Data Center.<br><br>Il nome dell'area deve contenere solo lettere e numeri compresi tra 0 e 9. Senza caratteri speciali, ad esempio "-" o "#" e così via sono consentiti.| 
| Nome di dominio esterno | Il nome della zona sistema DNS (Domain Name) per gli endpoint con gli indirizzi VIP esterni. Usato nel nome di dominio completo per i VIP pubblici. | 
| Nome di dominio privato (interna) | Il nome di dominio (e zona DNS interno) creata in Azure Stack per la gestione dell'infrastruttura. 
| | |

## <a name="certificate-requirements"></a>Requisiti dei certificati

Per la distribuzione, è necessario fornire i certificati Secure Sockets Layer (SSL) per gli endpoint pubblici. A livello generale, i certificati sono i requisiti seguenti:

- È possibile usare un certificato con caratteri jolly singolo o è possibile usare un set di certificati dedicati e usare caratteri jolly solo per gli endpoint, ad esempio archiviazione e di Key Vault.
- I certificati possono essere rilasciati da un'autorità pubblica certificato attendibile (CA) o un'autorità di certificazione gestite dal cliente.

Per ulteriori informazioni su quali PKI sono richiesti certificati per la distribuzione di Azure Stack e come ottenerli, vedere [requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Le informazioni del certificato PKI fornite devono essere utilizzate come indicazioni generali. Prima di acquistare i certificati PKI per Azure Stack, lavorare con i partner hardware OEM. Si fornirà più dettagliato i requisiti e le indicazioni di certificato.


## <a name="time-synchronization"></a>Sincronizzazione degli orari
È necessario scegliere un orario specifico server con viene usato per la sincronizzazione di Azure Stack.  Symbolization tempo è fondamentale per Azure Stack e i relativi ruoli di infrastruttura, perché viene usato per generare i ticket Kerberos che vengono usati per autenticare i servizi interni tra loro.

È necessario specificare che un indirizzo IP per il server di sincronizzazione di tempo, anche se la maggior parte dei componenti dell'infrastruttura possa risolvere un URL, alcune può supportare solo gli indirizzi IP. Se si sono utilizza l'opzione di distribuzione disconnesso, è necessario specificare un server nella rete aziendale che si è certi raggiungibili dalla rete dell'infrastruttura in Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Connettere Azure Stack in Azure

Per gli scenari cloud ibridi, è necessario pianificare come si vuole connettere Azure Stack in Azure. Esistono due metodi supportati per connettere reti virtuali in Azure Stack per le reti virtuali in Azure: 
- **Site-to-site**. Una connessione di rete privata virtuale (VPN) su IPsec (IKE v1 e IKE v2). Questo tipo di connessione richiede un dispositivo VPN o Routing e accesso remoto (RRAS). Per altre informazioni sui gateway VPN di Azure, vedere [informazioni sul Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). La comunicazione attraverso questo tunnel viene crittografata e protetta. Tuttavia, larghezza di banda è limitata dalla velocità effettiva massima del tunnel (100 a 200 Mbps).
- **NAT in uscita**. Per impostazione predefinita, tutte le macchine virtuali in Azure Stack disporranno di connettività per le reti esterne tramite NAT in uscita. Ogni rete virtuale creata in Azure Stack Ottiene un indirizzo IP pubblico assegnato. Se la macchina virtuale viene assegnata direttamente un indirizzo IP pubblico o si trova dietro un bilanciamento del carico con un indirizzo IP pubblico, avrà accesso in uscita tramite NAT in uscita tramite l'indirizzo VIP della rete virtuale. Questa procedura funziona solo per le comunicazioni avviate dalla macchina virtuale e destinata per le reti esterne (internet o intranet). Non è utilizzabile per comunicare con la macchina virtuale dall'esterno.

### <a name="hybrid-connectivity-options"></a>Opzioni di connettività ibrida

Per la connettività ibrida, è importante prendere in considerazione il tipo di distribuzione si desidera offrire e in cui verrà distribuito. È necessario considerare se è necessario isolare il traffico di rete per ogni tenant e indica se è possibile una distribuzione intranet o internet.

- **Stack di Azure a tenant singolo**. Una distribuzione di Azure Stack che esegue una ricerca, almeno da una prospettiva di rete, come se fosse un unico tenant. Possono esistere che molte sottoscrizioni del tenant, ma come per qualsiasi servizio di rete intranet, tutto il traffico verrà trasmesso attraverso le stesse reti. Il traffico di rete da una sottoscrizione verrà trasmesso attraverso la stessa connessione di rete di un'altra sottoscrizione e non desidera essere isolate tramite un tunnel crittografato.

- **Stack di Azure multi-tenant**. Una distribuzione di Azure Stack in cui il traffico di ogni sottoscrizione tenant associato per le reti esterne ad Azure Stack deve essere isolato dal traffico di rete degli altri tenant.
 
- **Distribuzione Intranet**. Una distribuzione di Azure Stack che si trova in una rete intranet aziendale, in genere in spazio di indirizzi IP privati e protetti da firewall uno o più. Gli indirizzi IP pubblici non sono realmente pubblici, come non può essere instradati direttamente tramite internet pubblico.

- **Distribuzione di Internet**. Una distribuzione di Azure Stack che è connesso al pubblico indirizzi internet e Usa IP instradabile su internet pubblico per l'intervallo di indirizzi VIP pubblici. La distribuzione può ancora trovarsi dietro un firewall, ma l'intervallo di indirizzi VIP pubblico sia raggiungibile direttamente da internet e Azure pubblico.
 
Nella tabella seguente sono riepilogati gli scenari di connettività ibrida, con vantaggi, svantaggi e casi d'uso.

| Scenario | Metodo di connettività | Vantaggi | Svantaggi | Soluzione idonea per |
| -- | -- | --| -- | --|
| Singolo tenant di Azure Stack, distribuzione intranet | NAT in uscita | Migliore della larghezza di banda per trasferimenti più rapidi. Semplice da implementare; Nessun gateway necessari. | Traffico non crittografato. senza isolamento o crittografia all'esterno dello stack. | Distribuzioni aziendali in cui tutti i tenant sono altrettanto attendibili.<br><br>Organizzazioni che dispongono di un circuito Azure ExpressRoute in Azure. |
| Azure multi-tenant, Stack di distribuzione nella rete intranet | Da sito a VPN | Il traffico da rete virtuale del tenant di destinazione è sicuro. | Larghezza di banda è limitata dal tunnel VPN site-to-site.<br><br>Richiede un gateway nella rete virtuale e un dispositivo VPN della rete di destinazione. | Le distribuzioni aziendali in cui traffico alcuni tenant devono essere protetti da altri tenant. |
| Singolo tenant di Azure Stack, distribuzione di internet | NAT in uscita | Migliore della larghezza di banda per trasferimenti più rapidi. | Traffico non crittografato. senza isolamento o crittografia all'esterno dello stack. | Hosting di scenari in cui il tenant Ottiene un circuito dedicato all'ambiente Azure Stack e la distribuzione di Azure Stack. Ad esempio, Multiprotocol Label cambio MPLS ed ExpressRoute.
| Stack di Azure multi-tenant, la distribuzione di internet | Da sito a VPN | Il traffico da rete virtuale del tenant di destinazione è sicuro. | Larghezza di banda è limitata dal tunnel VPN site-to-site.<br><br>Richiede un gateway nella rete virtuale e un dispositivo VPN della rete di destinazione. | Hosting di scenari in cui il provider intende realizzare un cloud multi-tenant, in cui i tenant non considera attendibile tra loro e il traffico deve essere crittografato.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Utilizzo di ExpressRoute

Azure Stack è possibile connettersi ad Azure tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per scenari multi-tenant e a tenant singolo intranet. È necessario un circuito ExpressRoute con provisioning tramite [un provider di connettività](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Il diagramma seguente mostra ExpressRoute per uno scenario a tenant singolo (dove "Connessione cliente" è il circuito ExpressRoute).

![Scenario di ExpressRoute diagramma che mostra a tenant singolo](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Il diagramma seguente mostra ExpressRoute per uno scenario multi-tenant.

![Scenario di ExpressRoute diagramma che mostra multi-tenant](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitoraggio esterno
Per ottenere una singola visualizzazione di tutti gli avvisi dai dispositivi e la distribuzione di Azure Stack e integrare gli avvisi in IT del servizio Gestione flussi di lavoro esistenti per la creazione di ticket, è possibile [integra Azure Stack con Data Center esternosoluzionidimonitoraggio](azure-stack-integrate-monitor.md).

Incluso con la soluzione di Azure Stack, l'host del ciclo di vita dell'hardware è un computer all'esterno di Azure Stack che esegue strumenti di gestione fornito dal fornitore OEM per l'hardware. È possibile usare questi strumenti o altre soluzioni che si integrano direttamente con le soluzioni di monitoraggio esistente nel tuo Data Center.

La tabella seguente riepiloga l'elenco delle opzioni attualmente disponibili.

| Area | Soluzione di monitoraggio esterno |
| -- | -- |
| Software Azure Stack | [Azure Stack Management Pack per Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios plug-in](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Chiamate API basate su REST | 
| Server fisici (BMC tramite IPMI) | Hardware OEM - management pack di Operations Manager fornitore<br>Soluzione fornita dal produttore hardware OEM<br>Fornitore dell'hardware plug-in di Nagios | Monitoraggio delle soluzioni (incluso) supportato da partner OEM | 
| Dispositivi di rete (SNMP) | Individuazione dispositivo rete in Operations Manager<br>Soluzione fornita dal produttore hardware OEM<br>Opzione di Nagios plug-in |
| Monitoraggio dell'integrità della sottoscrizione tenant | [System Center Management Pack per Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Tenere presenti i requisiti seguenti:
- La soluzione in che uso deve essere senza agente. Non è possibile installare agenti di terze parti all'interno dei componenti di Azure Stack. 
- Se si desidera usare System Center Operations Manager, è necessario Operations Manager 2012 R2 o Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Backup e ripristino di emergenza

Pianificazione di backup e ripristino di emergenza consiste nel pianificare per entrambi Azure Stack infrastruttura sottostante che ospita le macchine virtuali IaaS e servizi PaaS e per le applicazioni di tenant e i dati. È necessario pianificare queste separatamente.

### <a name="protect-infrastructure-components"></a>Proteggere i componenti dell'infrastruttura

È possibile [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md) condividono componenti dell'infrastruttura per un protocollo SMB di specificare:

- È necessario una condivisione file SMB esterna in un file basato su Windows server esistente o un dispositivo di terze parti.
- Per il backup dei commutatori di rete e l'host del ciclo di vita dell'hardware, è consigliabile utilizzare questa condivisione stessa. Il fornitore dell'hardware OEM fornirà il materiale sussidiario per il backup e ripristino di questi componenti come queste sono esterne ad Azure Stack. Si è responsabile dell'esecuzione i flussi di lavoro di backup basati su indicazione del fornitore OEM.

In caso di grave perdita dei dati, è possibile usare il backup di infrastruttura ai dati della distribuzione, ad esempio distribuzione input e gli identificatori, gli account del servizio, certificato della CA radice, alle risorse federative (nel disconnesse distribuzioni), piani, offerte, reseed le sottoscrizioni, quote, le assegnazioni di ruolo e criteri RBAC e i segreti di Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteggere le applicazioni di tenant in macchine virtuali IaaS

Azure Stack non esegue il backup delle applicazioni tenant e i dati. È necessario pianificare per la protezione di ripristino di backup e ripristino di emergenza in una destinazione esterna ad Azure Stack. Protezione di tenant è un'attività basate su tenant. Per le macchine virtuali IaaS, i tenant possono usare le tecnologie nel guest per proteggere le cartelle del file, i dati dell'applicazione e lo stato del sistema. Tuttavia, come provider di servizio o dell'organizzazione, è possibile offrono una soluzione di backup e ripristino dello stesso Data Center o esternamente in un cloud.

Per eseguire il backup di macchine virtuali IaaS Windows o Linux, è necessario utilizzare i prodotti per il backup con l'accesso al sistema operativo guest per proteggere i file, cartella, lo stato del sistema operativo e i dati dell'applicazione. È possibile usare Backup di Azure, System Center Data Center Protection Manager, o prodotti di terze parti supportati.

Per replicare i dati in una posizione secondaria e orchestrare il failover dell'applicazione se si verifica un'emergenza, è possibile usare Azure Site Recovery, o prodotti di terze parti supportati. Inoltre, le applicazioni che supportano la replica nativa, ad esempio Microsoft SQL Server, possono replicare i dati in un'altra posizione in cui viene eseguita l'applicazione.

## <a name="learn-more"></a>Altre informazioni

- Per informazioni sui casi d'uso, acquisto, partner e fornitori di hardware OEM, vedere la [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e la disponibilità geografica per Azure Stack i sistemi integrati, vedere il white paper: [Azure Stack: un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Passaggi successivi
[Modelli di Azure Stack distribuzione connessione](azure-stack-connection-models.md)
