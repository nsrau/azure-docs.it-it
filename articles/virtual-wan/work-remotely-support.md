---
title: Rete WAN virtuale di Azure ed esecuzione remota
description: Questa pagina descrive come è possibile sfruttare la rete WAN virtuale di Azure per abilitare l'uso in remoto a causa della pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337137"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Rete WAN virtuale di Azure e supporto per lavoro remoto

>[!NOTE]
>Questo articolo descrive come sfruttare la rete WAN virtuale di Azure, Azure, Microsoft Network e l'ecosistema di partner di Azure per lavorare in modalità remota e mitigare i problemi di rete a cui si è connessi a causa di una crisi COVID-19.
>

Si sta rimescolando per fornire connettività agli utenti remoti?
Si noterà improvvisamente la necessità di supportare un sovraccarico di utenti oltre a quello pianificato?
È necessario che l'utente si connetta da casa e non solo accede al cloud, ma anche in grado di raggiungere l'ambiente locale?
È necessario che gli utenti remoti siano in grado di raggiungere le risorse dietro una rete WAN privata?
È necessario che gli utenti accedano alle risorse tra cloud senza che sia necessario configurare la connettività tra le aree?
Poiché questa pandemia globale crea modifiche senza precedenti, il team WAN virtuale di Azure è qui per soddisfare le esigenze di connettività.

La rete WAN virtuale di Azure è un servizio di rete che offre numerose funzionalità di rete, sicurezza e routing per offrire una singola interfaccia operativa. Queste funzionalità includono la connettività dei rami (tramite l'automazione della connettività da dispositivi partner WAN virtuali, ad esempio il CPE SD o VPN), connettività VPN da sito a sito, VPN utente remoto (da punto a sito), connettività privata (ExpressRoute), connettività intra-cloud (connettività transitiva per reti virtuali), connettività ExpressRoute VPN, routing, firewall di Azure, crittografia per la connettività privata e così via. Non è necessario avere tutti questi casi d'uso per iniziare a usare la rete WAN virtuale. È possibile iniziare con un solo caso di utilizzo e regolare la rete quando si evolve.

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan1.png)

A questo punto, è possibile esaminare gli elementi necessari per far funzionare la rete:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configurare la connettività utente remoto

È possibile connettersi alle risorse in Azure tramite una connessione IPsec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client VPN per l'utente remoto. Questo client può essere il client [VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554) o il client OpenVPN o qualsiasi client che supporti IKEv2. Per altre informazioni, vedere [Creare una connessione da punto a sito](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Connettività dall'utente remoto al sito locale

Sono disponibili due opzioni:

* Configurare la connettività da sito a sito con qualsiasi dispositivo VPN esistente. Quando si connette il dispositivo VPN IPsec all'hub WAN virtuale di Azure, l'interconnettività tra la VPN utente da punto a sito (utente remoto) e la VPN da sito a sito è automatica. Per altre informazioni su come configurare una VPN da sito a sito dal dispositivo VPN locale alla rete WAN virtuale di Azure, vedere [creare una connessione da sito a sito con la rete WAN virtuale](virtual-wan-site-to-site-portal.md).

* Connettere il circuito ExpressRoute all'hub WAN virtuale. Per la connessione di un circuito ExpressRoute è necessario distribuire un gateway ExpressRoute nella rete WAN virtuale. Non appena ne è stata distribuita una, l'interconnettività tra la VPN utente da punto a sito e l'utente ExpressRoute è automatica. Per creare la connessione ExpressRoute, vedere [creare una connessione ExpressRoute usando la rete WAN virtuale](virtual-wan-expressroute-portal.md). È possibile usare un circuito ExpressRoute esistente per connettersi alla rete WAN virtuale di Azure.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente WAN virtuale di base esistente

La rete WAN virtuale di base fornisce solo VPN da sito a sito. Per consentire agli utenti remoti di connettersi, sarà necessario aggiornare la rete WAN virtuale alla rete WAN virtuale standard. Per i passaggi necessari per aggiornare una rete WAN virtuale, vedere [aggiornare una rete WAN virtuale da Basic a standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Informazioni aggiuntive

La rete WAN virtuale supporta un hub per area/località. Per informazioni sulle località, vedere l'articolo [Partner e località della rete WAN virtuale](virtual-wan-locations-partners.md). Ogni hub supporta fino a 10.000 connessioni utente remote, 1.000 Branch Connection, quattro circuiti ExpressRoute e fino a 500 connessioni di rete virtuale. Quando si aumentano le prestazioni degli utenti remoti, in caso di domande, non esitare a richiedere assistenza inviando azurevirtualwan@microsoft.comun messaggio di posta elettronica a. Se è necessario il supporto tecnico, assicurarsi di aprire un ticket di supporto dal portale di Azure e la guida sarà in corso.

## <a name="faq"></a><a name="faq"></a>Domande frequenti

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere [Panoramica della rete WAN virtuale](virtual-wan-about.md)