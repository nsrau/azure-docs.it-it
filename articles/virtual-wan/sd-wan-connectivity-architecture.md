---
title: Architettura della connettività SD-WAN
titleSuffix: Azure Virtual WAN
description: Informazioni sull'interconnessione di una rete SD-WAN privata con la rete WAN virtuale di Azure
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: sukishen
ms.openlocfilehash: 7b134e8ef4e09f1506f1d548ffb7579dcf65fbdd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753321"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Architettura della connettività SD-WAN con la rete WAN virtuale di Azure

La rete WAN virtuale di Azure è un servizio di rete che riunisce molti servizi di sicurezza e connettività cloud con una singola interfaccia operativa. Questi servizi includono il ramo (tramite VPN da sito a sito), l'utente remoto (VPN da punto a sito), la connettività privata (ExpressRoute), la connettività transitiva intra-cloud per reti virtuali, l'interconnettività VPN ed ExpressRoute, il routing, Firewall di Azure e la crittografia per la connettività privata.

Anche se la rete WAN virtuale di Azure è di per sé una rete WAN definita dal software (SD-WAN), è stata progettata anche per facilitare l'interconnessione con le tecnologie e i servizi SD-WAN locali. Molti di questi servizi sono offerti dall'ecosistema della [rete WAN virtuale](virtual-wan-locations-partners.md) di Microsoft e dai partner dei servizi gestiti [(MSP)](../networking/networking-partners-msp.md) della Rete di Azure. Le aziende che trasformano la rete WAN privata in SD-WAN possono scegliere tra varie opzioni per interconnettere la rete SD-WAN privata con la rete WAN virtuale di Azure. Sono disponibili le opzioni seguenti:

* Modello di interconnessione diretta
* Modello di interconnessione indiretta
* Modello WAN ibrido gestito con il provider di servizi gestiti ([MSP](../networking/networking-partners-msp.md)) preferito

In tutti questi casi, l'interconnessione della rete WAN virtuale con SD-WAN è simile dal punto di vista della connettività, ma può variare per quanto riguarda l'orchestrazione e il lato operativo.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modello di interconnessione diretta

![Modello di interconnessione diretta](./media/sd-wan-connectivity-architecture/direct.png)

In questo modello di architettura, l'apparecchiatura CPE (Customer-Premises Equipment) del ramo SD-WAN è connessa direttamente agli hub di rete WAN virtuale tramite connessioni IPsec. Tale apparecchiatura può anche essere connessa ad altri rami tramite la rete SD-WAN privata o può sfruttare la rete WAN virtuale per la connettività da ramo a ramo. I rami che devono accedere ai carichi di lavoro in Azure saranno in grado di accedere in modo diretto e sicuro ad Azure tramite uno o più tunnel IPsec terminati in uno o più hub di rete WAN virtuale.

I partner CPE SD-WAN possono abilitare l'automazione per la connettività IPsec, normalmente tediosa e soggetta a errori, dai rispettivi dispositivi CPE. L'automazione consente al controller SD-WAN di comunicare con Azure tramite l'API WAN virtuale per configurare i siti WAN virtuali, nonché di effettuare il push della configurazione del tunnel IPsec necessaria ai dispositivi CPE dei rami. Per la descrizione dell'automazione dell'interconnessione di rete WAN virtuale da parte di diversi partner SD-WAN, vedere le [linee guida per l'automazione](virtual-wan-configure-automation-providers.md).

L'apparecchiatura CPE SD-WAN continua a essere il punto in cui vengono implementate e applicate l'ottimizzazione del traffico e la selezione del percorso. 

In questo modello, alcune ottimizzazioni del traffico proprietarie del fornitore, basate su caratteristiche di traffico in tempo reale, potrebbero non essere supportate perché la connettività alla rete WAN virtuale avviene tramite IPsec e la VPN IPsec viene terminata nel gateway VPN della rete WAN virtuale. Ad esempio, la selezione dinamica del percorso nel dispositivo CPE del ramo è possibile grazie allo scambio di informazioni sui pacchetti di rete tra il dispositivo del ramo e un altro nodo SD-WAN, che consente di identificare, dinamicamente nel ramo, il collegamento ottimale da usare per il traffico in base a criteri di priorità. Questa funzionalità può essere utile nelle aree in cui è necessaria l'ottimizzazione dell'ultimo miglio (il ramo verso il POP Microsoft più vicino).

Con la rete WAN virtuale, gli utenti possono sfruttare la funzionalità di selezione del percorso di Azure, ovvero la selezione del percorso basata su criteri tra più collegamenti ISP dal dispositivo CPE del ramo ai gateway VPN della rete WAN virtuale. La rete WAN virtuale consente la configurazione di più collegamenti (percorsi) dallo stesso dispositivo CPE del ramo SD-WAN. Ogni collegamento rappresenta una connessione a doppio tunnel da un indirizzo IP pubblico univoco del dispositivo CPE SD-WAN a due istanze diverse del gateway VPN della rete WAN virtuale di Azure. I fornitori di reti SD-WAN possono implementare il percorso ottimale verso Azure, in base ai criteri di traffico impostati dal motore dei criteri nei collegamenti CPE. Sul lato di Azure, tutte le connessioni in arrivo vengono gestite allo stesso modo.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modello di interconnessione indiretta

![Modello di interconnessione indiretta](./media/sd-wan-connectivity-architecture/indirect.png)

In questo modello di architettura, i dispositivi CPE dei rami SD-WAN sono connessi indirettamente agli hub di rete WAN virtuale. Come illustrato nella figura, un dispositivo CPE virtuale SD-WAN viene distribuito in una rete virtuale aziendale. Il dispositivo CPE virtuale è a sua volta connesso a uno o più hub di rete WAN virtuale tramite IPsec e funge da gateway SD-WAN verso Azure. I rami che devono accedere ai carichi di lavoro in Azure potranno farlo tramite il gateway v-CPE.

Poiché la connettività ad Azure avviene tramite il gateway v-CPE (appliance virtuale di rete), tutto il traffico tra le reti virtuali che gestiscono il carico di lavoro di Azure e altri rami SD-WAN passa attraverso l'appliance virtuale di rete. In questo modello, l'utente è responsabile della gestione e del funzionamento dell'appliance virtuale di rete SD-WAN, inclusi i requisiti di disponibilità elevata, scalabilità e routing.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modello di rete WAN ibrido gestito

![Modello di rete WAN ibrido gestito](./media/sd-wan-connectivity-architecture/hybrid.png)

In questo modello di architettura, le aziende possono sfruttare un servizio SD-WAN gestito offerto da un partner provider di servizi gestiti (MSP). Questo modello è simile ai modelli diretti o indiretti descritti in precedenza. In questo caso, tuttavia, la progettazione, l'orchestrazione e le operazioni della rete SD-WAN vengono fornite dal provider SD-WAN.

I [partner MSP della Rete di Azure](../networking/networking-partners-msp.md) possono usare [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) per implementare il servizio di rete SD-WAN e WAN virtuale nella sottoscrizione di Azure del cliente aziendale, nonché per gestire la rete WAN ibrida end-to-end per conto del cliente. Questi MSP possono anche essere in grado di implementare Azure ExpressRoute nella rete WAN virtuale e usarlo come servizio gestito end-to-end.

## <a name="additional-information"></a>Informazioni aggiuntive

* [Domande frequenti](virtual-wan-faq.md)
* [Risoluzione della connettività remota](work-remotely-support.md)
