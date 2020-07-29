---
title: Panoramica di IPv6 per la rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Descrizione IPv6 di endpoint e percorsi dati IPv6 in una rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 5c175a1575a4efbdc2294412e3743e201d8c4bb1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653294"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Che cos'è IPv6 per la rete virtuale di Azure?

IPv6 per la rete virtuale di Azure consente di ospitare le applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. A causa dell'esaurimento degli indirizzi IPv4 pubblici, le nuove reti per la mobilità e l'Internet delle cose (IoT) sono spesso basate su IPv6. Anche le reti ISP e mobili consolidate vengono trasformate in IPv6. I servizi solo IPv4 possono trovarsi in uno svantaggio reale nei mercati esistenti ed emergenti. La connettività IPv4/IPv6 dual stack consente ai servizi ospitati in Azure di attraversare questo gap tecnologico con servizi dual stack disponibili a livello globale che si connettono prontamente con gli IPv4 esistenti e i nuovi dispositivi e reti IPv6.

La connettività IPv6 originale di Azure consente di fornire facilmente connettività Internet dual stack (IPv4/IPv6) per le applicazioni ospitate in Azure. Consente di distribuire in modo semplice le macchine virtuali con connettività IPv6 con bilanciamento del carico per le connessioni avviate sia in ingresso che in uscita. Questa funzionalità è ancora disponibile e sono disponibili altre informazioni [qui](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 per la rete virtuale di Azure è una funzionalità molto più completa per l'abilitazione di architetture delle soluzioni IPv6 complete da distribuire in Azure.


Il diagramma seguente illustra una distribuzione semplice dual stack (IPv4/IPv6) in Azure:

![Diagramma di distribuzione della rete IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantaggi

Vantaggi di IPv6 per la rete virtuale di Azure:

- Consente di espandere la copertura delle applicazioni ospitate in Azure in mercati mobili e di Internet delle cose in crescita.
- Le macchine virtuali IPv4/IPv6 dual stack offrono la massima flessibilità per la distribuzione del servizio. Una singola istanza del servizio può connettersi con client Internet compatibili con IPv4 e IPv6.
- Si basa su connettività IPv6 da macchina virtuale a Internet di Azure stabile e consolidata.
- Protezione per impostazione predefinita, poiché la connettività IPv6 a Internet viene stabilita solo quando viene richiesta in modo esplicito nella distribuzione.

## <a name="capabilities"></a>Capabilities

IPv6 per la rete virtuale di Azure include le funzionalità riportate di seguito:

- I clienti di Azure possono definire lo spazio indirizzi della rete virtuale IPv6 per soddisfare le esigenze delle applicazioni, dei clienti o per un'integrazione perfetta nello spazio IP locale.
- Le reti virtuali dual stack (IPv4 e IPv6) con subnet dual stack consentono alle applicazioni di connettersi alle risorse IPv4 e IPv6 nella propria rete virtuale o in Internet.
    > [!IMPORTANT]
    > Le subnet per IPv6 devono avere esattamente una dimensione di /64.  In questo modo si garantisce la compatibilità futura se si decide di abilitare il routing della subnet a una rete locale, poiché alcuni router possono accettare solo le route IPv6 /64.  
- Proteggere le risorse con le regole IPv6 per i gruppi di sicurezza di rete.
    - Inoltre, le protezioni DDoS (Distributed Denial of Service) della piattaforma Azure sono estese agli indirizzi IP pubblici con connessione Internet
- Personalizzare il routing del traffico IPv6 nella rete virtuale con le route definite dall'utente, soprattutto quando si usano appliance virtuali di rete per migliorare l'applicazione.
- Linux e macchine virtuali Windows possono usare IPv6 per la rete virtuale di Azure
- Supporto per [Load Balancer pubblico IPv6 standard](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) per la creazione di applicazioni resilienti e scalabili, tra cui:
    - Probe di integrità IPv6 facoltativo per determinare quali istanze del pool back-end sono integre e quindi possono ricevere nuovi flussi.
    - Le regola in uscita facoltative forniscono il controllo dichiarativo completo sulla connettività in uscita per ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche.
    - Più configurazioni front-end facoltative che consentono a un singolo bilanciamento del carico di usare più indirizzi IP pubblici IPv6; lo stesso protocollo e la stessa porta front-end possono essere riusati in tutti gli indirizzi front-end.
    - Le porte iPv6 facoltative possono essere riusate nelle istanze di back-end con la funzionalità *IP mobile* delle regole di bilanciamento del carico 
    - Nota: il bilanciamento del carico non esegue alcuna traslazione di protocollo (nessun NAT64). 
    - Nota: IPv6 può essere con carico bilanciato solo per l'interfaccia di rete primaria (NIC) nelle macchine virtuali di Azure. 
- Supporto per [Load Balancer interno IPv6 standard](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) per la creazione di applicazioni multilivello resilienti nelle reti virtuali di Azure.   
- Il supporto per Load Balancer pubblico IPv6 di base per la compatibilità con le distribuzioni legacy
- Gli [indirizzi IP pubblici IPv6 riservati e gli intervalli di indirizzi](ipv6-public-ip-address-prefix.md) offrono indirizzi IPv6 stabili e prevedibili, che semplificano la creazione di elenchi di elementi consentiti delle applicazioni ospitate in Azure per la società e i clienti.
- IP pubblico a livello di istanza fornisce la connettività Internet IPv6 direttamente alle singole macchine virtuali.
- [Aggiungere IPv6 alle distribuzioni solo IPv4 esistenti](ipv6-add-to-existing-vnet-powershell.md): questa funzionalità consente di aggiungere facilmente connettività IPv6 alle distribuzioni solo IPv4 esistenti senza la necessità di ricreare le distribuzioni.  Il traffico di rete IPv4 non è interessato da questo processo, quindi a seconda dell'applicazione e del sistema operativo, potrebbe essere possibile aggiungere IPv6 anche ai servizi attivi.    
- Consentire ai client Internet di accedere facilmente all'applicazione dual stack usando il protocollo scelto con il supporto DNS di Azure per i record IPv6 (AAAA). 
- Creare applicazioni dual stack che si adattano automaticamente al carico con i set di scalabilità di macchine virtuali con IPv6.
- [Peering di rete virtuale](virtual-network-peering-overview.md): all'interno del peering globale e regionale, consente di connettere facilmente le reti virtuali dual stack; gli endpoint IPv4 e IPv6 sulle macchine virtuali nelle reti con peering saranno in grado di comunicare tra loro. È anche possibile eseguire il peering dual stack con reti virtuali solo IPv4 mentre si esegue la transizione delle distribuzioni a dual stack. 
- La diagnostica e la risoluzione dei problemi IPv6 sono disponibili con metriche/avvisi del bilanciamento del carico e le funzionalità Network Watcher come l'acquisizione pacchetti, i log dei flussi NSG, la risoluzione dei problemi di connessione e il monitoraggio della connessione.   

## <a name="scope"></a>Scope
IPv6 per la rete virtuale di Azure è un set di funzionalità di base che consente ai clienti di ospitare applicazioni dual stack (IPv4 + IPv6) in Azure.  Si prevede di aggiungere il supporto IPv6 a più funzionalità di rete di Azure nel tempo e infine di offrire versioni dual stack dei servizi Azure PaaS, ma nel frattempo è possibile accedere a tutti i servizi Azure PaaS tramite gli endpoint IPv4 in macchine virtuali dual stack.   

## <a name="limitations"></a>Limitazioni
La versione corrente di IPv6 per la rete virtuale di Azure presenta le limitazioni seguenti:
- IPv6 per la rete virtuale di Azure è disponibile in tutte le aree commerciali globali di Azure e del governo degli Stati Uniti usando tutti i metodi di distribuzione.  
- I gateway ExpressRoute possono essere usati per il traffico solo IPv4 in una VNET con IPv6 abilitato.  Il supporto per il traffico IPv6 è presente nella roadmap.   
- NON è possibile usare i gateway VPN in una VNET con IPv6 abilitato, né direttamente né con peering con "UseRemoteGateway".
- La piattaforma Azure (AKS e così via) non supporta la comunicazione IPv6 per contenitori.  
- IPv6 può essere con carico bilanciato solo per l'interfaccia di rete primaria (NIC) nelle macchine virtuali di Azure. Il bilanciamento del carico del traffico IPv6 verso le schede di interfaccia di rete secondarie non è supportato.    
- Le macchine virtuali solo IPv6 o i set di scalabilità di macchine virtuali non sono supportati; ogni scheda di interfaccia di rete deve includere almeno una configurazione IP IPv4. 
- Quando si aggiunge IPv6 alle distribuzioni IPv4 esistenti, gli intervalli IPv6 non possono essere aggiunti a una rete virtuale con collegamenti di navigazione delle risorse esistenti.  
- Il DNS diretto per IPv6 è attualmente supportato per il DNS pubblico di Azure, ma il DNS inverso non è ancora supportato.   

## <a name="pricing"></a>Prezzi

Per le risorse e la larghezza di banda di Azure IPv6 vengono addebitate le stesse tariffe di IPv4. Per IPv6 non sono previsti addebiti aggiuntivi o diversi. Sono disponibili informazioni dettagliate sui prezzi per gli [indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/) o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire un'applicazione dual stack IPv6 usando Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Informazioni su come [distribuire un'applicazione dual stack IPv6 usando l'interfaccia della riga di comando di Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Informazioni su come [distribuire un'applicazione dual stack IPv6 usando i Modelli di Resource Manager (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
