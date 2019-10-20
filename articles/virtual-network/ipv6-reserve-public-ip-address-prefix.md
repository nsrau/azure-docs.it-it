---
title: Riservare gli indirizzi e gli intervalli di indirizzi IPv6 pubblici in una rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come riservare gli indirizzi IPv6 pubblici e gli intervalli di indirizzi in una rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595076"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Riserva il prefisso dell'indirizzo IPv6 pubblico
IPv6 per rete virtuale di Azure (VNet) consente di ospitare le applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. Oltre a riservare i singoli indirizzi IPv6, è possibile riservare intervalli contigui di indirizzi IPv6 di Azure (noti come prefisso IP) per l'uso. Questo articolo descrive come creare indirizzi IP e intervalli di indirizzi IP pubblici IPv6 usando Azure PowerShell e l'interfaccia della riga di comando.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente disponibile in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Creare un singolo IP pubblico IPv6 riservato

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

È possibile creare un singolo indirizzo IP pubblico IPv6 riservato (statico) usando Azure PowerShell con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) come indicato di seguito:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

 È possibile creare un singolo indirizzo IP pubblico IPv6 riservato (statico) per l'interfaccia della riga di comando di Azure con [AZ Network Public-IP create](/cli/azure/network/public-ip) come indicato di seguito:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Creare un prefisso IPv6 riservato (intervallo)

Per riservare un prefisso IPv6, aggiungere la famiglia di indirizzi IP di IPv6 allo stesso comando utilizzato per la creazione di prefissi IPv4. I comandi seguenti creano un prefisso di dimensioni/125 (8 indirizzi IPv6).  

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

È possibile creare un indirizzo IPv6 pubblico usando l'interfaccia della riga di comando di Azure con [AZ Network Public-IP create](/powershell/module/az.network/new-azpublicipprefix) come indicato di seguito:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

È possibile creare un indirizzo IPv6 pubblico usando l'interfaccia della riga di comando di Azure come indicato di seguito:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Allocare un indirizzo IP pubblico da un prefisso IPv6 riservato

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

 Per creare un indirizzo IP pubblico IPv6 statico da un prefisso riservato, è necessario aggiungere l'argomento `-PublicIpPrefix` quando si crea l'indirizzo IP pubblico usando Azure PowerShell. Nell'esempio seguente si presuppone che sia stato creato un prefisso e che sia stato archiviato in una variabile di PowerShell denominata: *$MyOwnIPv 6prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
 
Nell'esempio seguente si presuppone che sia stato creato un prefisso e che sia stato archiviato in una variabile CLI denominata: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [prefisso dell'indirizzo IPv6](ipv6-public-ip-address-prefix.md).
- Altre informazioni sugli [indirizzi IPv6](ipv6-overview.md).
