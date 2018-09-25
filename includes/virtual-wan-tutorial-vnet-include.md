---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004169"
---
Se non è già disponibile una rete virtuale, è possibile crearne rapidamente una tramite PowerShell. È anche possibile creare una rete virtuale tramite il portale di Azure.

* Assicurarsi di verificare che lo spazio degli indirizzi della rete virtuale creata non si sovrapponga a uno qualsiasi degli intervalli di indirizzi per le altre reti virtuali a cui ci si vuole connettere o allo spazio degli indirizzi della rete locale. 
* Se è già disponibile una rete virtuale, verificare che soddisfi i criteri richiesti e che non abbia un gateway di rete virtuale.

È possibile creare con facilità la rete virtuale facendo clic su "Prova" in questo articolo per aprire una console di PowerShell. Personalizzare i valori, quindi copiare e incollare i comandi nella finestra della console.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Modificare i comandi di PowerShell e quindi creare un gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Modificare i comandi di PowerShell per creare una rete virtuale compatibile per l'ambiente specifico.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```