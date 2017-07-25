---
title: Panoramica di IPv6 per Azure Load Balancer | Microsoft Docs
description: Informazioni sul supporto IPv6 per Azure Load Balancer e le macchine virtuali con bilanciamento del carico.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 397f3e75830acf46b29ac864c77ed3160f4e01e4
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Panoramica di IPv6 per Azure Load Balancer

I servizi di bilanciamento del carico con connessione Internet possono essere distribuiti con un indirizzo IPv6. Oltre alla connettività IPv4, sono abilitate le funzionalità seguenti:

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
* La modifica del parametro IdleTimeout per IPv6 **non è attualmente supportata**. Il valore predefinito è quattro minuti.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come distribuire un servizio di bilanciamento del carico con IPv6.

* [Disponibilità di IPv6 per area](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando un modello](load-balancer-ipv6-internet-template.md)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuire un servizio di bilanciamento del carico con IPv6 usando l'interfaccia della riga di comando di Azure](load-balancer-ipv6-internet-cli.md)

