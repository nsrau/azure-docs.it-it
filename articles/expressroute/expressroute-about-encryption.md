---
title: Informazioni sulla crittografia-Azure ExpressRoute | Microsoft Docs
description: Informazioni sulla crittografia ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: b452fccf2ae0915592420bdda3a47fef43eba9a8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72788702"
---
# <a name="expressroute-encryption"></a>Crittografia ExpressRoute
 
ExpressRoute supporta alcune tecnologie di crittografia per garantire la riservatezza e l'integrità dei dati attraversati tra la rete e la rete Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Domande frequenti sulla crittografia Point-to-Point by MACsec
MACsec è uno [standard IEEE](https://1.ieee802.org/security/802-1ae/). Esegue la crittografia dei dati a livello di Media Access Control (MAC) o rete 2. È possibile usare MACsec per crittografare i collegamenti fisici tra i dispositivi di rete e i dispositivi di rete Microsoft quando ci si connette a Microsoft tramite [ExpressRoute Direct](expressroute-erdirect-about.md). Per impostazione predefinita, MACsec è disabilitato sulle porte ExpressRoute Direct. È possibile importare la chiave MACsec per la crittografia e archiviarla in [Azure Key Vault](../key-vault/key-vault-overview.md). Decidere quando ruotare la chiave. Vedere le domande frequenti più avanti.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>È possibile abilitare MACsec sul circuito ExpressRoute sottoposto a provisioning da un provider ExpressRoute?
No. MACsec crittografa tutto il traffico su un collegamento fisico con una chiave di proprietà di un'entità (ad esempio, Customer). Pertanto, è disponibile solo in ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>È possibile crittografare alcuni dei circuiti ExpressRoute sulle porte ExpressRoute dirette e lasciare altri circuiti sulle stesse porte non crittografati? 
No. Quando MACsec è abilitato, tutto il traffico di controllo della rete, ad esempio il traffico dei dati BGP e il traffico dati del cliente vengono crittografati. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando si Abilita/Disabilita MACsec o aggiorna la chiave di MACsec, la rete locale perde la connettività a Microsoft tramite ExpressRoute?
Sì. Per la configurazione di MACsec, è supportata solo la modalità chiave precondivisa. Ciò significa che è necessario aggiornare la chiave sia nei dispositivi che nei dispositivi Microsoft (tramite l'API). Questa modifica non è atomica, quindi si perde la connettività quando si verifica una mancata corrispondenza tra le due parti. Si consiglia vivamente di pianificare una finestra di manutenzione per la modifica della configurazione. Per ridurre al minimo i tempi di inattività, è consigliabile aggiornare la configurazione in un collegamento di ExpressRoute Direct alla volta dopo aver passato il traffico di rete all'altro collegamento.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Il traffico continuerà a fluire se si verifica una mancata corrispondenza tra la chiave di MACsec e i dispositivi personali e Microsoft?
No. Se MACsec è configurato e si verifica una mancata corrispondenza della chiave, si perde la connettività a Microsoft. In altre parole, non verrà eseguito il fallback a una connessione non crittografata, che espone i dati. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>L'abilitazione di MACsec in ExpressRoute Direct diminuisce le prestazioni di rete?
La crittografia e la decrittografia MACsec si verificano nell'hardware dei router usati. Non ci sono effetti sulle prestazioni. Tuttavia, è consigliabile rivolgersi al fornitore di rete per i dispositivi usati e verificare se MACsec presenta implicazioni sulle prestazioni.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>quali pacchetti di crittografia sono supportati per la crittografia?
Sono supportati AES128 e AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Domande frequenti sulla crittografia end-to-end tramite IPsec
IPsec è uno [standard IETF](https://tools.ietf.org/html/rfc6071). Esegue la crittografia dei dati a livello di protocollo IP (Internet Protocol) o rete 3. È possibile usare IPsec per crittografare una connessione end-to-end tra la rete locale e la rete virtuale (VNET) in Azure. Vedere le domande frequenti più avanti.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>È possibile abilitare IPsec oltre a MACsec sulle porte dirette di ExpressRoute?
Sì. MACsec protegge le connessioni fisiche tra l'utente e Microsoft. IPsec protegge la connessione end-to-end tra l'utente e le reti virtuali in Azure. È possibile abilitarli in modo indipendente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>È possibile usare il gateway VPN di Azure per configurare il tunnel IPsec tra la rete locale e la rete virtuale di Azure?
Sì. È possibile configurare il tunnel IPsec sul peering Microsoft del circuito ExpressRoute. Seguire la [Guida alla configurazione](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>È possibile usare il gateway VPN di Azure per configurare il tunnel IPsec tramite il peering privato di Azure?
No. È necessario distribuire un gateway VPN di terze parti nella rete virtuale di Azure e stabilire un tunnel IPsec tra l'IT e il gateway VPN locale.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual è la velocità effettiva che verrà abilitata dopo l'abilitazione di IPsec sulla connessione ExpressRoute?
Se si usa il gateway VPN di Azure, controllare i [numeri di prestazioni qui](../vpn-gateway/vpn-gateway-about-vpngateways.md). Se viene usato un gateway VPN di terze parti, rivolgersi al fornitore per i numeri relativi alle prestazioni.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla configurazione di MACsec, vedere [configurare MACsec](expressroute-howto-macsec.md) .

Per ulteriori informazioni sulla configurazione IPsec, vedere [configurare IPSec](site-to-site-vpn-over-microsoft-peering.md) .
