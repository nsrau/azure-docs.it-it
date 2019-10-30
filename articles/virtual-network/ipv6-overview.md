---
title: Panoramica di IPv6 per rete virtuale di Azure (anteprima)
titlesuffix: Azure Virtual Network
description: Descrizione IPv6 di endpoint e percorsi di dati IPv6 in una rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 001a6d5ef742874698cd7a67014179a2f8528fc6
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053471"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Che cos'è IPv6 per la rete virtuale di Azure? (Anteprima)

IPv6 per rete virtuale di Azure (VNet) consente di ospitare le applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. A causa dell'esaurimento degli indirizzi IPv4 pubblici, le nuove reti per la mobilità e la Internet delle cose (tutto) sono spesso basate su IPv6. Anche le reti ISP e mobile stabilite a lungo vengono trasformate in IPv6. I servizi solo IPv4 possono trovarsi in uno svantaggio reale nei mercati esistenti ed emergenti. La connettività IPv4/IPv6 dual stack consente ai servizi ospitati in Azure di attraversare questo gap tecnologico con servizi dual stack disponibili a livello globale che si connettono prontamente con i dispositivi IPv4 e le nuove reti IPv6 esistenti.

La connettività IPv6 originale di Azure consente di fornire facilmente connettività Internet dual stack (IPv4/IPv6) per le applicazioni ospitate in Azure. Consente di distribuire in modo semplice le VM con connettività IPv6 con bilanciamento del carico per le connessioni avviate sia in ingresso che in uscita. Questa funzionalità è ancora disponibile e altre informazioni sono disponibili [qui](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 per la rete virtuale di Azure è molto più completo per l'abilitazione di architetture di soluzioni IPv6 complete da distribuire in Azure.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente disponibile in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il diagramma seguente illustra una distribuzione semplice a doppio stack (IPv4/IPv6) in Azure:

![Diagramma di distribuzione della rete IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantaggi

Vantaggi di IPv6 per Azure VNET:

- Consente di espandere la portata delle applicazioni ospitate in Azure in mercati mobili e Internet delle cose in crescita.
- Le VM IPv4/IPv6 dual stack offrono la massima flessibilità per la distribuzione del servizio. Una singola istanza del servizio può connettersi con client Internet compatibili con IPv4 e IPv6.
- Si basa su connettività IPv6 da macchina virtuale a Internet di Azure stabile e di lunga durata.
- Sicurezza per impostazione predefinita, poiché la connettività IPv6 a Internet viene stabilita solo quando viene richiesta in modo esplicito nella distribuzione.

## <a name="capabilities"></a>Capabilities

IPv6 per Azure VNet include le funzionalità seguenti:

- I clienti di Azure possono definire lo spazio di indirizzi della rete virtuale IPv6 per soddisfare le esigenze delle applicazioni, dei clienti o integrarsi facilmente nello spazio IP locale.
- Le reti virtuali dual stack (IPv4 e IPv6) con subnet dual stack consentono alle applicazioni di connettersi alle risorse IPv4 e IPv6 nella propria rete virtuale o in Internet.
    > [!IMPORTANT]
    > Le subnet per IPv6 devono avere dimensioni esattamente/64.  In questo modo si garantisce la compatibilità futura se si decide di abilitare il routing della subnet a una rete locale, poiché alcuni router possono accettare solo le route IPv6/64.  
- Proteggi le tue risorse con le regole IPv6 per i gruppi di sicurezza di rete.
    - E la protezione DDoS (Distributed Denial of Service) della piattaforma Azure è estesa agli indirizzi IP pubblici con connessione Internet
- Personalizzare il routing del traffico IPv6 nella rete virtuale con le route definite dall'utente, soprattutto quando si usano appliance virtuali di rete per migliorare l'applicazione.
- Linux e macchine virtuali Windows possono tutti usare IPv6 per Azure VNET
- Supporto di [Load Balancer pubblico IPv6 standard](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) per creare applicazioni resilienti e scalabili, tra cui:
    - Probe di integrità IPv6 facoltativo per determinare quali istanze del pool back-end sono integrità e quindi possono ricevere nuovi flussi.
    - Regole in uscita facoltative che offrono un controllo dichiarativo completo sulla connettività in uscita per scalare e ottimizzare questa capacità in base alle esigenze specifiche.
    - Più configurazioni front-end facoltative che consentono a un singolo servizio di bilanciamento del carico di usare più indirizzi IP pubblici IPv6. lo stesso protocollo e la stessa porta front-end possono essere riutilizzati in tutti gli indirizzi front-end.
    - Le porte IPv6 facoltative possono essere riutilizzate nelle istanze back-end usando la funzionalità *IP mobile* delle regole di bilanciamento del carico 
- Supporto [Load Balancer interno IPv6 standard](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) per creare applicazioni multilivello resilienti in Azure reti virtuali.  
- Supporto Load Balancer pubblico IPv6 di base per la compatibilità con le distribuzioni legacy
- [Gli indirizzi IP pubblici IPv6 riservati e gli intervalli di indirizzi](ipv6-public-ip-address-prefix.md) offrono indirizzi IPv6 stabili e prevedibili, che semplificano l'inserimento nell'elenco degli elementi consentiti delle applicazioni ospitate in Azure per la società e i clienti.
- IP pubblico a livello di istanza fornisce la connettività Internet IPv6 direttamente alle singole macchine virtuali.
- [Aggiungere IPv6 alle distribuzioni solo IPv4 esistenti](ipv6-add-to-existing-vnet-powershell.md): questa funzionalità consente di aggiungere facilmente connettività IPv6 alle distribuzioni solo IPv4 esistenti senza la necessità di ricreare le distribuzioni.  Il traffico di rete IPv4 non è interessato da questo processo, quindi, a seconda dell'applicazione e del sistema operativo, potrebbe essere possibile aggiungere IPv6 anche ai servizi Live.    
- Consentire ai client Internet di accedere facilmente all'applicazione dual stack usando il protocollo scelto con il supporto DNS di Azure per i record IPv6 (AAAA). 
- Creare applicazioni dual stack che si adattano automaticamente al carico con i set di scalabilità di macchine virtuali con IPv6.
- [Peering di rete virtuale (VNET)](virtual-network-peering-overview.md) , sia all'interno che al peering globale, che consente di connettere facilmente dual stack reti virtuali. gli endpoint IPv4 e IPv6 nelle macchine virtuali nelle reti con peering saranno in grado di comunicare tra loro. È anche possibile eseguire il peering dual stack con reti virtuali solo IPv4 mentre si esegue la transizione delle distribuzioni a dual stack. 
- La diagnostica e la risoluzione dei problemi IPv6 sono disponibili con le metriche del servizio di bilanciamento del carico/avvisi e le funzionalità Network Watcher come l'acquisizione di pacchetti, i log di flusso NSG, la risoluzione dei problemi di connessione e il monitoraggio della connessione   

## <a name="scope"></a>Scope
IPv6 per Azure VNET è un set di funzionalità di base che consente ai clienti di ospitare applicazioni dual stack (IPv4 + IPv6) in Azure.  Si prevede di aggiungere il supporto IPv6 a più funzionalità di rete di Azure nel tempo e infine di offrire versioni dual stack dei servizi PaaS di Azure, ma nel frattempo è possibile accedere a tutti i servizi PaaS di Azure tramite gli endpoint IPv4 in macchine virtuali a doppio stack.   

## <a name="limitations"></a>Limitazioni
La versione corrente di IPv6 per la rete virtuale di Azure presenta le limitazioni seguenti:
- IPv6 per rete virtuale di Azure (anteprima) è disponibile in tutte le aree di Azure globali, ma solo in Azure globale, non ancora nei cloud governativi.
- Non è possibile usare Express Route e i gateway VPN in una VNET con IPv6 abilitato, direttamente o con peering con "UseRemoteGateway". 
- La piattaforma Azure (AKS e così via) non supporta la comunicazione IPv6 per i contenitori.  

## <a name="pricing"></a>Prezzi

Per le risorse e la larghezza di banda di Azure IPv6 vengono addebitate le stesse tariffe di IPv4. Per IPv6 non sono previsti addebiti aggiuntivi o diversi. È possibile trovare informazioni dettagliate sui prezzi per [gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/)o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire un'applicazione IPv6 dual stack usando Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Informazioni su come [distribuire un'applicazione IPv6 dual stack usando l'interfaccia](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)della riga di comando di Azure.
- Informazioni su come [distribuire un'applicazione IPv6 dual stack usando i modelli di gestione risorse (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
