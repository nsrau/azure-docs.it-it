---
title: WAN virtuale di Azure e funzionamento remoto
description: In questa pagina viene descritto come sfruttare la rete WAN virtuale di Azure per consentire di lavorare in remoto a causa della pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337137"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>WAN virtuale di Azure e supporto del lavoro remotoAzure Virtual WAN and supporting remote work

>[!NOTE]
>Questo articolo descrive come sfruttare la rete WAN virtuale di Azure, Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto e ridurre i problemi di rete che si trovano ad affrontare a causa di una crisi DI COVID-19.
>

Stai cercando di fornire connettività per gli utenti remoti?
Improvvisamente vedi la necessità di supportare un'ondata di utenti oltre a quello che avevi pianificato?
È necessario che l'utente si connetta da casa e non solo acceda al cloud, ma sia anche in grado di raggiungere in locale?
È necessario che gli utenti remoti siano in grado di raggiungere le risorse dietro una rete WAN privata?
È necessario per gli utenti accedere alle risorse cloud intra senza dover configurare la connettività tra aree?
Poiché questa pandemia globale crea intorno a noi cambiamenti senza precedenti, il team WAN virtuale di Azure è qui per soddisfare le tue esigenze di connettività.

La rete WAN virtuale di Azure è un servizio di rete che riunisce molte funzionalità di rete, sicurezza e routing per fornire un'unica interfaccia operativa. Queste funzionalità includono la connettività delle filiali (tramite l'automazione della connettività da dispositivi partner WAN virtuali come SD-WAN o VPN CPE), connettività VPN da sito a sito, connettività VPN utente remoto (punto-a-sito), connettività privata (ExpressRoute), Connettività intra cloud (connettività transitiva per reti virtuali), Interconnettività VPN ExpressRoute, routing, firewall di Azure, crittografia per connettività privata e così via. Non è necessario disporre di tutti questi casi d'uso per iniziare a utilizzare la rete WAN virtuale. È possibile iniziare con un solo caso d'uso e regolare la rete man mano che si evolve.

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan1.png)

Ora parlando di utenti remoti, consente di guardare a ciò che è necessario per ottenere la vostra rete in esecuzione:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configurare la connettività degli utenti remoti

È possibile connettersi alle risorse in Azure tramite una connessione IPsec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client VPN per l'utente remoto. Questo client può essere il client VPN di Azure o OpenVPN Client o qualsiasi client che supporta IKEv2.This client can be the [Azure VPN client](https://go.microsoft.com/fwlink/?linkid=2117554) or OpenVPN Client or any client that supports IKEv2. Per altre informazioni, vedere [Creare una connessione da punto a sito](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Connettività dall'utente remoto all'ambiente locale

Qui sono disponibili due opzioni:

* Configurare la connettività da sito a sito con qualsiasi dispositivo VPN esistente. Quando si connette il dispositivo VPN IPsec all'hub WAN virtuale di Azure, l'interconnettività tra la VPN utente da punto a sito (utente remoto) e la VPN da sito a sito è automatica. Per altre informazioni su come configurare la VPN da sito a sito dal dispositivo VPN locale alla rete WAN virtuale di Azure, vedere Creare una connessione da [sito a sito tramite WAN virtuale.](virtual-wan-site-to-site-portal.md)

* Connettere il circuito ExpressRoute all'hub WAN virtuale. La connessione di un circuito ExpressRoute richiede la distribuzione di un gateway ExpressRoute nella rete WAN virtuale. Non appena ne è stato distribuito uno, l'interconnettività tra la VPN utente da punto a sito e l'utente ExpressRoute è automatica. Per creare la connessione ExpressRoute, vedere [Creare una connessione ExpressRoute tramite WAN virtuale.](virtual-wan-expressroute-portal.md) È possibile usare un circuito ExpressRoute esistente per connettersi alla rete WAN virtuale di Azure.You can use an existing ExpressRoute circuit to connect to Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente WAN virtuale di base esistente

La rete WAN virtuale di base fornisce solo VPN da sito a sito. Affinché gli utenti remoti si connettano, è necessario aggiornare la rete WAN virtuale alla rete WAN virtuale standard. Per la procedura di aggiornamento di una rete WAN virtuale, vedere [Aggiornare una rete WAN virtuale da Basic a Standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Ulteriori informazioni

La rete WAN virtuale supporta un hub per area/posizione. Per informazioni sulle località, vedere l'articolo [Partner e località della rete WAN virtuale](virtual-wan-locations-partners.md). Ogni hub supporta fino a 10.000 connessioni utente remote, 1.000 connessioni di succursale, quattro circuiti ExpressRoute e fino a 500 connessioni di rete virtuale. Come si scala fino gli utenti remoti, se avete domande, non esitate a cercare aiuto inviando una e-mail a azurevirtualwan@microsoft.com. Se è necessario il supporto tecnico, assicurarsi di aprire un ticket di supporto dal portale di Azure e la Guida sarà in arrivo.

## <a name="faq"></a><a name="faq"></a>Domande frequenti

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete WAN virtuale, vedere [Panoramica della WAN virtuale](virtual-wan-about.md)