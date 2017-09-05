---
title: Guida alla progettazione e alla pianificazione di una rete virtuale di Azure | Documentazione Microsoft
description: "Informazioni su come pianificare e progettare reti virtuali in Azure in base ai requisiti di isolamento, connettività e località."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 9a0126235c9ff3fec05d7709bdee95ab4832a33b
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="plan-and-design-azure-virtual-networks"></a>Pianificare e progettare reti virtuali di Azure
Creare una rete virtuale a scopi sperimentali è abbastanza semplice, ma è probabile che si distribuiscano più reti virtuali nel corso del tempo per supportare le esigenze di produzione dell'organizzazione. Con una pianificazione e una progettazione adeguate è possibile distribuire reti virtuali e connettere le risorse necessarie in modo più efficace. Se non si ha familiarità con le reti virtuali, è consigliabile [acquisire informazioni sulle reti virtuali](virtual-networks-overview.md) e su [come distribuirne](virtual-networks-create-vnet-arm-pportal.md) una prima di procedere.

## <a name="plan"></a>Pianificazione
Per ottenere buoni risultati, è fondamentale una conoscenza approfondita delle sottoscrizioni, delle aree e delle risorse di rete di Azure. È possibile usare l'elenco di considerazioni di seguito come punto di partenza. Dopo aver compreso queste considerazioni, è possibile definire i requisiti per la progettazione della rete.

### <a name="considerations"></a>Considerazioni
Prima di rispondere alle domande di pianificazione seguenti, tenere in considerazione quanto segue:

