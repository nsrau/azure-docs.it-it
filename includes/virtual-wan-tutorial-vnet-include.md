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
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150853"
---
Per creare rapidamente una rete virtuale, è possibile fare clic su "Prova" in questo articolo per aprire una console di PowerShell. Personalizzare i valori, quindi copiare e incollare i comandi nella finestra della console. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az). Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

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