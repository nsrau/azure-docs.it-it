---
title: Architettura della connettività SD-WAN
titleSuffix: Azure Virtual WAN
description: Informazioni sull'interconnessione di una WAN SD privata con la rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 34b2282421b9c95ad99ad040cb11847a30d3b52c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199987"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Architettura di connettività SD-WAN con la rete WAN virtuale di Azure

WAN virtuale di Azure è un servizio di rete che riunisce molti servizi di sicurezza e connettività cloud con una singola interfaccia operativa. Questi servizi includono il ramo (tramite VPN da sito a sito), l'utente remoto (VPN da punto a sito), la connettività privata (ExpressRoute), la connettività transitiva intra-cloud per reti virtuali, l'interconnettività VPN e ExpressRoute, il routing, il firewall di Azure e la crittografia per la connettività privata.

Sebbene la rete WAN virtuale di Azure sia una WAN software defined (SD-WAN), è progettata anche per consentire l'interconnessione senza problemi con le tecnologie e i servizi basati su SD locali. Molti di questi servizi sono offerti dall'ecosistema [WAN virtuale](virtual-wan-locations-partners.md) e dai partner dei servizi gestiti di rete di Azure [(MSPS)](../networking/networking-partners-msp.md). Le aziende che trasformano la rete WAN privata in SD-WAN includono opzioni per l'interconnessione della rete WAN privata con la rete WAN virtuale di Azure. Le aziende possono scegliere tra le opzioni seguenti:

* Modello di interconnessione diretta
* Modello di interconnessione indiretta
* Modello WAN ibrido gestito con il provider di servizi gestiti preferito [msp](../networking/networking-partners-msp.md)

In tutti questi casi, l'interconnessione di WAN virtuale con SD-WAN è analoga a quella del lato connettività, ma può variare in base all'orchestrazione e al lato operativo.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modello di interconnessione diretta

![Modello di interconnessione diretta](./media/sd-wan-connectivity-architecture/direct.png)

In questo modello di architettura, il sito SD-WAN Branch Customer-locale Equipment (CPE) è connesso direttamente agli hub WAN virtuali tramite connessioni IPsec. Il CPE del ramo può anche essere connesso ad altri rami tramite la WAN SD privata o sfruttare la rete WAN virtuale per la connettività ramo a ramo. I rami che devono accedere ai carichi di lavoro in Azure saranno in grado di accedere in modo diretto e sicuro ad Azure tramite i tunnel IPsec terminati negli hub WAN virtuali.

I partner CPE SD-WAN possono abilitare l'automazione per automatizzare la connettività IPsec normalmente noiosa e soggetta a errori dai rispettivi dispositivi CPE. L'automazione consente al controller SD-WAN di comunicare con Azure tramite l'API WAN virtuale per configurare i siti WAN virtuali, nonché di effettuare il push della configurazione del tunnel IPsec necessaria al ramo CPEs. Vedere [linee guida di automazione](virtual-wan-configure-automation-providers.md) per la descrizione dell'automazione dell'interconnessione WAN virtuale da parte di diversi partner SD-WAN.

Il CPE SD-WAN continua a essere il luogo in cui viene implementata e applicata l'ottimizzazione del traffico e la selezione del percorso. 

In questo modello, alcune ottimizzazioni del traffico proprietarie del fornitore basate sulle caratteristiche di traffico in tempo reale potrebbero non essere supportate perché la connettività alla rete WAN virtuale è tramite IPsec e la VPN IPsec viene terminata nel gateway VPN WAN virtuale. Ad esempio, la selezione dinamica del percorso al ramo CPE è fattibile a causa del dispositivo del ramo che scambia varie informazioni sui pacchetti di rete con un altro nodo SD-WAN, di conseguenza identificando il collegamento migliore da usare per il traffico in ordine di priorità in modo dinamico al ramo. Questa funzionalità può essere utile nelle aree in cui è necessaria l'ottimizzazione dell'ultimo miglio (ramo al POP Microsoft più vicino).

Con la rete WAN virtuale, gli utenti possono ottenere la selezione dei percorsi di Azure, ovvero la selezione del percorso basato su criteri tra più collegamenti ISP dal ramo CPE ai gateway VPN WAN virtuali. La rete WAN virtuale consente l'installazione di più collegamenti (percorsi) dallo stesso ramo di SD-WAN CPE; ogni collegamento rappresenta una connessione a tunnel doppio da un indirizzo IP pubblico univoco del CPE SD-WAN a due istanze diverse del gateway VPN WAN virtuale di Azure. I fornitori SD-WAN possono implementare il percorso ottimale di Azure, in base ai criteri di traffico impostati dal motore dei criteri nei collegamenti CPE.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modello di interconnessione indiretta

![Modello di interconnessione indiretta](./media/sd-wan-connectivity-architecture/indirect.png)

In questo modello di architettura, il ramo SD-WAN CPEs è indirettamente connesso agli hub WAN virtuali. Come illustrato nella figura, un CPE virtuale SD-WAN viene distribuito in un VNet aziendale. Questo CPE virtuale è a sua volta connesso agli hub WAN virtuali tramite IPsec. Il CPE virtuale funge da Gateway SD-WAN in Azure. I rami che devono accedere ai carichi di lavoro in Azure saranno in grado di accedervi tramite il gateway v-CPE.

Poiché la connettività ad Azure è tramite il gateway v-CPE, tutto il traffico da e verso i reti virtuali di carico di lavoro di Azure ad altri rami SD-WAN passa attraverso l'appliance virtuale di rete. In questo modello, l'utente è responsabile della gestione e del funzionamento dell'appliance virtuale di rete SD, incluse la disponibilità elevata, la scalabilità e il routing.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modello WAN ibrido gestito

![Modello WAN ibrido gestito](./media/sd-wan-connectivity-architecture/hybrid.png)

In questo modello di architettura, le aziende possono sfruttare un servizio di SD-WAN gestito offerto da un partner del provider di servizi gestiti (MSP). Questo modello è simile ai modelli diretti o indiretti descritti in precedenza. Tuttavia, in questo modello, la progettazione, l'orchestrazione e le operazioni di SD-WAN vengono recapitate dal provider SD-WAN.

I [partner MSP di rete di Azure](../networking/networking-partners-msp.md) possono usare [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) per implementare il servizio WAN SD e il servizio WAN virtuale nella sottoscrizione di Azure del cliente aziendale, nonché per gestire la WAN ibrida end-to-end per conto del cliente. Questi MSPs possono anche essere in grado di implementare Azure ExpressRoute nella rete WAN virtuale e di usarlo come servizio gestito end-to-end.

## <a name="additional-information"></a>Informazioni aggiuntive

* [Includi domande frequenti](virtual-wan-faq.md)
* [Risoluzione della connettività remota](work-remotely-support.md)
