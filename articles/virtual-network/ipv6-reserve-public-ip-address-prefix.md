---
title: Riservare indirizzi IPv6 pubblici e intervalli di indirizzi in una rete virtuale di AzureReserve public IPv6 addresses and address ranges in an Azure virtual network
titlesuffix: Azure Virtual Network
description: Informazioni su come prenotare indirizzi IPv6 pubblici e intervalli di indirizzi in una rete virtuale di Azure.Learn how to reserve public IPv6 addresses and address ranges in an Azure virtual network.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72595076"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Riserva prefisso indirizzo IPv6 pubblico
IPv6 per la rete virtuale di Azure consente di ospitare applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. Oltre a riservare singoli indirizzi IPv6, è possibile riservare intervalli contigui di indirizzi IPv6 di Azure (noto come prefisso IP) per l'uso. Questo articolo descrive come creare indirizzi IP pubblici IPv6 e intervalli di indirizzi usando Azure PowerShell e l'interfaccia della riga di comando.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Creare un singolo IP pubblico IPv6 riservatoCreate a single reserved IPv6 public IP

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

È possibile creare un singolo indirizzo IP pubblico IPv6 riservato (statico) usando Azure PowerShell con New-AzPublicIpAddress come segue:You can create a single reserved (static) IPv6 Public IP address using Azure PowerShell with [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) as follows:

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

 È possibile creare un singolo indirizzo IP pubblico IPv6 riservato (statico) con az network public-ip create come segue:You can create a single reserved (static) IPv6 Public IP address Azure CLI with [az network public-ip create](/cli/azure/network/public-ip) as follows:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Creare un prefisso IPv6 riservato (intervallo)Create a reserved IPv6 prefix (range)

Per riservare un prefisso IPv6, aggiungere la famiglia di indirizzi IP di IPv6 allo stesso comando utilizzato per la creazione di prefissi IPv4. I comandi seguenti creano un prefisso di dimensione /125 (8 indirizzi IPv6).  

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

È possibile creare un indirizzo IPv6 pubblico usando l'interfaccia della riga di comando di Azure con az network public-ip create come segue:You can create a public IPv6 address using Azure CLI with [az network public-ip create](/powershell/module/az.network/new-azpublicipprefix) as follows:
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

È possibile creare un indirizzo IPv6 pubblico usando l'interfaccia della riga di comando di Azure come segue:You can create a public IPv6 address using Azure CLI as follows:

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

 Si crea un indirizzo IP pubblico IPv6 `-PublicIpPrefix` statico da un prefisso riservato aggiungendo l'argomento quando si crea l'indirizzo IP pubblico tramite Azure PowerShell.You create a static IPv6 Public IP from a reserved prefix by adding the argument when creating the public IP using Azure PowerShell. Nell'esempio seguente si presuppone che un prefisso sia stato creato e archiviato in una variabile di PowerShell denominata: *$myOwnIPv6Prefix*.

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
 
Nell'esempio seguente si presuppone che un prefisso sia stato creato e archiviato in una variabile CLI denominata: *IPv6PrefixWestUS*.

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
- Ulteriori informazioni sul [prefisso dell'indirizzo IPv6](ipv6-public-ip-address-prefix.md).
- Ulteriori informazioni sugli [indirizzi IPv6](ipv6-overview.md).
