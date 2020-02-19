---
title: Intervalli di indirizzi IP privati SNAT del firewall di Azure
description: È possibile configurare gli intervalli privati degli indirizzi IP in modo che il firewall non SNAT il traffico agli indirizzi IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444459"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalli di indirizzi IP privati SNAT del firewall di Azure

Il firewall di Azure non SNAT quando l'indirizzo IP di destinazione si trova in un intervallo di indirizzi IP privati per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. Tuttavia, è possibile configurare il firewall di Azure in modo che **non** SNAT l'intervallo di indirizzi IP pubblici.

## <a name="configure-snat-private-ip-address-ranges"></a>Configurare gli intervalli di indirizzi IP privati SNAT

È possibile usare Azure PowerShell per specificare un intervallo di indirizzi IP che il firewall non SNAT.

### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall, il Azure PowerShell comando è:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges viene espansa alle impostazioni predefinite correnti nel firewall di Azure mentre gli altri intervalli vengono aggiunti.

Per ulteriori informazioni, vedere [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente, usare i comandi Azure PowerShell seguenti:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelli

È possibile aggiungere quanto segue alla sezione `additionalProperties`:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).