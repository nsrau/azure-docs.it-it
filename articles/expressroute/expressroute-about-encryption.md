---
title: 'Azure ExpressRoute: About Encryption'
description: Informazioni sulla crittografia ExpressRoute.Learn about ExpressRoute encryption.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: 7dd9106539b6756d74629ac663241a5b5562cefb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75437051"
---
# <a name="expressroute-encryption"></a>Crittografia ExpressRouteExpressRoute encryption
 
ExpressRoute supporta un paio di tecnologie di crittografia per garantire la riservatezza e l'integrità dei dati che attraversano tra la rete e la rete di Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Domande frequenti sulla crittografia point-to-point in base a MACsec
MACsec è [uno standard IEEE](https://1.ieee802.org/security/802-1ae/). Crittografa i dati a livello mac (Media Access Control) o Network Layer 2. È possibile usare MACsec per crittografare i collegamenti fisici tra i dispositivi di rete e i dispositivi di rete microsoft quando ci si connette a Microsoft tramite [ExpressRoute Direct](expressroute-erdirect-about.md). MACsec è disabilitato sulle porte ExpressRoute Direct per impostazione predefinita. Portare la propria chiave MACsec per la crittografia e archiviarla in [Azure Key Vault](../key-vault/key-vault-overview.md). Sei tu a decidere quando ruotare la chiave. Vedi altre domande frequenti di seguito.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>È possibile abilitare MACsec nel circuito ExpressRoute di cui è stato eseguito il provisioning da un provider ExpressRoute?
No. MACsec crittografa tutto il traffico su un collegamento fisico con una chiave di proprietà di un'entità (ad esempio un cliente). Pertanto, è disponibile solo in ExpressRoute Direct.Therefore, it's available on ExpressRoute Direct only.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>È possibile crittografare alcuni dei circuiti ExpressRoute nelle porte di ExpressRoute Direct e lasciare in chiaro altri circuiti sulle stesse porte? 
No. Una volta abilitato MACsec, tutto il traffico di controllo di rete, ad esempio, il traffico dati BGP e il traffico dati dei clienti vengono crittografati. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando abilito/disabilita MACsec o aggiorno la chiave MACsec, la rete locale perderà la connettività a Microsoft tramite ExpressRoute?
Sì. Per la configurazione MACsec, è supportata solo la modalità chiave precondivisa. Significa che devi aggiornare la chiave sia sui tuoi dispositivi che su Microsoft (tramite la nostra API). Questa modifica non è atomica, quindi perderai la connettività quando c'è una mancata corrispondenza chiave tra i due lati. È consigliabile pianificare una finestra di manutenzione per la modifica della configurazione. Per ridurre al minimo il tempo di inattività, è consigliabile aggiornare la configurazione su un collegamento di ExpressRoute Direct alla volta dopo il passaggio del traffico di rete all'altro collegamento.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Il traffico continuerà a fluire se c'è una mancata corrispondenza nella chiave MACsec tra i miei dispositivi e Microsoft?
No. Se MACsec è configurato e si verifica una mancata corrispondenza delle chiavi, si perde la connettività a Microsoft. In altre parole, non ripiegheremo su una connessione non crittografata, esponendo i tuoi dati. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>L'abilitazione di MACsec su ExpressRoute Direct degradilerà le prestazioni della rete?
La crittografia e la decrittografia MACsec si verificano nell'hardware dei router che utilizziamo. Non c'è nessun impatto sulle prestazioni da parte nostra. Tuttavia, è necessario rivolgersi al fornitore della rete per i dispositivi in uso e verificare se MACsec presenta implicazioni sulle prestazioni.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>quali suite di crittografia sono supportate per la crittografia?
Supportiamo AES128 e AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Domande frequenti sulla crittografia end-to-end tramite IPsec
IPsec è [uno standard IETF](https://tools.ietf.org/html/rfc6071). Crittografa i dati a livello IP (Internet Protocol) o Network Layer 3. È possibile usare IPsec per crittografare una connessione end-to-end tra la rete locale e la rete virtuale (VNET) in Azure.You can use IPsec to encrypt an end-to-end connection between your on-premises network and your virtual network (VNET) on Azure. Vedi altre domande frequenti di seguito.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>È possibile abilitare IPsec oltre a MACsec sulle porte ExpressRoute Direct?
Sì. MACsec protegge le connessioni fisiche tra l'utente e Microsoft. IPsec secures the end-to-end connection between you and your virtual networks on Azure. È possibile abilitarli in modo indipendente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>È possibile usare il gateway VPN di Azure per configurare il tunnel IPsec tra la rete locale e la rete virtuale di Azure?
Sì. È possibile configurare questo tunnel IPsec su Microsoft Peering del circuito ExpressRoute.You can set up this IPsec tunnel over Microsoft Peering of your ExpressRoute circuit. Segui la nostra guida alla [configurazione](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>È possibile usare il gateway VPN di Azure per configurare il tunnel IPsec rispetto al peering privato di Azure?
Se si adotta la rete WAN virtuale di Azure, è possibile seguire [questi passaggi](../virtual-wan/vpn-over-expressroute.md) per crittografare la connessione end-to-end. Se si dispone di una rete virtuale di Azure regolare, è possibile distribuire un gateway VPN di terze parti nella rete virtuale e stabilire un tunnel IPsec tra esso e il gateway VPN locale.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual è la velocità effettiva che otterrò dopo aver abilitato IPsec nella mia connessione ExpressRoute?
Se viene usato il gateway VPN di Azure, controllare i [numeri delle prestazioni qui](../vpn-gateway/vpn-gateway-about-vpngateways.md). Se viene utilizzato un gateway VPN di terze parti, verificare con il fornitore i numeri di prestazioni.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla configurazione MACsec, vedere [Configure MACsec.](expressroute-howto-macsec.md)

Per ulteriori informazioni sulla configurazione di IPsec, vedere [Configure IPsec.](site-to-site-vpn-over-microsoft-peering.md)
