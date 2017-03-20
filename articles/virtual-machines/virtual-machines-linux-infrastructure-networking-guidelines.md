---
title: Linee guida sull&quot;infrastruttura di rete di Azure - Linux| Documentazione Microsoft
description: Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di una rete virtuale nei servizi di infrastruttura di Azure.
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7ebd5da-3c62-45e8-ad90-6c73a37ebeb1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 1faab292ec93c2138eed2efd977a0bdecbb4574b
ms.lasthandoff: 03/03/2017


---
# <a name="azure-networking-infrastructure-guidelines-for-linux-vms"></a>Linee guida sull'infrastruttura di rete di Azure per macchine virtuali Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dei passaggi necessari per la pianificazione della rete virtuale in Azure e sulla connettività tra ambienti locali esistenti.

## <a name="implementation-guidelines-for-virtual-networks"></a>Linee guida di implementazione per reti virtuali
Decisioni:

* Quale tipo di rete virtuale è necessaria per funzionare da host per l’infrastruttura o il carico di lavoro IT (solo cloud o cross-premise)?
* Per le reti virtuali cross-premise, qual è la quantità di spazio degli indirizzi necessaria per funzionare da host per le subnet e le macchine virtuali ora e quale invece per l'espansione ragionevole in futuro?
* Si intende creare reti virtuali centralizzate o reti virtuali singole per ogni gruppo di risorse?

Attività:

* Definire lo spazio degli indirizzi per le reti virtuali che si desidera creare.
* Definire il set di subnet e lo spazio degli indirizzi per ognuno.
* Per le reti virtuali cross-premise, definire il set di spazi degli indirizzi della rete locale per le sedi locali che devono essere raggiunte dalle macchine virtuali nella rete virtuale.
* Collaborare con il team della rete locale per assicurare la configurazione del routing appropriato se si creano reti virtuali cross-premise.
* Creare la rete virtuale usando la convenzione di denominazione scelta.

## <a name="virtual-networks"></a>Reti virtuali
Le reti virtuali sono necessarie per supportare le comunicazioni tra macchine virtuali. Come avviene con le reti fisiche, è possibile definire subnet, indirizzo IP personalizzato, impostazioni DNS, filtro di protezione e bilanciamento del carico. Usando un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o un [circuito ExpressRoute](../expressroute/expressroute-introduction.md), è possibile connettere le reti virtuali di Azure alle reti locali. Altre informazioni sulle [reti virtuali e sui relativi componenti](../virtual-network/virtual-networks-overview.md).

L'uso dei gruppi di risorse offre maggiore flessibilità alla progettazione dei componenti di rete virtuali. Le macchine virtuali possono connettersi alle reti virtuali esterne al proprio gruppo di risorse. Un approccio di progettazione diffuso prevede la creazione di gruppi di risorse centralizzati che contengono l'infrastruttura di rete di base e possono essere gestiti da un team comune. Le VM e le relative applicazioni sono distribuite a gruppi di risorse distinti. Questo approccio consente ai proprietari delle applicazioni di accedere al gruppo di risorse che contiene le proprie VM senza dover aprire l'accesso alla configurazione delle più vaste risorse della rete virtuale.

## <a name="site-connectivity"></a>Connettività del sito
### <a name="cloud-only-virtual-networks"></a>Reti virtuali solo cloud
Se utenti e computer locali non richiedono connettività costante alle VM in una rete virtuale Azure, progettare la rete virtuale è semplice:

