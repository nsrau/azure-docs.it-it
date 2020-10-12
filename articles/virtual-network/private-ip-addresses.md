---
title: Indirizzi IP privati in Azure
titlesuffix: Azure Virtual Network
description: Informazioni sugli indirizzi IP privati in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 13688b73c0cd73e13e407f1b75beb8dda61a754f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84172256"
---
# <a name="private-ip-addresses"></a>Indirizzi IP privati
Gli indirizzi IP privati consentono la comunicazione tra le risorse in Azure. 

Le risorse possono essere:
* Servizi di Azure, ad esempio:
    * Interfacce di rete di macchina virtuale
    * Servizi di bilanciamento del carico interno
    * Gateway applicazione
* In una [rete virtuale](virtual-networks-overview.md).
* Rete locale tramite un gateway VPN o un circuito ExpressRoute.

Gli indirizzi IP privati consentono la comunicazione con queste risorse senza l'uso di un indirizzo IP pubblico.

## <a name="allocation-method"></a>Metodo di allocazione

Azure assegna indirizzi IP privati alle risorse dall'intervallo di indirizzi della subnet della rete virtuale in cui si trova la risorsa.

Azure riserva i primi quattro indirizzi in ogni intervallo di indirizzi della subnet. Non è possibile assegnare gli indirizzi alle risorse. Ad esempio, se l'intervallo di indirizzi della subnet è 10.0.0.0/16, gli indirizzi 10.0.0.0-10.0.0.3 e 10.0.255.255 non sono disponibili. Gli indirizzi IP dell'intervallo di indirizzi della subnet possono essere assegnati a una sola risorsa alla volta. 

Esistono due metodi in cui viene fornito un indirizzo IP privato:

- **Dinamico**: Azure assegna l'indirizzo IP disponibile successivo non assegnato o non riservato nell'intervallo di indirizzi della subnet. Ad esempio, Azure assegna 10.0.0.10 a una nuova risorsa se gli indirizzi 10.0.0.4-10.0.0.9 sono già assegnati ad altre. 

    Il metodo di allocazione predefinito è quello dinamico. Una volta assegnati, gli indirizzi IP dinamici vengono rilasciati se un'interfaccia di rete è:
    
    * Eliminata
    * Riassegnata a una subnet diversa nella stessa rete virtuale.
    * Il metodo di allocazione viene modificato in statico e viene specificato un indirizzo IP diverso. 
    
    Quando si modifica il metodo di allocazione da dinamico a statico, per impostazione predefinita Azure assegna l'indirizzo assegnato dinamicamente precedente come indirizzo statico.

- **Statico**: si seleziona e si assegna l'indirizzo IP disponibile successivo non assegnato o non riservato nell'intervallo di indirizzi della subnet. 

    Ad esempio, l'intervallo di indirizzi di una subnet è 10.0.0.0/16 e gli indirizzi 10.0.0.4-10.0.0.9 sono assegnati ad altre risorse. È possibile assegnare qualsiasi indirizzo tra 10.0.0.10-10.0.255.254. Gli indirizzi statici vengono rilasciati solo in caso di eliminazione di un'interfaccia di rete. 
    
    Azure assegna l'indirizzo IP statico come IP dinamico quando viene modificato il metodo di allocazione. La riassegnazione si verifica anche se l'indirizzo non è il successivo disponibile nella subnet. L'indirizzo cambia quando l'interfaccia di rete viene assegnata a una subnet diversa.
    
    Per assegnare l'interfaccia di rete a una subnet diversa, è necessario modificare il metodo di allocazione da statico a dinamico. Assegnare l'interfaccia di rete a una subnet diversa, quindi impostare di nuovo il metodo di allocazione su statico. Assegnare un indirizzo IP dall'intervallo di indirizzi della nuova subnet.
    
## <a name="virtual-machines"></a>Macchine virtuali

Uno o più indirizzi IP privati vengono assegnati a una o più **interfacce di rete**. Le interfacce di rete vengono assegnate a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Per ogni indirizzo IP privato è possibile specificare il metodo di allocazione statico o dinamico.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Risoluzione del nome host DNS interno per le macchine virtuali

Per impostazione predefinita, le macchine virtuali di Azure vengono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) . È possibile configurare in modo esplicito i server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interna per le macchine virtuali che si trovano all'interno della stessa rete virtuale.

Un mapping per il nome host all'indirizzo IP privato di una macchina virtuale viene aggiunto ai server DNS gestiti da Azure. 

Viene eseguito il mapping di un nome host all'indirizzo IP primario dell'interfaccia di rete principale quando una macchina virtuale è:

* Più interfacce di rete
* Più indirizzi IP
* Entrambi

Le macchine virtuali configurate con DNS gestito da Azure risolvono i nomi host all'interno della stessa rete virtuale. Usare un server DNS personalizzato per risolvere i nomi host delle macchine virtuali in reti virtuali connesse.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Servizi di bilanciamento del carico interno e gateway applicazione

È possibile assegnare un indirizzo IP privato alla configurazione **front-end** di un:

* Servizio di [bilanciamento del carico interno di Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Gateway applicazione di Azure](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Questo indirizzo IP privato funge da endpoint interno. L'endpoint interno è accessibile solo alle risorse all'interno della rete virtuale e alle reti remote connesse. È possibile assegnare un indirizzo IP statico o dinamico.

## <a name="at-a-glance"></a>Riepilogo
La tabella seguente illustra la proprietà tramite la quale un indirizzo IP privato può essere associato a una risorsa. 

Vengono visualizzati anche i possibili metodi di allocazione che è possibile usare:

* Dynamic
* Statico

| Risorse di livello superiore | Associazione di indirizzi IP | Dynamic | Statico |
| --- | --- | --- | --- |
| Macchina virtuale |Interfaccia di rete |Sì |Sì |
| Bilanciamento del carico |Configurazione front-end |Sì |Sì |
| gateway applicazione |Configurazione front-end |Sì |Sì |

## <a name="limits"></a>Limiti
I limiti per gli indirizzi IP sono disponibili nel set completo di [limiti per la rete](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. I limiti sono classificati per area e per sottoscrizione. [Contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai limiti massimi in base alle esigenze aziendali.

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle [indirizzi IP pubblici in Azure](public-ip-addresses.md)
* [Come impostare un indirizzo IP statico privato nel portale di Azure](virtual-networks-static-private-ip-arm-pportal.md)
