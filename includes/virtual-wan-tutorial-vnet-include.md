---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077505"
---
Per creare rapidamente una rete virtuale, è possibile fare clic su "Prova" in questo articolo per aprire una console di PowerShell in Azure Cloud Shell. Personalizzare i valori, quindi copiare e incollare i comandi nella finestra della console. 

Assicurarsi di verificare che lo spazio degli indirizzi della rete virtuale creata non si sovrapponga a uno qualsiasi degli intervalli di indirizzi per le altre reti virtuali a cui ci si vuole connettere o allo spazio degli indirizzi della rete locale.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se non è già presente un gruppo di risorse da usare, crearne uno. Modificare i comandi di PowerShell in modo da riflettere il nome del gruppo di risorse da usare, quindi eseguire il cmdlet seguente:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Modificare i comandi di PowerShell per creare una rete virtuale compatibile per l'ambiente.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```
