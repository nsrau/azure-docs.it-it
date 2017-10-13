---
title: "Risoluzione di un problema relativo all'impossibilità di stabilire una connessione VPN da sito a sito di Azure | Microsoft Docs"
description: "Informazioni su come risolvere un problema relativo a una connessione VPN da sito a sito che si interrompe improvvisamente e non può essere riconnessa."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 55cfba5e9730b123bba20dfdc5d10c1157352a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Risoluzione dei problemi: una connessione VPN da sito a sito di Azure non può essere stabilita e smette di funzionare

Dopo aver configurato una connessione VPN da sito a sito tra una rete locale e una rete virtuale di Azure, la connessione VPN si interrompe improvvisamente e non può essere riconnessa. Questo articolo illustra la procedura per risolvere questo tipo di problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il problema, provare prima a [reimpostare il gateway VPN di Azure](vpn-gateway-resetgw-classic.md) e a reimpostare il tunnel dal dispositivo VPN locale. Se il problema persiste, seguire questa procedura per identificare la causa del problema.

### <a name="prerequisite-step"></a>Passaggio preliminare

Controllare il tipo del gateway VPN di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per informazioni sul tipo, vedere la pagina **Panoramica** del gateway VPN.
    
    ![Panoramica del gateway](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passaggio 1. Controllare se il dispositivo VPN locale è convalidato

1. Controllare se si usano un [dispositivo VPN e una versione del sistema operativo convalidati](vpn-gateway-about-vpn-devices.md#devicetable). Se il dispositivo non è un dispositivo VPN convalidato, può essere necessario contattare il produttore del dispositivo per verificare se esiste un problema di compatibilità.

2. Verificare che il dispositivo VPN sia configurato correttamente. Per altre informazioni, vedere [Esempi di modifica di configurazione dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Passaggio 2. Verificare la chiave condivisa

Confrontare la chiave condivisa del dispositivo VPN locale con quella del gateway VPN della rete virtuale per verificare che corrispondano. 

Per visualizzare la chiave condivisa per la connessione VPN di Azure, usare uno dei metodi seguenti:

**Portale di Azure**

1. Passare alla connessione gateway VPN di Azure da sito a sito appena creata.

2. Nella sezione **Impostazioni** fare clic su **Chiave condivisa**.
    
    ![Chiave condivisa](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Per il modello di distribuzione di Azure Resource Manager:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Per il modello di distribuzione classica:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passaggio 3. Verificare gli IP dei peer VPN

-   La definizione IP nell'oggetto **Gateway di rete locale** in Azure deve corrispondere all'IP del dispositivo locale.
-   La definizione dell'IP del gateway di Azure impostata nel dispositivo locale deve corrispondere all'IP del gateway di Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passaggio 4. Verificare i gruppi di sicurezza di rete e il routing definito dall'utente nella subnet del gateway

Cercare e rimuovere il routing definito dall'utente o i gruppi di sicurezza di rete nella subnet del gateway e quindi testare il risultato. Se il problema viene risolto, convalidare le impostazioni applicate dal gruppo di sicurezza di rete o dal routing definito dall'utente.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passaggio 5. Controllare l'indirizzo dell'interfaccia esterna del dispositivo VPN locale

- Se l'indirizzo IP per Internet del dispositivo VPN è incluso nella definizione della **rete locale** in Azure, è possibile che si verifichino sporadiche disconnessioni.
- L'interfaccia esterna del dispositivo deve essere direttamente su Internet. Non devono essere presenti Network Address Translation o firewall tra Internet e il dispositivo.
- Per configurare il clustering di firewall in modo che abbia un IP virtuale, è necessario interrompere il cluster ed esporre il dispositivo VPN direttamente a un'interfaccia pubblica con cui il gateway può interfacciarsi.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passaggio 6. Verificare che le subnet corrispondano esattamente (gateway basati su criteri di Azure)

-   Verificare che ci sia una corrispondenza esatta delle subnet tra la rete virtuale di Azure e le definizioni locali per la rete virtuale di Azure.
-   Verificare che ci sia una corrispondenza esatta delle subnet tra il **gateway di rete locale** e le definizioni locali per la rete locale.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passaggio 7. Verificare il probe di integrità del gateway di Azure

1. Passare al [probe di integrità](https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe).

2. Fare clic sull'avviso del certificato.
3. Se si riceve una risposta, il gateway VPN viene considerato integro. Se non si riceve una risposta, è possibile che il gateway non sia integro oppure il problema è causato da un gruppo di sicurezza di rete nella subnet del gateway. Il testo seguente è una risposta di esempio:

    &lt;?xml version="1.0"?&gt;  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6&lt;/string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passaggio 8. Controllare se nel dispositivo VPN locale è abilitata la funzionalità PFS

La funzionalità PFS (Perfect Forward Secrecy) può causare problemi di disconnessione. Se nel dispositivo VPN è abilitata la funzionalità PFS, disabilitarla e aggiornare i criteri IPsec del gateway VPN.

## <a name="next-steps"></a>Passaggi successivi

-   [Configurare una connessione da sito a sito a una rete virtuale](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configurare criteri IPsec/IKE per connessioni VPN da sito a sito o da rete virtuale a rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md)
