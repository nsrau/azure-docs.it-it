---
title: Risolvere i problemi relativi alle disconnessioni VPN da sito a sito di Azure intermittenti | Microsoft Docs
description: Informazioni su come risolvere il problema per cui la connessione VPN da sito a sito viene periodicamente interrotta.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: 9f6ea8296f1bf81813594b07c50f70dd8647f032
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Risoluzione dei problemi: disconnessioni VPN da sito a sito di Azure intermittenti

Può verificarsi un problema a causa del quale una connessione VPN da punto a sito di Microsoft Azure nuova o esistente non è stabile o si interrompe periodicamente. Questo articolo illustra la procedura per identificare e risolvere la causa del problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

### <a name="prerequisite-step"></a>Passaggio preliminare

Controllare il tipo di gateway di rete virtuale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per le informazioni sul tipo, controllare la pagina **Panoramica** del gateway di rete virtuale.
    
    ![Panoramica del gateway](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passaggio 1 Controllare se il dispositivo VPN locale è convalidato

1. Controllare se si usano un [dispositivo VPN e una versione del sistema operativo convalidati](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Se il dispositivo VPN non è convalidato, potrebbe essere necessario contattare il produttore del dispositivo per verificare se esiste qualche problema di compatibilità.
2. Verificare che il dispositivo VPN sia configurato correttamente. Per altre informazioni, vedere [Esempi di modifica di configurazione dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Passaggio 2 Controllare le impostazioni di associazione di sicurezza (per i gateway di rete virtuale di Azure basati su criteri)

1. Verificare che la rete virtuale, le subnet e gli intervalli nella definizione **Gateway di rete locale** in Microsoft Azure siano gli stessi della configurazione sul dispositivo VPN locale.
2. Verificare che le impostazioni di associazione di sicurezza corrispondano.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Passaggio 3 Cercare le route definite dall'utente o i gruppi di sicurezza di rete nella subnet gateway

Una route definita dall'utente nella subnet gateway potrebbe limitare parte del traffico e consentirne un'altra. La connessione VPN appare quindi non affidabile per parte del traffico e valida per un'altra. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Passaggio 4 Controllare l'impostazione "un tunnel VPN per ogni coppia di subnet" (per i gateway di rete virtuale basati su criteri)

Verificare che il dispositivo VPN locale sia impostato in modo da avere **un tunnel VPN per ogni coppia di subnet** per i gateway di rete virtuale basati su criteri.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Passaggio 5 Controllare la limitazione di associazione di sicurezza (per i gateway di rete virtuale basati su criteri)

Il gateway di rete virtuale basato su criteri ha un limite di 200 coppie di associazione di sicurezza di subnet. Se il numero di subnet di rete virtuale di Azure moltiplicato per il numero di subnet locali è superiore a 200, si osservano sporadiche disconnessioni delle subnet.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Passaggio 6 Controllare l'indirizzo dell'interfaccia esterna del dispositivo VPN locale

- Se l'indirizzo IP per Internet del dispositivo VPN è incluso nella definizione del **gateway di rete locale** in Azure potrebbero verificarsi sporadiche disconnessioni.
- L'interfaccia esterna del dispositivo deve essere direttamente su Internet. Non devono essere presenti Network Address Translation (NAT) o firewall tra Internet e il dispositivo.
-  Se si configura il clustering di firewall in modo che abbia un IP virtuale, è necessario interrompere il cluster ed esporre il dispositivo VPN direttamente a un'interfaccia pubblica con cui il gateway può interfacciarsi.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Passaggio 7 Controllare se il dispositivo VPN locale ha PFS (Perfect Forward Secrecy) abilitato

La funzionalità **PFS (Perfect Forward Secrecy)** può causare i problemi di disconnessione. Se il dispositivo VPN ha la funzionalità **PFS (Perfect Forward Secrecy)** abilitata, disabilitarla, quindi [aggiornare i criteri IPsec del gateway di rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare una connessione da sito a sito a una rete virtuale](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Configure IPsec/IKE policy for Site-to-Site VPN connections (Configurare i criteri IPsec/IKE per le connessioni VPN da sito a sito)](vpn-gateway-ipsecikepolicy-rm-powershell.md)


