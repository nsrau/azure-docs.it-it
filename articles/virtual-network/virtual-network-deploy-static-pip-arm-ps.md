---
title: Creare una macchina virtuale con un indirizzo IP pubblico statico - PowerShell | Microsoft Docs
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante Azure PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 422a48a3671974248e9e3258be16d3537713f762
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089999"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Creare una macchina virtuale con un indirizzo IP pubblico statico usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile creare una macchina virtuale con un indirizzo IP pubblico statico. Un indirizzo IP pubblico consente di comunicare con una macchina virtuale da Internet. Assegnare un indirizzo IP pubblico statico, anziché un indirizzo dinamico, per assicurarsi che l'indirizzo non cambi mai. Altre informazioni sugli [indirizzi IP pubblici statici](virtual-network-ip-addresses-overview-arm.md#allocation-method). Per modificare da dinamico a statico un indirizzo IP pubblico assegnato a una macchina virtuale esistente o per usare indirizzi IP privati, vedere [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Gli indirizzi IP pubblici hanno un [costo nominale](https://azure.microsoft.com/pricing/details/ip-addresses)ed esiste un [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

È possibile eseguire la procedura seguente dal computer locale o tramite Azure Cloud Shell. Per usare il computer locale, assicurarsi che [Azure PowerShell sia installato](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Per usare Azure Cloud Shell, selezionare **Prova** nell'angolo superiore destro di una qualsiasi finestra di comando riportata di seguito. Cloud Shell consente di accedere ad Azure.

1. Se si usa Cloud Shell, andare al passaggio 2. Aprire una sessione di comando e accedere ad Azure con `Connect-AzAccount`.
2. Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse nell'area di Azure Stati Uniti orientali:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Creare una macchina virtuale con il [New-AzVM](/powershell/module/az.Compute/New-azVM) comando. L'opzione `-AllocationMethod "Static"` assegna un indirizzo IP pubblico statico alla macchina virtuale. L'esempio seguente crea una macchina virtuale Windows Server con un indirizzo IP pubblico statico SKU Basic denominato *myPublicIpAddress*. Quando richiesto, immettere un nome utente e una password da usare come credenziali di accesso per la macchina virtuale:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se l'indirizzo IP pubblico deve essere uno SKU standard, è necessario [creare un indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address), [creare un'interfaccia di rete](virtual-network-network-interface.md#create-a-network-interface), [assegnare l'indirizzo IP pubblico all'interfaccia di rete](virtual-network-network-interface-addresses.md#add-ip-addresses) e quindi [creare una macchina virtuale con l'interfaccia di rete](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm) in passaggi separati. Altre informazioni sugli [SKU di indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#sku). Se la macchina virtuale verrà aggiunta al pool back-end di un Azure Load Balancer pubblico, lo SKU dell'indirizzo IP pubblico della macchina virtuale deve corrispondere allo SKU dell'indirizzo IP pubblico del bilanciamento del carico. Per informazioni dettagliate, vedere [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Visualizzare l'indirizzo IP pubblico assegnato e verificare che sia stato creato come un indirizzo statico, con [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure ha assegnato un indirizzo IP pubblico tra gli indirizzi usati nell'area in cui la macchina virtuale è stata creata. È possibile scaricare l'elenco degli intervalli (prefissi) per i cloud [Pubblico](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [Cina](https://www.microsoft.com/download/details.aspx?id=57062) e [Germania](https://www.microsoft.com/download/details.aspx?id=57064) di Azure.

> [!WARNING]
> Non modificare le impostazioni dell'indirizzo IP all'interno del sistema operativo della macchina virtuale. Il sistema operativo non rileva gli indirizzi IP pubblici di Azure. Anche se è possibile aggiungere al sistema operativo impostazioni relative a indirizzi IP privati, è consigliabile farlo solo se necessario e solo dopo aver letto [Aggiungere un indirizzo IP privato a un sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Altre informazioni sugli [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e sull'assegnazione di un [indirizzo IP privato statico](virtual-network-network-interface-addresses.md#add-ip-addresses) a una macchina virtuale di Azure
- Altre informazioni sulla creazione di macchine virtuali [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
