---
title: Procedure consigliate per la sicurezza di rete - Microsoft AzureBest practices for network security - Microsoft Azure
description: Questo articolo fornisce una serie di procedure consigliate per la sicurezza di rete usando le funzionalità integrate di Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548311"
---
# <a name="azure-best-practices-for-network-security"></a>Procedure consigliate di Azure per la sicurezza di reteAzure best practices for network security
Questo articolo illustra una raccolta di procedure consigliate di Azure per migliorare la sicurezza di rete. derivate dalla nostra esperienza con la rete di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata questo articolo spiega:

* Qual è la procedura consigliata
* Il motivo per cui si vuole abilitare tale procedura consigliata
* Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
* Alternative possibili alla procedura consigliata
* Come imparare ad abilitare la procedura consigliata

Queste procedure consigliate si basano su un parere di consenso e sulle funzionalità della piattaforma Azure e sui set di funzionalità, così come esistono al momento della scrittura di questo articolo. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

## <a name="use-strong-network-controls"></a>Utilizzare controlli di rete sicuri
È possibile connettere i dispositivi e le [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) ad altri dispositivi di rete inserendoli in [reti virtuali di Azure](../../virtual-network/index.yml). Vale a dire che si possono connettere le schede di interfaccia di rete virtuale a una rete virtuale per consentire le comunicazioni basate su TCP/IP tra dispositivi di rete abilitati. Le macchine virtuali connesse a una rete virtuale di Azure possono connettersi ai dispositivi nella stessa rete virtuale, in reti virtuali diverse, su Internet o persino in reti locali.

Durante la pianificazione della rete e della sicurezza della rete, è consigliabile centralizzare:

- Gestione delle funzioni di rete di base come ExpressRoute, provisioning di rete virtuale e subnet e indirizzamento IP.
- Governance degli elementi di sicurezza di rete, ad esempio le funzioni dell'appliance virtuale di rete come ExpressRoute, il provisioning di reti virtuali e subnet e l'indirizzamento IP.

Se si utilizza un set comune di strumenti di gestione per monitorare la rete e la sicurezza della rete, si ottiene una chiara visibilità su entrambi. Una strategia di sicurezza semplice e unificata riduce gli errori perché aumenta la comprensione umana e l'affidabilità dell'automazione.

## <a name="logically-segment-subnets"></a>Segmentare logicamente le subnet
Le reti virtuali di Azure sono simili alle reti LAN nella rete locale. L'idea alla base di una rete virtuale di Azure è la creazione di una rete, basata su un singolo spazio di indirizzi IP privati, in cui è possibile posizionare tutte le macchine virtuali di Azure.The idea behind an Azure virtual network is it you create a network, based on a single private IP address space, on which you can place all your Azure virtual machines. Gli spazi indirizzi IP privati disponibili sono negli intervalli di Classe A (10.0.0.0/8), Classe B (172.16.0.0/12) e Classe C (192.168.0.0/16).

Le procedure consigliate per suddividere logicamente le subnet includono:

**Procedura consigliata:** non assegnare regole di autorizzazione con intervalli ampi (ad esempio, consentire da 0.0.0.0 a 255.255.255.255).  
**Dettaglio**: Assicurarsi che le procedure di risoluzione dei problemi scoraggino o proibiscano l'impostazione di questi tipi di regole. Queste regole consentono di portare a un falso senso di sicurezza e vengono spesso trovate e sfruttate dai team rossi.

