---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552968"
---
Se non è già disponibile una rete virtuale, è possibile crearne rapidamente una tramite PowerShell. È anche possibile creare una rete virtuale tramite il portale di Azure.

* Assicurarsi di verificare che lo spazio degli indirizzi della rete virtuale creata non si sovrapponga a uno qualsiasi degli intervalli di indirizzi per le altre reti virtuali a cui ci si vuole connettere o allo spazio degli indirizzi della rete locale. 
* Se è già disponibile una rete virtuale, verificare che soddisfi i criteri richiesti e che non abbia un gateway di rete virtuale.

È possibile creare con facilità la rete virtuale facendo clic su "Prova" in questo articolo per aprire una console di PowerShell. Personalizzare i valori, quindi copiare e incollare i comandi nella finestra della console.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Modificare i comandi di PowerShell e quindi creare un gruppo di risorse.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Modificare i comandi di PowerShell per creare una rete virtuale compatibile per l'ambiente specifico.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```