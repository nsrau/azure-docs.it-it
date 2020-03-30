---
title: Panoramica della rete virtuale IPv6 per Azure (anteprima)Overview of IPv6 for Azure Virtual Network (Preview)
titlesuffix: Azure Virtual Network
description: Descrizione IPv6 degli endpoint IPv6 e dei percorsi dati in una rete virtuale di Azure.IPv6 description of IPv6 endpoints and data paths in an Azure virtual network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780080"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Che cos'è IPv6 per la rete virtuale di Azure? (Anteprima)

IPv6 per la rete virtuale di Azure consente di ospitare applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. A causa dell'esaurimento degli indirizzi IPv4 pubblici, le nuove reti per la mobilità e l'Internet of Things (IoT) sono spesso costruite su IPv6. Anche le reti mobili e gli ISP e le reti mobili di lunga data vengono trasformati in IPv6. I servizi solo IPv4 possono trovarsi in una situazione di reale svantaggio sia nei mercati esistenti che in quelli emergenti. La connettività IPv4/IPv6 a doppio stack consente ai servizi ospitati da Azure di attraversare questa lacuna tecnologica con servizi dual stack disponibili a livello globale che si connettono facilmente sia con i dispositivi e le reti IPv4 esistenti che con questi nuovi dispositivi e reti IPv6.

Azure's original IPv6 connectivity makes it easy to provide dual stack (IPv4/IPv6) Internet connectivity for applications hosted in Azure. Consente una distribuzione semplice delle macchine virtuali con connettività IPv6 con carico bilanciato sia per le connessioni in ingresso che per le connessioni avviate in uscita. Questa funzione è ancora disponibile e ulteriori informazioni sono disponibili [qui](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 for Azure virtual network is much more full featured- enabling full IPv6 solution architectures to be deployed in Azure.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il diagramma seguente illustra una semplice distribuzione dual stack (IPv4/IPv6) in Azure:The following diagram depicts a simple dual stack (IPv4/IPv6) deployment in Azure:

