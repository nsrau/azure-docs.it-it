---
title: Creare una VM con un indirizzo IP pubblico statico - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 203c886828fa13433f784d1db9a032b06fab398a
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715200"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Creare una macchina virtuale con un indirizzo IP pubblico statico usando l'interfaccia della riga di comando di Azure

È possibile creare una macchina virtuale con un indirizzo IP pubblico statico. Un indirizzo IP pubblico consente di comunicare con una macchina virtuale da Internet. Assegnare un indirizzo IP pubblico statico, anziché un indirizzo dinamico, per assicurarsi che l'indirizzo non cambi mai. Altre informazioni sugli [indirizzi IP pubblici statici](virtual-network-ip-addresses-overview-arm.md#allocation-method). Per modificare da dinamico a statico un indirizzo IP pubblico assegnato a una macchina virtuale esistente o per usare indirizzi IP privati, vedere [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Gli indirizzi IP pubblici hanno un [costo nominale](https://azure.microsoft.com/pricing/details/ip-addresses)ed esiste un [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

È possibile eseguire la procedura seguente dal computer locale o tramite Azure Cloud Shell. Per usare il computer locale, assicurarsi che l'[interfaccia della riga di comando di Azure sia installata](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Per usare Azure Cloud Shell, selezionare **Prova** nell'angolo superiore destro di una qualsiasi finestra di comando riportata di seguito. Cloud Shell consente di accedere ad Azure.

1. Se si usa Cloud Shell, andare al passaggio 2. Aprire una sessione di comando e accedere ad Azure con `az login`.
2. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse nell'area di Azure Stati Uniti orientali:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Crea una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az-vm-create). L'opzione `--public-ip-address-allocation=static` assegna un indirizzo IP pubblico statico alla macchina virtuale. L'esempio seguente crea una macchina virtuale Ubuntu con un indirizzo IP pubblico statico SKU Basic denominato *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Se l'indirizzo IP pubblico deve essere uno SKU standard, aggiungere `--public-ip-sku Standard` al comando precedente. Altre informazioni sugli [SKU di indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#sku). Se la macchina virtuale verrà aggiunta al pool back-end di un Azure Load Balancer pubblico, lo SKU dell'indirizzo IP pubblico della macchina virtuale deve corrispondere allo SKU dell'indirizzo IP pubblico del bilanciamento del carico. Per informazioni dettagliate, vedere [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Visualizzare l'indirizzo IP pubblico assegnato e confermare che sia stato creato come indirizzo statico SKU Basic con [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure ha assegnato un indirizzo IP pubblico tra gli indirizzi usati nell'area in cui la macchina virtuale è stata creata. È possibile scaricare l'elenco degli intervalli (prefissi) per i cloud [Pubblico](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [Cina](https://www.microsoft.com/download/details.aspx?id=57062) e [Germania](https://www.microsoft.com/download/details.aspx?id=57064) di Azure.

> [!WARNING]
Non modificare le impostazioni dell'indirizzo IP all'interno del sistema operativo della macchina virtuale. Il sistema operativo non rileva gli indirizzi IP pubblici di Azure. Anche se è possibile aggiungere al sistema operativo impostazioni relative a indirizzi IP privati, è consigliabile farlo solo se necessario e solo dopo aver letto [Aggiungere un indirizzo IP privato a un sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [az group delete](/cli/azure/group#az-group-delete) per rimuoverli:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Altre informazioni sugli [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e sull'assegnazione di un [indirizzo IP privato statico](virtual-network-network-interface-addresses.md#add-ip-addresses) a una macchina virtuale di Azure
- Altre informazioni sulla creazione di macchine virtuali [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)