**Procedura consigliata**: segmentare lo spazio di indirizzi più ampio in subnet.   
**Dettagli**: usare i principi di suddivisione in subnet basati su [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per creare le subnet.

**Procedura consigliata**: creare controlli di accesso di rete tra subnet. Il routing tra subnet avviene automaticamente e non è necessario configurare manualmente le tabelle di routing. Per impostazione predefinita, non sono disponibili controlli di accesso alla rete tra le subnet create in una rete virtuale di Azure.By default, there are no network access controls between the subnets that you create on an Azure virtual network.   
**Dettaglio:** usare un gruppo di sicurezza di rete per la protezione dal traffico non richiesto nelle subnet di Azure.Detail: Use a [network security group](/azure/virtual-network/virtual-networks-nsg) to protect against unsolicited traffic into Azure subnets. I gruppi di sicurezza di rete sono dispositivi di ispezione pacchetti semplici e con stato che usano l'approccio a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione e protocollo di livello 4) per creare regole di autorizzazione/negazione per il traffico di rete.Network security groups are simple, stateful packet inspection devices that use the 5-tuple approach (source IP, source port, destination IP, destination port, and layer 4 protocol) to create allow/deny rules for network traffic. È possibile consentire o negare il traffico da e verso un singolo indirizzo IP, da e verso più indirizzi IP o da e verso intere subnet.

Quando si utilizzano gruppi di sicurezza di rete per il controllo dell'accesso di rete tra subnet, è possibile inserire le risorse che appartengono alla stessa area di sicurezza o ruolo nelle proprie subnet.

**Procedura consigliata**: evitare reti virtuali e subnet di piccole dimensioni per garantire semplicità e flessibilità.   
**Dettaglio**: La maggior parte delle organizzazioni aggiunge più risorse di quanto inizialmente pianificato e la riallocazione degli indirizzi richiede un utilizzo intensivo del lavoro. L'utilizzo di subnet di piccole dimensioni aggiunge un valore di sicurezza limitato e il mapping di un gruppo di sicurezza di rete a ogni subnet comporta l'aggiunta di overhead. Definire le subnet in modo ampio per garantire la flessibilità per la crescita.

**Procedura consigliata**: Semplificare la gestione delle regole dei gruppi di sicurezza di rete definendo i gruppi di [sicurezza delle](https://azure.microsoft.com/blog/applicationsecuritygroups/)applicazioni .  
**Dettaglio**: Definire un gruppo di sicurezza dell'applicazione per elenchi di indirizzi IP che si ritiene potrebbero cambiare in futuro o essere utilizzati in molti gruppi di sicurezza di rete. Assicurarsi di assegnare un nome chiaramente ai gruppi di sicurezza delle applicazioni in modo che gli altri utenti possano comprenderne il contenuto e lo scopo.

## <a name="adopt-a-zero-trust-approach"></a>Adottare un approccio con attendibilità zero
Le reti basate sul perimetro operano partendo dal presupposto che tutti i sistemi all'interno di una rete possano essere considerati attendibili. Ma i dipendenti di oggi accedono alle risorse della propria organizzazione da qualsiasi luogo su una varietà di dispositivi e app, il che rende irrilevanti i controlli di sicurezza perimetrale. I criteri di controllo di accesso che si concentrano solo su chi può accedere a una risorsa non sono sufficienti. Per padroneggiare l'equilibrio tra sicurezza e produttività, gli amministratori della sicurezza devono anche tenere conto della *modalità* di accesso a una risorsa.

Le reti devono evolversi dalle difese tradizionali perché le reti potrebbero essere vulnerabili alle violazioni: un utente malintenzionato può compromettere un singolo endpoint all'interno del limite attendibile e quindi espandere rapidamente un punto d'appoggio attraverso l'intera rete. Le reti [con attendibilità](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) zero eliminano il concetto di trust in base al percorso di rete all'interno di un perimetro. Al contrario, le architetture di attendibilità zero utilizzano attestazioni di attendibilità utente e dispositivo per negare l'accesso ai dati e alle risorse dell'organizzazione. Per le nuove iniziative, adottare approcci con attendibilità zero che convalidino la fiducia al momento dell'accesso.

Le procedure consigliate sono:

**Procedura consigliata:** concedere l'accesso condizionale alle risorse in base a dispositivo, identità, garanzia, percorso di rete e altro ancora.  
**Dettaglio:** [l'accesso condizionale](/azure/active-directory/conditional-access/overview) di Azure AD consente di applicare i controlli di accesso corretti implementando decisioni sul controllo di accesso automatico in base alle condizioni richieste. Per altre informazioni, vedere [Gestire l'accesso alla gestione di Azure con l'accesso condizionale.](../../role-based-access-control/conditional-access-azure-management.md)

**Procedura consigliata:** abilitare l'accesso alle porte solo dopo l'approvazione del flusso di lavoro.  
**Dettaglio:** è possibile usare [l'accesso alle macchine virtuali just-in-time nel Centro sicurezza](../../security-center/security-center-just-in-time.md) di Azure per bloccare il traffico in ingresso verso le macchine virtuali di Azure, riducendo l'esposizione agli attacchi e fornendo un facile accesso per connettersi alle macchine virtuali quando necessario.

**Procedura consigliata:** concedere autorizzazioni temporanee per eseguire attività con privilegi, che impediscono a utenti malintenzionati o non autorizzati di ottenere l'accesso dopo la scadenza delle autorizzazioni. L'accesso viene concesso solo quando l'utente ne ha necessità.  
**Dettaglio:** usare l'accesso Just-In-Time in Gestione delle identità con privilegi di Azure AD o in una soluzione di terze parti per concedere le autorizzazioni per eseguire attività con privilegi.

La fiducia zero è la prossima evoluzione nella sicurezza di rete. Lo stato degli attacchi informatici spinge le organizzazioni a prendere la mentalità "assume breach", ma questo approccio non dovrebbe essere limitante. Le reti con attendibilità zero proteggono i dati e le risorse aziendali, garantendo al contempo che le organizzazioni possano creare un ambiente di lavoro moderno utilizzando tecnologie che consentono ai dipendenti di essere produttivi in qualsiasi momento e ovunque e in qualsiasi modo.

## <a name="control-routing-behavior"></a>Controllare il comportamento di routing
Quando si inserisce una macchina virtuale in una rete virtuale di Azure, la macchina virtuale può connettersi a qualsiasi altra macchina virtuale nella stessa rete virtuale e ad altre macchine virtuali in subnet diverse. Questo è possibile perché un insieme di route di sistema, abilitate per impostazione predefinita, consente questo tipo di comunicazione. Queste route predefinite consentono alle macchine virtuali nella stessa rete virtuale di avviare le connessioni tra loro e con Internet (per le comunicazioni in uscita solo con Internet).

Nonostante le route di sistema predefinite siano utili per molti scenari di distribuzione, in alcuni casi si vuole personalizzare la configurazione del routing per le distribuzioni. È consentito configurare l'indirizzo hop successivo in modo da raggiungere destinazioni specifiche.

È consigliabile configurare [route definite dall'utente](../../virtual-network/virtual-networks-udr-overview.md) quando si distribuisce un dispositivo di sicurezza per una rete virtuale. Questo aspetto viene affrontato in una sezione successiva intitolata [Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Le route definite dall'utente non sono necessarie e le route di sistema predefinite generalmente funzionano.
>
>

## <a name="use-virtual-network-appliances"></a>Usare i dispositivi di rete virtuale
I gruppi di sicurezza di rete e il routing definito dall'utente possono fornire una certa misura della sicurezza di rete a livello di rete e di trasporto del [modello OSI.](https://en.wikipedia.org/wiki/OSI_model) Ma in alcune situazioni, è preferibile o necessario abilitare la sicurezza ai livelli alti dello stack. In tali situazioni, è consigliabile distribuire i dispositivi di sicurezza di rete virtuale forniti dai partner di Azure.

I dispositivi di sicurezza di rete di Azure possono offrire livelli di sicurezza migliori rispetto a quanto offerto dai controlli a livello di rete. Le funzionalità di sicurezza di rete fornite da dispositivi di sicurezza di rete virtuale includono:


* Funzionalità di firewall
* Rilevamento intrusioni/Prevenzione intrusioni
* Gestione vulnerabilità
* Controllo applicazione
* Rilevamento anomalie basato su rete
* Filtro Web
* Antivirus
* Protezione botnet

Per trovare le appliance di sicurezza di rete virtuale di Azure disponibili, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "sicurezza di rete".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuire reti perimetrali per le aree di sicurezza
Una [rete perimetrale](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) è un segmento di rete fisica o logica che fornisce un ulteriore livello di sicurezza tra le risorse e Internet. I dispositivi specializzati per il controllo di accesso alla rete dislocati al margine di una rete perimetrale lasciano entrare nella rete virtuale solo il traffico desiderato.

Le reti perimetrali sono utili perché permettono di concentrare le operazioni di gestione, monitoraggio, registrazione e creazione di report del controllo di accesso alla rete sui dispositivi della rete virtuale di Azure. Una rete perimetrale è dove in genere si abilita la prevenzione DDoS (Distributed Denial of Service), i sistemi di rilevamento/prevenzione delle intrusioni (IDS/IPS), le regole e i criteri del firewall, il filtro Web, l'antimalware di rete e altro ancora. I dispositivi di sicurezza di rete sono posizionati tra Internet e la rete virtuale di Azure e hanno un'interfaccia su entrambe le reti.

Anche se questo è il design di base di una rete perimetrale, ci sono molti disegni diversi, come back-to-back, tri-homed, e multi-homed.

In base al concetto di attendibilità zero menzionato in precedenza, è consigliabile usare una rete perimetrale per tutte le distribuzioni ad alta sicurezza per migliorare il livello di sicurezza di rete e il controllo degli accessi per le risorse di Azure.Based on the zo Trust concept mentioned earlier, we recommend that you consider using a perimeter network for all high security deployments to enhance the level of network security and access control for your Azure resources. È possibile usare Azure o una soluzione di terze parti per fornire un ulteriore livello di sicurezza tra le risorse e Internet:You can use Azure or a third-party solution to provide an additional layer of security between your assets and the internet:

- Controlli nativi di Azure.Azure native controls. [Firewall di Azure](/azure/firewall/overview) e il [firewall dell'applicazione Web nel gateway applicazione](../../application-gateway/features.md#web-application-firewall) offrono una sicurezza di base con un firewall completamente con stato come servizio, disponibilità elevata integrata, scalabilità cloud senza restrizioni, filtro FQDN, supporto per set di regole di base OWASP e configurazione e configurazione semplice.
- Offerte di terze parti. Cerca nel [Marketplace di Azure Marketplace](https://azuremarketplace.microsoft.com/) il firewall di nuova generazione (NGFW) e altre offerte di terze parti che forniscono strumenti di sicurezza familiari e livelli di sicurezza di rete notevolmente migliorati. La configurazione potrebbe essere più complessa, ma un'offerta di terze parti potrebbe consentire di utilizzare le funzionalità e i set di competenze esistenti.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitare l'esposizione a Internet con collegamenti WAN dedicati
Molte organizzazioni hanno scelto la strada dell'IT ibrido. Con l'IT ibrido, alcuni degli asset informativi dell'azienda sono in Azure e altri rimangono in locale. In molti casi alcuni componenti di un servizio sono eseguiti in Azure mentre altri componenti restano in locale.

In uno scenario IT ibrido, in genere è disponibile un tipo di connettività cross-premise. che consente alla società di connettere le proprie reti locali alle reti virtuali di Azure. Sono disponibili due soluzioni di connettività cross-premise:

* [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). È una tecnologia affidabile, affidabile e consolidata, ma la connessione avviene su Internet. La larghezza di banda è vincolata a un massimo di circa 1,25 Gbps. VPN da sito a sito è un'opzione desiderabile in alcuni scenari.
* **Azure ExpressRoute**. per la connettività cross-premise, [ExpressRoute](../../expressroute/expressroute-introduction.md) è la scelta consigliata. ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute, puoi stabilire connessioni a servizi cloud Microsoft come Azure, Office 365 e Dynamics 365. ExpressRoute è un collegamento WAN dedicato tra la posizione locale o un provider di hosting di Microsoft Exchange.ExpressRoute is a dedicated WAN link between your on-premises location or a Microsoft Exchange hosting provider. Poiché si tratta di una connessione telco, i dati non viaggiano su Internet, quindi non sono esposti ai potenziali rischi delle comunicazioni Internet.

La posizione della connessione ExpressRoute può influire sulla capacità del firewall, la scalabilità, l'affidabilità e la visibilità del traffico di rete. È necessario identificare dove terminare ExpressRoute nelle reti esistenti (locali). È possibile:

- Terminare all'esterno del firewall (il paradigma della rete perimetrale) se è necessaria la visibilità del traffico, se è necessario continuare una pratica esistente di isolamento dei data center o se si inseriscono esclusivamente risorse Extranet in Azure.Terminate outside the firewall (the perimeter network paradigm) if you require visibili into the traffic, if you need to continue an existing practice of iting datacenters, or if you're sole putting extranet resources on Azure.
- Terminare all'interno del firewall (il paradigma dell'estensione di rete). Questa è la raccomandazione predefinita. In tutti gli altri casi, è consigliabile considerare Azure come un ennesimo data center.

## <a name="optimize-uptime-and-performance"></a>Ottimizzare il tempo di attività e le prestazioni
Se un servizio non è attivo, non è possibile accedere alle informazioni. Se le prestazioni sono talmente insufficienti da rendere i dati inutilizzabili, è possibile considerare che i dati siano inaccessibili. Dal punto di vista della sicurezza, è necessario fare del proprio meglio per assicurarsi che i servizi offrano sempre prestazioni e tempi di attività ottimali.

Uno dei metodi più diffusi ed efficaci per migliorare le prestazioni e la disponibilità consiste nel ricorrere al bilanciamento del carico. Il bilanciamento del carico è un metodo di distribuzione del traffico di rete tra server che fanno parte di un servizio. Ad esempio, se dei server Web front-end fanno parte del servizio, è possibile usare il bilanciamento del carico per distribuire il traffico tra più server Web front-end.

Questa distribuzione del traffico aumenta la disponibilità perché se uno dei server Web non è più disponibile, il servizio di bilanciamento del carico interrompe l'invio di traffico a tale server, reindirizzandolo verso i server che sono ancora online. Il bilanciamento del carico favorisce anche le prestazioni, perché il sovraccarico di processore, rete e memoria per l'elaborazione delle richieste viene distribuito tra tutti server con carico bilanciato.

È consigliabile impiegare il bilanciamento del carico ogni volta possibile e quando adeguato ai servizi. Di seguito sono descritti degli scenari a livello di rete virtuale di Azure e altri a livello globale, con le opzioni di bilanciamento del carico per ognuno.

**Scenario**: è presente un'applicazione che:

- Necessita che le richieste provengano dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end. Esempi di queste applicazioni sono i carrelli dei siti di acquisti e i server di posta Web.
- Accetta solo connessioni protette, pertanto le comunicazioni non crittografate verso il server non rappresentano un'opzione accettabile.
- Necessita che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.

**Opzione di bilanciamento del carico**: usare il [gateway applicazione di Azure](/azure/application-gateway/application-gateway-introduction), un servizio di bilanciamento del carico legato al traffico Web HTTP. Il gateway applicazione supporta la crittografia TLS end-to-end e la [terminazione TLS](/azure/application-gateway/application-gateway-introduction) nel gateway. I server Web possono quindi essere liberati dal sovraccarico prodotto dai processi di crittografia e decrittografia e il traffico può essere trasmesso in formato non crittografato ai server back-end.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni in ingresso da Internet tra i server che si trovano in una rete virtuale di Azure. Scenari in cui:

- Sono presenti applicazioni senza stato che accettano le richieste in ingresso da Internet.
- Non richiedono sessioni appiccicose o offload TLS. La combinazione di sessioni permanenti e del bilanciamento del carico delle applicazioni è un metodo per ottenere l'affinità dei server.

**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico esterno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni provenienti da macchine virtuali che non si trovano su Internet. Nella maggior parte dei casi, le connessioni che sono accettate per il bilanciamento del carico possono essere avviate da dispositivi nella rete virtuale di Azure, quali le istanze SQL Server o i server Web interni.   
**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario il bilanciamento del carico globale quando:

- Si dispone di una soluzione cloud ampiamente distribuita tra più aree e che richiede il livello massimo di tempo di attività (disponibilità) possibile.
- Si necessita del massimo livello di tempo di attività possibile per assicurarsi che il servizio sia disponibile anche se un intero data center diventa non disponibile.

**Opzione di bilanciamento del carico**: usare Gestione traffico di Azure. Gestione traffico rende possibile bilanciare il carico delle connessioni ai servizi in base alla posizione dell'utente.

Ad esempio, se l'utente effettua una richiesta al servizio dall'Unione europea, la connessione viene indirizzata ai servizi che si trovano in un data center dell'Unione europea. Questa parte bilanciamento del carico globale di Gestione traffico consente di migliorare le prestazioni perché la connessione al data center più vicino è più veloce rispetto alla connessione ai data center più lontani.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Disabilitare l'accesso RDP/SSH alle macchine virtuali
È possibile raggiungere le macchine virtuali di Azure usando [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e il protocollo Secure [Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Questi protocolli consentono di gestire le macchine virtuali da postazioni remote e sono standard nel computing dei data center.

L'uso di questi protocolli in Internet può provocare tuttavia un potenziale problema di sicurezza perché gli utenti malintenzionati possono usare tecniche di [attacco di forza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) per ottenere l'accesso alle macchine virtuali di Azure. Una volta che gli utenti malintenzionati avranno ottenuto l'accesso, potranno usare la macchina virtuale come punto di partenza per compromettere gli altri computer nella rete virtuale o persino per attaccare i dispositivi di rete all'esterno di Azure.

È consigliabile disabilitare l'accesso diretto RDP e SSH alle macchine virtuali di Azure da Internet. Dopo aver disabilitato l'accesso diretto RDP e SSH da Internet, sono disponibili altre opzioni per accedere a queste macchine virtuali per la gestione remota.

**Scenario**: consentire a un singolo utente di connettersi a una rete virtuale di Azure via Internet.   
**Opzione**: [VPN da punto a sito](/azure/vpn-gateway/vpn-gateway-point-to-site-create) è un altro termine per indicare una connessione client/server VPN con accesso remoto. Dopo aver stabilito la connessione da punto a sito, l'utente può usare RDP o SSH per connettersi a qualsiasi macchina virtuale presente nella rete virtuale Azure e a cui l'utente si è connesso con VPN da punto a sito. Ciò presuppone che l'utente sia autorizzato a raggiungere tali macchine virtuali.

La VPN da punto a sito è più sicura delle connessioni dirette RDP o SSH perché l'utente deve autenticarsi due volte prima di potersi connettere a una macchina virtuale. In primo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione VPN da punto a sito. In secondo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione RDP o SSH.

**Scenario**: abilitare gli utenti della rete locale per connettersi alle macchine virtuali nella rete virtuale di Azure.   
**Opzione**: [VPN da sito a sito](/azure/vpn-gateway/vpn-gateway-site-to-site-create) connette un'intera rete a un'altra rete via Internet. È possibile usare una VPN da sito a sito per connettere la rete locale a una rete virtuale di Azure. Gli utenti della rete locale si connettono usando il protocollo RDP o SSH tramite la connessione VPN da sito a sito. Non è necessario consentire l'accesso diretto RDP o SSH su Internet.

**Scenario**: usare un collegamento WAN dedicato per fornire funzionalità simili alla VPN da sito a sito.   
**Opzione**: usare [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Offre funzionalità simili a quelle della VPN da sito a sito. Le differenze principali sono le seguenti:

- Il collegamento WAN dedicato non attraversa Internet.
- I collegamenti WAN dedicati sono in genere più stabili e più efficienti.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali
Usare gli endpoint servizio di rete virtuale per estendere lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

Gli endpoint di servizio offrono i vantaggi seguenti:

- **Maggiore sicurezza per le risorse dei servizi di Azure**: con gli endpoint di servizio è possibile associare le risorse dei servizi di Azure alla rete virtuale. In questo modo si ottiene una maggiore sicurezza perché si rimuove completamente l'accesso Internet pubblico alle risorse, consentendo solo il traffico dalla rete virtuale.
- **Routing ottimale per il traffico del servizio di Azure dalla rete virtuale**: le route nella rete virtuale che forzano il transito del traffico Internet attraverso appliance locali e/o virtuali, eseguendo così un tunneling forzato, forzano anche il traffico del servizio di Azure nella stessa route del traffico Internet. Gli endpoint di servizio forniscono il routing ottimale per il traffico di Azure.

  Gli endpoint instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Azure. Mantenendo il traffico nella rete backbone di Azure è possibile continuare a monitorare e verificare il traffico Internet in uscita dalle reti virtuali, tramite il tunneling forzato, senza conseguenze per il traffico del servizio. Vedere altre informazioni sulle [route definite dall'utente e il tunneling forzato](../../virtual-network/virtual-networks-udr-overview.md).

- **Semplice da configurare con un minor sovraccarico di gestione**: non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure attraverso il firewall IP. Non sono necessari dispositivi NAT o gateway per configurare gli endpoint di servizio. Un endpoint di servizio può essere configurato con un semplice clic in una subnet. Non c'è alcun sovraccarico aggiuntivo per la gestione degli endpoint.

Per altre informazioni sugli endpoint di servizio, sui servizi di Azure e le aree per cui gli endpoint sono disponibili, vedere [Endpoint servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere Procedure consigliate e modelli di sicurezza di Azure per altre procedure consigliate di sicurezza da usare durante la progettazione, la distribuzione e la gestione delle soluzioni cloud tramite Azure.See [Azure security best practices](best-practices-and-patterns.md) and patterns for more security best practices to use when you're designing, deploying, and managing your cloud solutions by using Azure.
