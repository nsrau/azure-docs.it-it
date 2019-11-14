---
title: Panoramica di IPv6-Azure Load Balancer
description: Con questo percorso di apprendimento, iniziare a usare il supporto IPv6 per le macchine virtuali Azure Load Balancer e con bilanciamento del carico.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 07dd3ee507f31099e32f18143d2beedf76e83c36
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077015"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Panoramica di IPv6 per Azure Load Balancer


>[!NOTE] 
>Azure Load Balancer supporta due tipi diversi: Basic e Standard. Questo articolo illustra Load Balancer Basic. Per altre informazioni su Load Balancer Standard, vedere la [panoramica di Load Balancer Standard](load-balancer-standard-overview.md).

I bilanciamenti del carico con connessione Internet dello SKU Basic possono essere distribuiti con un indirizzo IPv6. Oltre alla connettività IPv4, sono abilitate le funzionalità seguenti:

* Connettività IPv6 nativa end-to-end tra client Internet pubblici e macchine virtuali di Azure tramite il servizio di bilanciamento del carico.
* Connettività IPv6 nativa end-to-end in uscita tra macchine virtuali e client Internet pubblici abilitati per IPv6.

L'immagine seguente illustra la funzionalità IPv6 per Azure Load Balancer.

![Azure Load Balancer con IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Dopo la distribuzione, un client Internet abilitato per IPv4 o IPv6 può comunicare con gli indirizzi (o nomi host) IPv4 o IPv6 pubblici dell'istanza di Azure Load Balancer con connessione Internet. Il servizio di bilanciamento del carico instrada i pacchetti IPv6 agli indirizzi IPv6 privati delle macchine virtuali tramite NAT (Network Address Translation). Il client Internet IPv6 non può comunicare direttamente con l'indirizzo IPv6 delle macchine virtuali.

## <a name="features"></a>Funzionalità

Il supporto IPv6 nativo per le macchine virtuali distribuite tramite Azure Resource Manager offre:

1. Servizi IPv6 con bilanciamento del carico per i client IPv6 in Internet
2. Endpoint IPv6 e IPv4 nativi nelle macchine virtuali ("dual stack")
3. Connessioni IPv6 native in ingresso e in uscita
4. I protocolli supportati, ad esempio TCP, UDP e HTTP(S), consentono un'ampia gamma di architetture di servizi

## <a name="benefits"></a>Vantaggi

Questa funzionalità offre i vantaggi principali seguenti:

* Soddisfare le normative che dettano che le nuove applicazioni siano accessibili solo ai client IPv6
* Consentire agli sviluppatori di app per dispositivi mobili e Internet delle cose (IOT) di usare macchine virtuali di Azure dual stack (IPv4+IPv6) per soddisfare le esigenze dei mercati mobile e IOT in espansione

## <a name="details-and-limitations"></a>Dettagli e limitazioni

Dettagli

* Il servizio DNS Azure contiene i record dei nomi AAAA sia per IPv4 che per IPv6 e risponde con entrambi i record per il servizio di bilanciamento del carico. Il client sceglie con quale indirizzo comunicare, IPv4 o IPv6.
* Quando una VM avvia una connessione a un dispositivo IPv6 pubblico connesso a Internet, l'indirizzo IPv6 di origine della VM è l'indirizzo di rete convertito (con Network Address Translation, NAT) nell'indirizzo IPv6 pubblico del servizio di bilanciamento del carico.
* Le macchine virtuali in esecuzione nel sistema operativo Linux devono essere configurate per ricevere un indirizzo IP IPv6 tramite DHCP. Molte delle immagini Linux nella raccolta di Azure sono già configurate per il supporto di IPv6 senza alcuna modifica. Per altre informazioni, vedere [Configurazione di DHCPv6 per VM Linux](load-balancer-ipv6-for-linux.md)
* Se si sceglie di usare un probe di integrità con il servizio di bilanciamento del carico, creare un probe IPv4 e usarlo con gli endpoint IPv4 e IPv6. Se il servizio nella macchina virtuale si arresta, gli endpoint IPv4 e IPv6 vengono esclusi dalla rotazione.

Limitazioni

* Non è possibile aggiungere regole di bilanciamento del carico IPv6 nel portale di Azure. Le regole possono essere create solo tramite il modello, l'interfaccia della riga di comando e PowerShell.
* Non è possibile aggiornare le macchine virtuali esistenti per l'uso di indirizzi IPv6. È necessario distribuire nuove macchine virtuali.
* Un singolo indirizzo IPv6 può essere assegnato a una singola interfaccia di rete in ogni macchina virtuale.
* Gli indirizzi IPv6 pubblici non possono essere assegnati a una macchina virtuale. Possono essere assegnati solo a un servizio di bilanciamento del carico.
* È possibile configurare la ricerca DNS inversa per gli indirizzi IPv6 indirizzi pubblici.
* Le macchine virtuali con gli indirizzi IPv6 non possono essere membri di un servizio cloud di Azure. Possono essere connesse a una rete virtuale di Azure e comunicare tra loro tramite gli indirizzi IPv4.
* Gli indirizzi IPv6 privati possono essere distribuiti nelle singole macchine virtuali di un gruppo di risorse, ma non possono essere distribuiti in un gruppo di risorse tramite set di scalabilità.
* Le macchine virtuali di Azure non possono connettersi tramite IPv6 ad altre macchine virtuali, altri servizi di Azure o dispositivi locali. Possono comunicare solo con Azure Load Balancer tramite IPv6. Possono tuttavia comunicare con queste altre risorse tramite IPv4.
* La protezione del gruppo di sicurezza di rete (NSG) per IPv4 è supportata nelle distribuzioni dual stack (IPv4 + IPv6). I gruppi di sicurezza di rete non si applicano agli endpoint IPv6.
* L'endpoint IPv6 nella macchina virtuale non viene esposto direttamente a Internet. Si trova dietro un servizio di bilanciamento del carico. Solo le porte specificate nelle regole del servizio di bilanciamento del carico sono accessibili tramite IPv6.
* La modifica del parametro IdleTimeout per IPv6 **è attualmente non supportata**. Il valore predefinito è quattro minuti.
* La modifica del parametro loadDistributionMethod per IPv6 **è attualmente non supportata**.
* Gli indirizzi IP IPv6 riservati (dove IPAllocationMethod = statico) sono **attualmente non supportati**.
* NAT64 (conversione di IPv6 in IPv4) non supportato.
* I bilanciamenti del carico con SKU standard non supportano attualmente indirizzi IPv6.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come distribuire un servizio di bilanciamento del carico con IPv6.

* [Disponibilità di IPv6 per area](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando un modello](load-balancer-ipv6-internet-template.md)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando l'interfaccia della riga di comando di Azure](load-balancer-ipv6-internet-cli.md)
