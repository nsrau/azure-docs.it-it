---
title: "Risoluzione del problema relativo all'impossibilità di stabilire una connessione VPN da sito a sito di Azure | Microsoft Docs"
description: "Informazioni su come risolvere il problema per cui la connessione VPN da sito a sito smette improvvisamente di funzionare e non può più essere ristabilita."
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
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
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Risoluzione dei problemi: la connessione VPN da sito a sito di Azure non può essere stabilita e smette di funzionare

Si configura la connessione VPN da sito a sito tra la rete locale e una rete virtuale di Microsoft Azure. La connessione VPN smette improvvisamente di funzionare e non è possibile ristabilirla. Questo articolo illustra la procedura per risolvere il problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il problema, provare prima a [reimpostare il gateway di rete virtuale di Azure](vpn-gateway-resetgw-classic.md) e a reimpostare il tunnel dal dispositivo VPN locale. Se il problema persiste, seguire questa procedura per identificare la causa del problema.

### <a name="prerequisite-step"></a>Passaggio preliminare

Controllare il tipo di gateway di rete virtuale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per le informazioni sul tipo, controllare la pagina **Panoramica** del gateway di rete virtuale.
    
    ![Panoramica del gateway](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passaggio 1 Controllare se il dispositivo VPN locale è convalidato

1. Controllare se si usano un [dispositivo VPN e una versione del sistema operativo convalidati](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Se il dispositivo non è un dispositivo VPN convalidato, potrebbe essere necessario contattare il produttore del dispositivo per verificare se esiste qualche problema di compatibilità.
2. Verificare che il dispositivo VPN sia configurato correttamente. Per altre informazioni, vedere [Esempi di modifica di configurazione dispositivo](/vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-keypsk"></a>Passaggio 2 Verificare la chiave condivisa (PSK)

Confrontare la **chiave condivisa** del dispositivo VPN locale e della VPN di rete virtuale per verificare che le chiavi corrispondano. 

Per visualizzare la chiave condivisa per la connessione VPN di Azure, usare uno dei metodi seguenti：

**Portale di Azure**

1. Andare a Gateway di rete virtuale > connessione da sito a sito creata.
2. Nella sezione **Impostazioni** fare clic su **Chiave condivisa**.
    
    ![Chiave condivisa](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Per la modalità Resource Manager

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Per la versione classica

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passaggio 3 Verificare gli IP dei peer VPN

-   La definizione IP nell'oggetto **Gateway di rete locale** in Azure deve corrispondere all'IP del dispositivo locale.
-   La definizione dell'IP del gateway di Azure impostata nel dispositivo locale deve corrispondere all'IP del gateway di Azure.

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>Passaggio 4 Gruppo di sicurezza di rete e routing definito dall'utente nella subnet gateway

Cercare e rimuovere il routing definito dall'utente o i gruppi di sicurezza di rete nella subnet gateway e quindi testare il risultato. Se il problema viene risolto, convalidare le impostazioni del gruppo di sicurezza di rete o del routing definito dall'utente applicato.

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>Passaggio 5 Controllare l'indirizzo dell'interfaccia esterna del dispositivo VPN locale

- Se l'indirizzo IP per Internet del dispositivo VPN è incluso nella definizione della **rete locale** in Azure potrebbero verificarsi sporadiche disconnessioni.
- L'interfaccia esterna del dispositivo deve essere direttamente su Internet. Non devono essere presenti Network Address Translation o firewall tra Internet e il dispositivo.
-  Se si configura il clustering di firewall in modo che abbia un IP virtuale, è necessario interrompere il cluster ed esporre il dispositivo VPN direttamente a un'interfaccia pubblica con cui il gateway può interfacciarsi.

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>Passaggio 6 Verificare che le subnet corrispondano esattamente (gateway basati su criteri di Azure)

-   Verificare che ci sia una corrispondenza esatta delle subnet tra la rete virtuale di Azure e le definizioni locali per la rete virtuale di Azure.
-   Verificare che ci sia una corrispondenza esatta delle subnet tra il **gateway di rete locale** e le definizioni locali per la rete locale.

### <a name="step-5-verify-azure-gateway-health-probe"></a>Passaggio 5 Verificare il probe di integrità del gateway di Azure

1. Passare a https://&lt;IPGatewayReteVirtuale&gt;:8081/healthprobe
2. Fare clic sull'avviso del certificato.
3. Se si riceve una risposta, il gateway di rete virtuale viene considerato integro. Se non si riceve una risposta, il gateway potrebbe non essere integro oppure il problema è causato da un gruppo di sicurezza di rete nella subnet gateway. Il testo seguente è una risposta di esempio:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>Passaggio 7 Controllare se il dispositivo VPN locale ha la funzionalità PFS (Perfect Forward Secrecy) abilitata

La funzionalità PFS (Perfect Forward Secrecy) può causare i problemi di disconnessione. Se il dispositivo VPN ha la funzionalità PFS (Perfect Forward Secrecy) abilitata, disabilitarla, quindi aggiornare i criteri IPsec del gateway di rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

-   [Configurare una connessione da sito a sito a una rete virtuale](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configure IPsec/IKE policy for Site-to-Site VPN connections (Configurare i criteri IPsec/IKE per le connessioni VPN da sito a sito)](vpn-gateway-ipsecikepolicy-rm-powershell.md)