![Diagramma rete virtuale di base solo cloud](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Questo approccio è in genere indicato per i carichi di lavoro con connessione Internet, come nel caso di un server Web basato su Internet. È possibile gestire queste macchine virtuali tramite SSH o connessioni VPN da punto a sito.

Poiché non si connettono alla rete locale, le reti virtuali solo Azure possono usare qualsiasi parte dello spazio di indirizzi IP privato. Lo spazio degli indirizzi può essere lo stesso spazio privato in uso a livello locale.

### <a name="cross-premises-virtual-networks"></a>Reti virtuali cross-premise
Se utenti e computer locali richiedono una connettività costante alle VM in una rete virtuale di Azure, creare una rete virtuale cross-premise. Connettere la rete virtuale alla rete locale con ExpressRoute o una connessione VPN da sito a sito.

![Diagramma rete virtuale cross-premise](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

In questa configurazione, la rete virtuale di Azure è essenzialmente un'estensione della rete locale basata su cloud.

Poiché si connettono alla rete locale, le reti virtuali cross-premise devono usare una parte dello spazio di indirizzi univoco usato dall'organizzazione. Così come diverse sedi aziendali vengono assegnate a una subnet IP specifica, con l'estensione della rete, Azure diventa un'altra sede.

Per consentire ai pacchetti di spostarsi dalla rete virtuale cross-premise alla rete locale, è necessario configurare il set di prefissi di indirizzo locali pertinenti come parte della definizione della rete locale per la rete virtuale. A seconda lo spazio di indirizzi della rete virtuale e del set di percorsi locali pertinenti, è possibile che nella rete locale siano presenti molti prefissi di indirizzo.

È possibile convertire una rete virtuale solo cloud in una rete virtuale cross-premise, ma più probabilmente è necessario riassegnare gli indirizzi IP dello spazio di indirizzi della rete virtuale e le risorse di Azure. Quando si assegna una subnet IP è pertanto necessario valutare attentamente se connettere una rete virtuale alla rete locale.

## <a name="subnets"></a>Subnet
Le subnet consentono di organizzare le risorse correlate, logicamente (ad esempio, una subnet per le macchine virtuali associate alla stessa applicazione) o fisicamente (ad esempio, una subnet per il gruppo di risorse). È anche possibile ricorrere a tecniche di isolamento subnet per una maggiore sicurezza.

Per le reti virtuali cross-premise, è necessario progettare delle subnet con le stesse convenzioni usate per le risorse locali. **Azure usa sempre i primi tre indirizzi IP dello spazio di indirizzi per ogni subnet**. Per determinare il numero di indirizzi necessari per la subnet, stabilire per prima cosa il numero di VM necessarie. Eseguire una stima della crescita futura e quindi usare la seguente tabella per determinare le dimensioni della subnet.

| Numero di macchine virtuali necessarie | Numero di bit di host necessari | Dimensioni della subnet |
| --- | --- | --- |
| 1-3 |3 |/29 |
| 4–11 |4 |/28 |
| 12–27 |5 |/27 |
| 28–59 |6 |/26 |
| 60–123 |7 |/25 |

> [!NOTE]
> Per le normali subnet locali, il numero massimo di indirizzi host per una subnet con n bit di host è 2<sup>n</sup> – 2. Per una subnet Azure, il numero massimo di indirizzi host per una subnet con n bit di host è 2<sup>n</sup> – 5 (2 più 3 per gli indirizzi che Azure utilizza in ogni subnet).
> 
> 

Se la subnet è troppo piccola, sarà necessario riassegnare gli indirizzi IP e ridistribuire le VM al suo interno.

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
È possibile applicare regole di filtro per il traffico che passa attraverso le reti virtuali usando i gruppi di sicurezza di rete. È possibile creare regole di filtro granulari per proteggere l'ambiente di rete virtuale, controllare il traffico in ingresso e in uscita, gli intervalli IP di origine e destinazione, le porte consentite e così via. I gruppi di sicurezza di rete possono essere applicati alle subnet all'interno di una rete virtuale o direttamente a una determinata interfaccia di rete virtuale. È consigliabile configurare un livello di filtraggio del traffico per il gruppo di sicurezza di rete sulle reti virtuali. Altre informazioni sulle [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

## <a name="additional-network-components"></a>Componenti di rete aggiuntivi
Come accade in un'infrastruttura di rete locale fisica, la rete virtuale di Azure non contiene necessariamente solo subnet e indirizzi IP. Quando si progetta l'infrastruttura dell'applicazione, è possibile includere alcuni di questi componenti aggiuntivi:

* [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) : connettere le reti virtuali di Azure ad altre reti virtuali di Azure oppure connettersi a reti locali tramite una connessione VPN da sito a sito. Per connessioni dedicate e sicure, implementare connessioni di Express Route. È anche possibile offrire l'accesso diretto agli utenti con connessioni VPN da punto a sito.
* [Bilanciamento del carico](../load-balancer/load-balancer-overview.md) - Offre il bilanciamento del carico del traffico per il traffico interno ed esterno in base alle esigenze.
* [Gateway applicazione](../application-gateway/application-gateway-introduction.md) : il bilanciamento del carico HTTP al livello dell'applicazione offre vantaggi aggiuntivi per le applicazioni Web, rispetto alla distribuzione del servizio di bilanciamento del carico di Azure.
* [Gestione traffico](../traffic-manager/traffic-manager-overview.md) - Distribuzione del traffico basata su DNS per indirizzare gli utenti finali all'endpoint dell'applicazione disponibile più vicino, così che l'host in uscita dell'applicazione possa risiedere in data center di Azure in aree diverse.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


