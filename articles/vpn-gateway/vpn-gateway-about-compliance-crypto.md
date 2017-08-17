---
title: Informazioni sui requisiti di crittografia e i gateway VPN di Azure | Microsoft Docs
description: In questo articolo vengono descritti i requisiti di crittografia e i gateway VPN di Azure
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: c789e6c278fc0c58c64f5d96e57f94aee5a6cefc
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Informazioni sui requisiti di crittografia e i gateway VPN di Azure

Questo articolo illustra come configurare i gateway VPN di Azure per soddisfare i requisiti di crittografia per i tunnel VPN S2S cross-premise e le connessioni da rete virtuale a rete virtuale all'interno di Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Informazioni sui parametri di criteri IPsec e IKE per gateway VPN di Azure
Lo standard di protocollo IPsec e IKE supporta un'ampia gamma di algoritmi di crittografia in varie combinazioni. Se i clienti non richiedono una combinazione specifica di algoritmi e parametri per la crittografia, i gateway VPN di Azure usano un set di proposte predefinite. I set di criteri predefiniti sono stati scelti per migliorare l'interoperabilità con un'ampia gamma di dispositivi VPN di terze parti in configurazioni predefinite. Di conseguenza, i criteri e il numero di proposte non possono coprire tutte le possibili combinazioni degli algoritmi di crittografia disponibili e della complessità delle chiavi.

Il criterio predefinito impostato per il gateway VPN di Azure è elencato nel documento: [Informazioni sui dispositivi VPN e sui parametri IPsec/IKE per connessioni del Gateway VPN da sito a sito](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisiti per la crittografia
Per le comunicazioni che richiedono algoritmi o parametri di crittografia specifici, in genere a causa di requisiti di conformità o sicurezza, i clienti possono ora configurare i gateway VPN di Azure per usare un criterio IPsec/IKE personalizzato con algoritmi di crittografia e complessità delle chiavi specifici, invece del set di criteri predefinito di Azure.

Ad esempio, i criteri in modalità principale IKEv2 per i gateway VPN di Azure usano solo il gruppo Diffie-Hellman Group 2 (1024 bit), mentre potrebbe essere necessario specificare gruppi più complessi da usare in IKE, ad esempio gruppo 14 (2048 bit), gruppo 24 (gruppo MODP 2048 bit) o ECP (gruppo a curva ellittica) a 256 o 384 bit (gruppo 19 e gruppo 20 rispettivamente). Simili requisiti si applicano anche ai criteri IPsec in modalità rapida.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Criteri IPsec/IKE personalizzati con i gateway VPN di Azure
I gateway VPN di Azure ora supportano i criteri IPsec/IKE personalizzati per connessione. Per una connessione da sito a sito o da rete virtuale a rete virtuale, è possibile scegliere una combinazione specifica di algoritmi di crittografia per IPsec e IKE con attendibilità della chiave, come illustrato nell'esempio seguente:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

È possibile creare un criterio IPsec/IKE e applicarlo a una connessione nuova o esistente. 

### <a name="workflow"></a>Flusso di lavoro

1. Creare le reti virtuali, i gateway VPN o i gateway di rete locale per la topologia di connettività, come descritto in altri documenti sulle procedure
2. Creare criteri IPsec/IKE
3. È possibile applicare i criteri quando si crea una connessione S2S o da rete virtuale a rete virtuale
4. Se la connessione è già stata creata, è possibile applicare o aggiornare i criteri per una connessione esistente


## <a name="ipsecike-policy-faq"></a>Domande frequenti sui criteri IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-ipsecikepolicy-faq-include.md)]


## <a name="next-steps"></a>Passaggi successivi
Vedere [Configurare i criteri IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) per istruzioni dettagliate sulla configurazione dei criteri IPsec/IKE personalizzato su una connessione.

Vedere anche [Connettere più dispositivi VPN basati su criteri](vpn-gateway-connect-multiple-policybased-rm-ps.md) per altre informazioni sull'opzione UsePolicyBasedTrafficSelectors.