* Tutti gli oggetti creati in Azure sono composti da una o più risorse. Una macchina virtuale (VM) è una risorsa, la scheda di interfaccia di rete (NIC) usata da una VM è una risorsa, l'indirizzo IP pubblico usato da una scheda di interfaccia di rete è una risorsa, la rete virtuale a cui è collegata la scheda di interfaccia di rete è una risorsa.
* Le risorse vengono create all'interno di un' [area](https://azure.microsoft.com/regions/#services) e una sottoscrizione di Azure. Le risorse possono essere connesse solo a una rete virtuale presente nella stessa area e nella stessa sottoscrizione della risorsa.
* È possibile connettere tra di esse le reti virtuali tramite:
    * **[Reti virtuali con peering](virtual-network-peering-overview.md)**: le reti virtuali devono trovarsi nella stessa area di Azure. La larghezza di banda tra le risorse delle reti virtuali con peering rimane uguale a quella tra le risorse connesse alla stessa rete virtuale.
    * **Gateway VPN[ di Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: le reti virtuali possono trovarsi nella stessa area di Azure o in aree diverse. La larghezza di banda tra le risorse delle reti virtuali connesse tramite gateway VPN è limitata dalla larghezza di banda del gateway VPN.
* È anche possibile collegare le reti virtuali alla rete locale usando una delle [opzioni di connettività](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) disponibili in Azure.
* È possibile raggruppare risorse diverse in [gruppi di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups), rendendo più semplice la gestione delle risorse come unità. Un gruppo di risorse può contenere risorse provenienti da più aree, purché le risorse appartengano alla stessa sottoscrizione.

### <a name="define-requirements"></a>Definire i requisiti
Usare le domande seguenti come punto di partenza per la progettazione della rete di Azure.    

1. Quali località di Azure verranno usate per ospitare le reti virtuali?
2. È necessario che tali località di Azure siano in comunicazione tra loro?
3. È necessario che le reti virtuali di Azure e i data center locali siano in comunicazione tra loro?
4. Quante VM IaaS (Infrastructure as a Service), ruoli dei servizi cloud e app Web sono necessari per la soluzione?
5. È necessario isolare il traffico in base a gruppi di VM (ad esempio server Web front-end e server database back-end)?
6. È necessario controllare il flusso del traffico tramite appliance virtuali?
7. Gli utenti necessitano di diversi set di autorizzazioni per risorse diverse di Azure?

### <a name="understand-vnet-and-subnet-properties"></a>Comprendere le proprietà della rete virtuale e della subnet
Le risorse della rete virtuale e della subnet consentono di definire un limite di sicurezza per i carichi di lavoro in esecuzione in Azure. Una rete virtuale è caratterizzata da una raccolta di spazi di indirizzi, definiti come blocchi CIDR.

> [!NOTE]
> Gli amministratori di rete hanno familiarità con la notazione CIDR. Se non si ha familiarità con CIDR, è possibile [ottenere maggiori informazioni](http://whatismyipaddress.com/cidr).
>
>

Le reti virtuali contengono le proprietà seguenti.

| Proprietà | Descrizione | Vincoli |
| --- | --- | --- |
| **nome** |Nome della rete virtuale |Stringa di massimo 80 caratteri. Può contenere lettere, numeri, caratteri di sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero. Deve terminare con una lettera, un numero o un carattere di sottolineatura. Può contenere lettere maiuscole o minuscole. |
| **location** |Località di Azure (nota anche come area). |Deve essere una delle località di Azure valide. |
| **addressSpace** |Raccolta di prefissi di indirizzi che costituiscono la rete virtuale nella notazione CIDR. |Deve essere una matrice di blocchi di indirizzi CIDR validi, inclusi intervalli di indirizzi IP pubblici. |
| **subnet** |Raccolta di subnet che costituiscono la rete virtuale |Vedere la tabella delle proprietà della subnet di seguito. |
| **dhcpOptions** |Oggetto che contiene un'unica proprietà obbligatoria denominata **dnsServers**. | |
| **dnsServers** |Matrice di server DNS usati dalla rete virtuale. Se non si specifica alcun server, viene usata la risoluzione dei nomi interna di Azure. |Deve essere una matrice di massimo 10 server DNS, in base all'indirizzo IP. |

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

Le subnet contengono le proprietà seguenti.

| Proprietà | Descrizione | Vincoli |
| --- | --- | --- |
| **nome** |Nome della subnet |Stringa di massimo 80 caratteri. Può contenere lettere, numeri, caratteri di sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero. Deve terminare con una lettera, un numero o un carattere di sottolineatura. Può contenere lettere maiuscole o minuscole. |
| **location** |Località di Azure (nota anche come area). |Deve essere una delle località di Azure valide. |
| **addressPrefix** |Singolo prefisso di indirizzo che costituisce la subnet nella notazione CIDR |Deve essere un singolo blocco CIDR che fa parte di uno degli spazi degli indirizzi della rete virtuale. |
| **networkSecurityGroup** |NSG applicato alla subnet | |
| **routeTable** |Tabella di route applicata alla subnet | |
| **ipConfigurations** |Raccolta di oggetti di configurazione IP usati dalle schede di interfaccia di rete connesse alla subnet | |

### <a name="name-resolution"></a>Risoluzione dei nomi
Per impostazione predefinita, la rete virtuale usa la [risoluzione dei nomi offerta da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md) per risolvere i nomi all'interno della rete virtuale e sulla rete Internet pubblica. Tuttavia, se si connettono le reti virtuali ai data center locali, è necessario indicare [il proprio server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) per la risoluzione dei nomi tra le reti.  

### <a name="limits"></a>Limiti
Esaminare i limiti di rete nell'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits) per garantire che il modello non entri in conflitto con uno dei limiti. Alcun limiti possono essere aumentati aprendo un ticket di supporto.

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
È possibile usare il [controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md) per controllare il livello di accesso di utenti diversi a risorse diverse in Azure. In questo modo è possibile isolare il lavoro svolto dal proprio team in base alle esigenze.

Per quanto riguarda le reti virtuali, gli utenti con ruolo di **collaboratore di rete** hanno pieno controllo sulle risorse della rete virtuale di Gestione risorse di Azure. Allo stesso modo, gli utenti con ruolo di **collaboratore di rete classico** hanno pieno controllo sulle risorse della rete virtuale classica.

> [!NOTE]
> È anche possibile [creare ruoli personalizzati](../active-directory/role-based-access-control-configure.md) per separare le esigenze amministrative.
>
>

## <a name="design"></a>Progettazione
Dopo avere risposto alle domande della sezione [Pianificazione](#Plan) , esaminare le informazioni seguenti prima di definire le reti virtuali.

### <a name="number-of-subscriptions-and-vnets"></a>Numero di sottoscrizioni e reti virtuali
È consigliabile creare più reti virtuali negli scenari seguenti:

* **VM da collocare nelle diverse località di Azure**. Le reti virtuali in Azure fanno riferimento a un'area geografica e non possono estendersi a più località. Per questo motivo, è necessaria almeno una rete virtuale per ogni località di Azure in cui si vogliono ospitare VM.
* **Carichi di lavoro che devono essere completamente isolati l'uno dall'altro**. È possibile creare reti virtuali separate, che usano anche gli stessi spazi degli indirizzi IP, per isolare carichi di lavoro diversi l'uno dall'altro.

Tenere presente che i limiti sopra indicati si riferiscono a singole aree e sottoscrizioni. Questo significa che è possibile usare più sottoscrizioni per aumentare il limite di risorse gestibili in Azure. È possibile usare una VPN site-to-site o un circuito ExpressRoute per connettere reti virtuali in sottoscrizioni diverse.

### <a name="subscription-and-vnet-design-patterns"></a>Modelli di progettazione per sottoscrizioni e reti virtuali
La tabella seguente illustra alcuni modelli di progettazione comuni per l'uso di sottoscrizioni e reti virtuali.

| Scenario | Diagramma | Vantaggi | Svantaggi |
| --- | --- | --- | --- |
| Singola sottoscrizione, due reti virtuali per app |![Singola sottoscrizione](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Solo una sottoscrizione da gestire. |Numero massimo di reti virtuali per area di Azure. Oltre questo limite, sono necessarie ulteriori sottoscrizioni. Per informazioni dettagliate, leggere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits). |
| Una sottoscrizione per app, due reti virtuali per app |![Singola sottoscrizione](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Usa solo due reti virtuali per sottoscrizione. |Più difficile da gestire quando sono presenti molte app. |
| Una sottoscrizione per business unit, due reti virtuali per app. |![Singola sottoscrizione](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Equilibrio tra numero di sottoscrizioni e reti virtuali. |Numero massimo di reti virtuali per business unit (sottoscrizione). Per informazioni dettagliate, leggere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits). |
| Una sottoscrizione per business unit, due reti virtuali per gruppo di app. |![Singola sottoscrizione](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Equilibrio tra numero di sottoscrizioni e reti virtuali. |Le app devono essere isolate usando subnet e gruppi di sicurezza di rete. |

### <a name="number-of-subnets"></a>Numero di subnet
È consigliabile creare più subnet in una rete virtuale negli scenari seguenti:

* **Numero insufficiente di indirizzi IP privati per tutte le schede di interfaccia di rete in una subnet**. Se lo spazio degli indirizzi della subnet non contiene indirizzi IP sufficienti per il numero di schede di interfaccia di rete nella subnet, è necessario creare più subnet. Tenere presente che Azure riserva 5 indirizzi IP privati da ogni subnet che non possono essere usati: il primo e l'ultimo indirizzo dello spazio degli indirizzi (per l'indirizzo subnet e multicast) e 3 indirizzi da usare internamente (a scopi DHCP e DNS).
* **Sicurezza**. È possibile usare subnet per separare gruppi di VM l'uno dall'altro per i carichi di lavoro con una struttura a più livelli e applicare diversi [gruppi di sicurezza di rete](virtual-networks-nsg.md#subnets) per queste subnet.
* **Connettività ibrida**. È possibile usare gateway VPN e circuiti ExpressRoute per [connettere](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) le reti virtuali tra loro e ai data center locali. I gateway VPN e i circuiti ExpressRoute richiedono una propria subnet da creare.
* **Appliance virtuali**. È possibile usare un'appliance virtuale, ad esempio un firewall, un acceleratore WAN o un gateway VPN in una rete virtuale di Azure. In questo caso, è necessario [instradare il traffico](virtual-networks-udr-overview.md) a tali appliance e isolarle nella rispettiva subnet.

### <a name="subnet-and-nsg-design-patterns"></a>Modelli di progettazione di subnet e gruppi di sicurezza di rete
La tabella seguente illustra alcuni modelli di progettazione comuni per l'uso di subnet.

| Scenario | Diagramma | Vantaggi | Svantaggi |
| --- | --- | --- | --- |
| Singola subnet, gruppi di sicurezza di rete per livello dell'applicazione, per applicazione |![Singola subnet](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Solo una subnet da gestire. |Sono necessari più gruppi di sicurezza di rete per isolare le applicazioni. |
| Una subnet per app, gruppi di sicurezza di rete per livello dell'applicazione |![Subnet per app](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Meno gruppi di sicurezza di rete da gestire. |Più subnet da gestire. |
| Una subnet per livello dell'applicazione, gruppi di sicurezza di rete per app. |![Subnet per livello](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Equilibrio tra numero di subnet e gruppi di sicurezza di rete. |Numero massimo di NSG per sottoscrizione. Per informazioni dettagliate, leggere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits). |
| Una subnet per livello dell'applicazione, per app, gruppi di sicurezza di rete per subnet |![Subnet per livello per app](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Probabile numero inferiore di gruppi di sicurezza di rete. |Più subnet da gestire. |

## <a name="sample-design"></a>Progettazione di esempio
Per illustrare l'applicazione delle informazioni in questo articolo, si consideri lo scenario seguente.

Si lavora per un'azienda con 2 data center in America del Nord e due data center in Europa. Vengono identificate 6 diverse applicazioni rivolte ai clienti gestite da 2 diverse business unit di cui si vuole eseguire la migrazione ad Azure come progetto pilota. L'architettura di base per le applicazioni è la seguente:

* App1, App2, App3 e App4 sono applicazioni Web ospitate su server Linux con Ubuntu in esecuzione. Ogni applicazione si connette a un server applicazioni separato che ospita servizi RESTful nei server Linux. I servizi RESTful si connettono a un database MySQL back-end.
* App5 e App6 sono applicazioni Web ospitate in Windows Server con Windows Server 2012 R2 in esecuzione. Ogni applicazione si connette a un database di SQL Server back-end.
* Tutte le app sono attualmente ospitate in uno dei data center dell'azienda in America del Nord.
* I data center locali usano lo spazio degli indirizzi 10.0.0.0/8.

È necessario progettare una soluzione di rete virtuale che soddisfi i requisiti seguenti:

* Ogni business unit non deve dipendere dal consumo di risorse di altre business unit.
* È consigliabile ridurre al minimo la quantità di reti virtuali e subnet per semplificare la gestione.
* Ogni business unit deve avere una singola rete virtuale di test/sviluppo usata per tutte le applicazioni.
* Ogni applicazione è ospitata in 2 diversi data center di Azure per continente (America del Nord ed Europa).
* Le applicazioni sono completamente isolate l'una dall'altra.
* Ogni applicazione è accessibile da parte dei clienti via Internet, tramite HTTP.
* Ogni applicazione è accessibile da parte degli utenti connessi ai data center locali tramite un tunnel crittografato.
* È consigliabile che la connessione ai data center locali usi dispositivi VPN esistenti.
* Il team responsabile della rete aziendale deve avere controllo completo sulla configurazione della rete virtuale.
* Gli sviluppatori in ogni business unit devono essere in grado solo di distribuire le VM a subnet esistenti.
* Verrà eseguita la migrazione di tutte le applicazioni in Azure, così come sono (sollevamento e spostamento).
* I database in ogni località devono essere replicati ad altre località di Azure una volta al giorno.
* Ogni applicazione deve usare 5 server Web front-end, 2 server applicazioni (se necessario) e 2 server database.

### <a name="plan"></a>Pianificazione
È consigliabile iniziare la progettazione della pianificazione rispondendo alla domanda nella sezione [Definire i requisiti](#Define-requirements) , come illustrato di seguito.

1. Quali località di Azure verranno usate per ospitare le reti virtuali?

    2 località in America del Nord e 2 località in Europa. È consigliabile scegliere le località in base alla posizione fisica dei data center locali esistenti. In questo modo la connessione dalle località fisiche ad Azure avrà una latenza maggiore.
2. È necessario che tali località di Azure siano in comunicazione tra loro?

    Sì. Questo perché i database devono essere replicati in tutte le località.
3. È necessario che le reti virtuali di Azure e i data center locali siano in comunicazione tra loro?

    Sì. Questo perché gli utenti connessi ai data center locali devono essere in grado di accedere alle applicazioni tramite un tunnel crittografato.
4. Quante VM IaaS sono necessarie per la soluzione?

    200 VM IaaS. App1, App2, App3 e App4 richiedono 5 server Web, 2 server applicazioni e 2 server database per ognuna. Questo equivale a un totale di 9 VM IaaS per applicazione o 36 VM IaaS. App5 e App6 richiedono 5 server Web e 2 server database. Questo equivale a un totale di 7 VM IaaS per applicazione o 14 VM IaaS. Per questo motivo, sono necessarie 50 VM IaaS per tutte le applicazioni in ogni area di Azure. Poiché è necessario usare 4 aree, occorrono 200 VM IaaS.

    È anche necessario mettere a disposizione alcuni server DNS in ogni rete virtuale o nei data center locali per risolvere il nome tra le VM IaaS di Azure e la rete locale.
5. È necessario isolare il traffico in base a gruppi di VM (ad esempio server Web front-end e server database back-end)?

    Sì. Le applicazioni devono essere completamente isolate l'una dall'altra e anche ogni livello dell'applicazione deve essere isolato.
6. È necessario controllare il flusso del traffico tramite appliance virtuali?

    No. È possibile usare appliance virtuali per un maggiore controllo del flusso del traffico, tra cui una registrazione più dettagliata del piano dati.
7. Gli utenti necessitano di diversi set di autorizzazioni per risorse diverse di Azure?

    Sì. Il team responsabile della rete deve avere controllo completo sulle impostazioni della rete virtuale, mentre gli sviluppatori devono solo essere in grado di distribuire le proprie VM a subnet preesistenti.

### <a name="design"></a>Progettazione
È consigliabile seguire la progettazione specificando sottoscrizioni, reti virtuali, subnet e gruppi di sicurezza di rete. I gruppi di sicurezza di rete verranno discussi qui, ma è consigliabile acquisire altre informazioni sui [gruppi di sicurezza di rete](virtual-networks-nsg.md) prima di completare la progettazione.

**Numero di sottoscrizioni e reti virtuali**

I requisiti seguenti sono correlati a sottoscrizioni e reti virtuali:

* Ogni business unit non deve dipendere dal consumo di risorse di altre business unit.
* È consigliabile ridurre al minimo la quantità di reti virtuali e subnet.
* Ogni business unit deve avere una singola rete virtuale di test/sviluppo usata per tutte le applicazioni.
* Ogni applicazione è ospitata in 2 diversi data center di Azure per continente (America del Nord ed Europa).

In base a tali requisiti, è necessaria una sottoscrizione per ogni business unit. In questo modo, il consumo di risorse di una business unit non influirà sui limiti per le altre business unit. E poiché si vuole ridurre al minimo il numero di reti virtuali, è consigliabile usare il modello che prevede **una sottoscrizione per business unit e due reti virtuali per gruppo di app** , come illustrato di seguito.

![Singola sottoscrizione](./media/virtual-network-vnet-plan-design-arm/figure9.png)

È anche necessario specificare lo spazio degli indirizzi per ogni rete virtuale. Poiché è necessaria la connettività tra i data center locali e le aree di Azure, lo spazio degli indirizzi usato per le reti virtuali di Azure non può essere in conflitto con la rete locale e lo spazio degli indirizzi usato da ogni rete virtuale non deve essere in conflitto con altre reti virtuali esistenti. È possibile usare gli spazi degli indirizzi nella tabella seguente per soddisfare questi requisiti.  

| **Sottoscrizione** | **Rete virtuale** | **Area di Azure** | **Spazio degli indirizzi** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |Stati Uniti occidentali |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Stati Uniti orientali |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Europa settentrionale |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europa occidentale |172.19.0.0/16 |
| BU1 |TestDevBU1 |Stati Uniti occidentali |172.20.0.0/16 |
| BU2 |TestDevBU2 |Stati Uniti occidentali |172.21.0.0/16 |
| BU2 |ProdBU2US1 |Stati Uniti occidentali |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Stati Uniti orientali |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Europa settentrionale |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europa occidentale |172.25.0.0/16 |

**Numero di subnet e gruppi di sicurezza di rete**

I requisiti seguenti sono correlati a subnet e gruppi di sicurezza di rete:

* È consigliabile ridurre al minimo la quantità di reti virtuali e subnet.
* Le applicazioni sono completamente isolate l'una dall'altra.
* Ogni applicazione è accessibile da parte dei clienti via Internet, tramite HTTP.
* Ogni applicazione è accessibile da parte degli utenti connessi ai data center locali tramite un tunnel crittografato.
* È consigliabile che la connessione ai data center locali usi dispositivi VPN esistenti.
* I database in ogni località devono essere replicati ad altre località di Azure una volta al giorno.

In base a tali requisiti, è possibile usare una subnet per ogni livello dell'applicazione e usare gruppi di sicurezza di rete per filtrare il traffico per ogni applicazione. In questo modo, sono sufficienti 3 subnet in ogni rete virtuale (front-end, livello dell'applicazione e livello dati) e un gruppo di sicurezza di rete per applicazione per ogni subnet. In questo caso, è consigliabile usare il modello di progettazione che prevede **una subnet per livello dell'applicazione, gruppi di sicurezza di rete per app** . La figura seguente illustra come usare il modello di progettazione che rappresenta la rete virtuale **ProdBU1US1** .

![Una subnet per livello, un gruppo di sicurezza di rete per applicazione per livello](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Tuttavia, è anche necessario creare una subnet aggiuntiva per la connettività VPN tra le reti virtuali e i data center locali. È anche necessario specificare lo spazio degli indirizzi per ogni subnet. La figura seguente illustra una soluzione di esempio per la rete virtuale **ProdBU1US1** . È necessario replicare questo scenario per ogni rete virtuale. Ogni colore rappresenta un'applicazione diversa.

![Rete virtuale di esempio](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controllo dell’accesso**

I requisiti seguenti sono correlati al controllo dell'accesso:

* Il team responsabile della rete aziendale deve avere controllo completo sulla configurazione della rete virtuale.
* Gli sviluppatori in ogni business unit devono essere in grado solo di distribuire le VM a subnet esistenti.

In base a tali requisiti, è possibile aggiungere utenti del team responsabile della rete al ruolo incorporato di **collaboratore di rete** in ogni sottoscrizione e creare un ruolo personalizzato per gli sviluppatori di applicazioni in ogni sottoscrizione, assegnando loro le autorizzazioni per aggiungere VM a subnet esistenti.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire una rete virtuale](virtual-networks-create-vnet-arm-template-click.md) in base a uno scenario.
* Comprendere come [bilanciare il carico](../load-balancer/load-balancer-overview.md) delle VM IaaS e [gestire il routing in più aree di Azure](../traffic-manager/traffic-manager-overview.md).
* Altre informazioni sui [gruppi di sicurezza di rete e su come pianificare e progettare](virtual-networks-nsg.md) una soluzione per gruppi di sicurezza di rete.
* Altre informazioni sulle [opzioni di connettività cross-premise e della rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).

