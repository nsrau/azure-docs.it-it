---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037211"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (Gateway VPN) e un gateway di rete virtualedella WAN virtuale di Azure?

La WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. I dispositivi del ramo CPE effettuano il provisioning automatico e si connettono alla WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quali provider di dispositivo (partner di WAN virtuale) sono supportati in fase di lancio? 

Al momento, Citrix e Riverbed supportano l'esperienza di WAN virtuale completamente automatizzata. Altri partner si aggiungeranno nei prossimi mesi, tra cui: Nokia Nuage, Palo Alto e Checkpoint. Per altre informazioni, vedere [Virtual WAN partners](https://aka.ms/virtualwan)(Partner di WAN virtuali).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti VPN e soddisfi i requisiti dell'anteprima per il supporto di IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>In che modo i partner di WAN virtuale automatizzano la connettività con la WAN virtuale di Azure?

Soluzioni di connettività definite dal software gestiscono in genere i propri dispositivi di ramo usando un controller o un centro di provisioning dei dispositivi. Il controller può usare le API di Azure per automatizzare la connettività alla WAN virtuale di Azure. Per altre informazioni sul funzionamento, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner di WAN virtuale).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>La WAN virtuale cambia qualche funzionalità di connettività esistente?   

Non sono state apportate modifiche alle funzionalità di connettività di Azure esistenti.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sono disponibili nuove risorse di Gestione risorse per la WAN virtuale?
  
Sì, la WAN virtuale introduce nuove risorse di Gestione risorse. Per altre informazioni, vedere la [panoramica](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Sono previsti requisiti speciali per accedere all'anteprima? 

Per poter configurare una WAN virtuale di Azure, è necessario innanzitutto eseguire la registrazione dell'abbonamento nell'anteprima. Per farlo, inviare un messaggio di posta elettronica a <azurevirtualwan@microsoft.com> con l'ID abbonamento. Dopo che l'abbonamento è stato registrato, si riceverà un messaggio di posta elettronica. Fino a quando non si riceve la conferma che l'abbonamento è stato registrato, non sarà possibile lavorare con la WAN virtuale nel portale.

Considerazioni:

* L'anteprima è limitata alle sole aree pubbliche di Azure.
* Sono supportate fino a 100 connessioni per ogni hub virtuale. Ogni connessione è costituita da 2 tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN di hub virtuale di Azure.
* È consigliabile usare questa versione di anteprima se:
  * Si vuole distribuire larghezza di banda aggregata minore di 1 Gbps per hub virtuale.
  * Si dispone di un dispositivo VPN che supporta la configurazione basata su route e la connettività IPsec IKEv2.
  * Si vuole provare a usare SD-WAN e dispositivi di ramo dei partner del lancio (Riverbed e Citrix).<br>oppure<br>Si desidera configurare la connettività da ramo a ramo e da ramo ad Azure che includa la gestione della configurazione del dispositivo locale. (Configurazione automatica)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>È supportato il peering di reti virtuali globale con la WAN virtuale di Azure? 

 No.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Le reti virtuali spoke connesse a un hub virtuale possono comunicare tra loro?

Sì. È possibile eseguire direttamente il peering di reti virtuali fra spoke connessi a un hub virtuale. Per altre informazioni, vedere [Peering di rete virtuale](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>È possibile distribuire e usare l'appliance virtuale di rete preferita (in una rete virtuale di appliance virtuali di rete) con la WAN virtuale di Azure?

Sì, è possibile connettere la rete virtuale di appliance virtuali di rete preferiti alla WAN virtuale di Azure, ma serviranno funzionalità di routing nell'hub che saranno presenti con la disponibilità a livello generale. Tutti gli spoke connessi alla rete virtuale di appliance virtuali di rete devono anche essere connessi all'hub virtuale. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Una rete virtuale di appliance virtuali di rete può avere un gateway di rete virtuale?

No. La rete virtuale di appliance virtuali di rete non può avere un gateway di rete virtuale se è connessa all'hub virtuale. 

### <a name="is-there-support-for-bgp"></a>È disponibile il supporto per BGP?

Sì, BGP è supportato. Per garantire che le route da una rete virtuale di appliance virtuali di rete siano pubblicizzate in modo appropriato, gli spoke devono disabilitare BGP se sono connessi a una rete virtuale di appliance virtuali di rete che, a sua volta, è connessa a un hub virtuale. Inoltre, connettere le reti virtuali spoke all'hub virtuale.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>È possibile indirizzare il traffico usando route definite dall'utente nell'hub virtuale?

Le funzionalità relative alle route definite dall'utente e al routing saranno presenti con la disponibilità a livello generale.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?
 
Non sono previsti costi aggiuntivi durante l'anteprima. I [prezzi della VPN di Azure e di uscita](https://azure.microsoft.com/pricing/details/vpn-gateway/) rimangono attivi durante la fase di anteprima.