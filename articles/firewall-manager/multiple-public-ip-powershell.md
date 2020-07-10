---
title: Distribuire il firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell
description: Distribuire un firewall con più indirizzi IP pubblici per proteggere un hub virtuale
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 87af7f0f9b446fb3a54a600f61409c2cfc1a2494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189430"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Distribuire un firewall di Azure con più indirizzi IP pubblici

Se si vuole proteggere un hub virtuale usando il firewall di Azure, è possibile distribuire il firewall con più indirizzi IP pubblici usando Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>Distribuire il firewall

Usare il Azure PowerShell esempio seguente per distribuire un firewall di Azure con più indirizzi IP pubblici per proteggere un hub virtuale.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="delete-a-public-ip-address"></a>Eliminare un indirizzo IP pubblico

È possibile usare Azure PowerShell per eliminare un indirizzo IP pubblico da un firewall di Azure. Nell'esempio seguente viene eliminato un indirizzo IP pubblico da un firewall. Inizia con tre indirizzi IP pubblici.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è un hub virtuale protetto?](secured-virtual-hub.md)