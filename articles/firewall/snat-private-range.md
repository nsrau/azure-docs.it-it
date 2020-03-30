---
title: Intervalli di indirizzi IP privati SNAT del firewall di AzureAzure Firewall SNAT private IP address ranges
description: È possibile configurare gli intervalli privati degli indirizzi IP in modo che il firewall non SNAT non scivoli verso tali indirizzi IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064801"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalli di indirizzi IP privati SNAT del firewall di AzureAzure Firewall SNAT private IP address ranges

Firewall di Azure non SNAT con regole di rete quando l'indirizzo IP di destinazione è in un intervallo di indirizzi IP privati per [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Le regole dell'applicazione vengono sempre applicate utilizzando un [proxy trasparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) indipendentemente dall'indirizzo IP di destinazione.

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure scommette il traffico verso uno degli indirizzi IP privati del firewall in AzureFirewallSubnet.If your organization uses a public IP address range for private networks, Azure Firewall SNATs the traffic to one of the firewall private IP addresses in AzureFirewallSubnet. Tuttavia, è possibile configurare Firewall di Azure in modo che **non** SNAT l'intervallo di indirizzi IP pubblici.

## <a name="configure-snat-private-ip-address-ranges"></a>Configurare gli intervalli di indirizzi IP privati SNAT

È possibile usare Azure PowerShell per specificare un intervallo di indirizzi IP che il firewall non sverrà.

### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall, il comando di Azure PowerShell è:For a new firewall, the Azure PowerShell command is:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges viene espanso ai valori predefiniti correnti in Firewall di Azure mentre gli altri intervalli vengono aggiunti ad esso.

Per ulteriori informazioni, vedere [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente, usare i comandi di Azure PowerShell seguenti:To configure an existing firewall, use the following Azure PowerShell commands:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelli

È possibile aggiungere quanto `additionalProperties` segue alla sezione:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).