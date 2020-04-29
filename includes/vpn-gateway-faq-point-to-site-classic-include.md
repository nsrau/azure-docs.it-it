---
title: includere il file
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309021"
---
Queste domande frequenti si applicano alle connessioni da punto a sito che usano il modello di distribuzione classica.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Quali sistemi operativi client è possibile usare con la connettività da punto a sito?

Sono supportati i sistemi operativi client seguenti:

* Windows 7 (a 32 e 64 bit)
* Windows Server 2008 R2 (solo a 64 bit)
* Windows 8 (a 32 e 64 bit)
* Windows 8.1 (a 32 e 64 bit)
* Windows Server 2012 (solo a 64 bit)
* Windows Server 2012 R2 (solo a 64 bit)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Per la connettività da punto a sito è possibile usare qualsiasi client VPN software che supporta SSTP?

No. L'assistenza è limitata solo alle versioni dei sistemi operativi Windows elencati.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Quanti endpoint client VPN possono esistere nella configurazione da punto sito?

La quantità di endpoint client VPN dipende dallo SKU e dal protocollo del gateway.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>È possibile usare la CA radice della PKI interna per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare fino a 20 certificati radice.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>È possibile attraversare proxy e firewall con la funzionalità da punto a sito?

Sì. Viene usato Secure Sockets Tunneling Protocol (SSTP) per effettuare il tunneling tramite firewall. Questo tunnel viene visualizzato come connessione HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se si riavvia un computer client configurato per la funzionalità Da punto a sito, la VPN verrà riconnessa automaticamente?

Per impostazione predefinita, il computer client non ristabilirà automaticamente la connessione VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La funzionalità Da punto a sito supporta la riconnessione automatica e DDNS nei client VPN?

No. La riconnessione automatica e DDNS non sono supportati attualmente nelle VPN da punto a sito.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>È possibile la coesistenza di configurazioni da sito a sito e da punto a sito per la stessa rete virtuale?

Sì. Entrambe le soluzioni funzionano se si ha un tipo di VPN basata su route per il gateway. Per il modello di distribuzione classica è necessario un gateway dinamico. Non viene fornito il supporto per i gateway VPN con routing statico da punto a sito o i gateway che usano il cmdlet **-VpnType PolicyBased**.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>È possibile configurare un client da punto a sito per connettersi contempo a più reti virtuali?

Sì. I prefissi IP delle reti virtuali non devono tuttavia essere sovrapposti e gli spazi degli indirizzi da punto a sito non devono sovrapporsi tra le reti virtuali.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Che velocità effettiva è possibile prevedere usando connessioni da sito a sito o da punto a sito?

È difficile mantenere esattamente la velocità effettiva tramite i tunnel VPN. IPSec e SSTP sono protocolli VPN con un elevato livello di crittografia. La velocità effettiva è limitata inoltre dalla latenza e dalla larghezza di banda tra le sedi locali e Internet.