![Diagramma di distribuzione della rete IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantaggi

Vantaggi di IPv6 per VNET di Azure:

- Consente di espandere la portata delle applicazioni ospitate in Azure nei mercati mobili e Internet of Things in crescita.
- Le dum IPv4/IPv6 duali con stack offrono la massima flessibilità di distribuzione del servizio. Una singola istanza del servizio può connettersi a client Internet compatibili con IPv4 e IPv6.
- Si basa sulla connettività IPv6 di Azure stabile e stabile e stabile.
- Sicuro per impostazione predefinita poiché la connettività IPv6 a Internet viene stabilita solo quando viene richiesta in modo esplicito nella distribuzione.

## <a name="capabilities"></a>Capabilities

IPv6 per la rete virtuale di Azure include le funzionalità seguenti:IPv6 for Azure VNet includes the following capabilities:

- I clienti di Azure possono definire il proprio spazio di indirizzi di rete virtuale IPv6 per soddisfare le esigenze delle applicazioni, dei clienti o integrarsi perfettamente nello spazio IP locale.
- Le reti virtuali Dual Stack (IPv4 e IPv6) con subnet dual stack consentono alle applicazioni di connettersi con risorse IPv4 e IPv6 nella propria rete virtuale o - Internet.
    > [!IMPORTANT]
    > Le subnet per IPv6 devono avere esattamente /64.  In questo modo si garantisce la compatibilità futura se si decide di abilitare il routing della subnet a una rete locale poiché alcuni router possono accettare solo route IPv6 /64.  
- Proteggi le tue risorse con le regole IPv6 per i gruppi di sicurezza di rete.
    - Inoltre, le protezioni DDoS (Distributed Denial of Service) della piattaforma Azure sono estese
- Personalizzare il routing del traffico IPv6 nella rete virtuale con route definite dall'utente, in particolare quando si utilizzano le appliance virtuali di rete per aumentare l'applicazione.
- Linux and Windows Virtual Machines can all use IPv6 for Azure VNET
- [Load Balancer pubblico IPv6 standard](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) supporta la creazione di applicazioni scalabili resilienti, tra cui:Standard IPv6 public Load Balancer support to create resilient, scalable applications, which include:
    - Probe di integrità IPv6 facoltativo per determinare quali istanze del pool back-end sono di integrità e pertanto possono ricevere nuovi flussi.
    - Regole in uscita facoltative che forniscono il controllo dichiarativo completo sulla connettività in uscita per ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche.
    - Configurazioni front-end facoltative che consentono a un singolo servizio di bilanciamento del carico di utilizzare più indirizzi IP pubblici IPv6: lo stesso protocollo e la stessa porta front-end possono essere riutilizzate tra gli indirizzi front-end.
    - Le porte IPv6 facoltative possono essere riutilizzate nelle istanze back-end utilizzando la funzionalità IP mobile delle regole di bilanciamento del caricoOptional IPv6 ports can be reused on backend instances using the *Floating IP* feature of load-balancing rules 
- Supporto del [bilanciamento del carico interno IPv6 standard](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) per creare applicazioni multilivello resilienti all'interno delle VNET di Azure.Standard IPv6 internal Load Balancer support to create resilient multi-tier applications within Azure VNETs.  
- Supporto di Basic IPv6 public Load Balancer per la compatibilità con le distribuzioni legacyBasic IPv6 public Load Balancer support for compatibility with legacy deployments
- [Gli indirizzi IP pubblici IPv6 riservati e gli intervalli](ipv6-public-ip-address-prefix.md) di indirizzi forniscono indirizzi IPv6 stabili e prevedibili che semplificano l'inserimento nell'elenco degli elenchi di applicazioni ospitate in azzurri per l'azienda e i clienti.
- L'IP pubblico a livello di istanza fornisce la connettività Internet IPv6 direttamente alle singole macchine virtuali.
- [Aggiungere IPv6 alle distribuzioni esistenti solo IPv4:](ipv6-add-to-existing-vnet-powershell.md)questa funzionalità consente di aggiungere facilmente la connettività IPv6 alle distribuzioni esistenti solo IPv4 senza la necessità di ricreare le distribuzioni.  Il traffico di rete IPv4 non viene influenzato durante questo processo, pertanto, a seconda dell'applicazione e del sistema operativo, è possibile aggiungere IPv6 anche ai servizi attivi.    
- Consenti ai client Internet di accedere senza problemi all'applicazione dual stack usando il protocollo preferito con il supporto DNS di Azure per i record IPv6 (AAAA). 
- Crea applicazioni dual stack che vengono scalate automaticamente al carico con set di scalabilità di macchine virtuali con IPv6.
- [Il peering di rete virtuale (VNET),](virtual-network-peering-overview.md) sia all'interno del peering che a quello globale, consente di connettere in modo apparentemente le reti vNET dual stack, sia gli endpoint IPv4 che IPv6 nelle macchine virtuali nelle reti con peering. È anche possibile eseguire il peering dual stack con VNET solo IPv4 durante la transizione delle distribuzioni a dual stack. 
- La risoluzione dei problemi e la diagnostica IPv6 sono disponibili con metriche/avvisi del bilanciamento del carico e funzionalità di Network Watcher, ad esempio l'acquisizione di pacchetti, i log di flusso del gruppo di sicurezza di rete, la risoluzione dei problemi di connessione e il monitoraggio delle connessioni.   

## <a name="scope"></a>Scope
IPv6 per la rete virtuale di Azure è un set di funzionalità fondamentale che consente ai clienti di ospitare applicazioni dual stack (IPv4-IPv6) in Azure.IPv6 for Azure VNET is a foundational feature set which enables customers to host dual stack (IPv4-IPv6) applications in Azure.  Intendiamo aggiungere il supporto IPv6 a più funzionalità di rete di Azure nel tempo e alla fine offrire versioni dual stack dei servizi di Azure PaaS, ma nel frattempo tutti i servizi di Azure PaaS sono accessibili tramite gli endpoint IPv4 nelle macchine virtuali dual stack.   

## <a name="limitations"></a>Limitazioni
La versione corrente di IPv6 per la rete virtuale di Azure presenta le limitazioni seguenti:The current IPv6 for Azure virtual network release has the following limitations:
- IPv6 per la rete virtuale di Azure (anteprima) è disponibile in tutte le aree di Azure globali, ma solo in Global Azure, non ancora nei cloud di enti pubblici.
- I gateway ExpressRoute e VPN non possono essere utilizzati in una rete virtuale con IPv6 abilitato, direttamente o sottoposto a peerè con "UseRemoteGateway". 
- La piattaforma Azure (AKS e così via) non supporta la comunicazione IPv6 per i contenitori.  

## <a name="pricing"></a>Prezzi

IPv6 Azure resources and bandwidth are charged at the same rates as IPv4. Non sono previsti costi aggiuntivi o diversi per IPv6. È possibile trovare informazioni dettagliate sui prezzi per [gli indirizzi IP pubblici,](https://azure.microsoft.com/pricing/details/ip-addresses/)la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/)o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire un'applicazione dual stack IPv6 con Azure PowerShell.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)
- Informazioni su come [distribuire un'applicazione dual stack IPv6 usando l'interfaccia della riga di comando](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)di Azure.
- Informazioni su come [distribuire un'applicazione dual stack IPv6 usando i modelli di Resource Manager (JSON)Learn how to deploy an IPv6 dual stack application using Resource Manager Templates (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
