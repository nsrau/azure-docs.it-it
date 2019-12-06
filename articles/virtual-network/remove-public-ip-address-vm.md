---
title: Annullare l'associazione di un indirizzo IP pubblico da una macchina virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come annullare l'associazione di un indirizzo IP pubblico da una macchina virtuale
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: beb2655b0796adbe289b0af104dead2d15e584db
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852159"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Annullare l'associazione di un indirizzo IP pubblico da una macchina virtuale di Azure 

Questo articolo illustra come annullare l'associazione di un indirizzo IP pubblico da una macchina virtuale (VM) di Azure.

È possibile usare la [portale di Azure](#azure-portal), l' [interfaccia della riga di comando](#azure-cli) di Azure o [PowerShell](#powershell) per annullare l'associazione di un indirizzo IP pubblico da una macchina virtuale.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare o cercare la macchina virtuale a cui si vuole dissociare l'indirizzo IP pubblico e quindi selezionarla.
3. Nella pagina VM selezionare **Panoramica**, selezionare l'indirizzo IP pubblico, come illustrato nell'immagine seguente:

   ![Seleziona IP pubblico](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Nella pagina indirizzo IP pubblico selezionare **Panoramica**, quindi selezionare **dissocia**, come illustrato nell'immagine seguente:

    ![Dissocia IP pubblico](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. In **dissocia l'indirizzo IP pubblico**selezionare **Sì**.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare l' [interfaccia](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)della riga di comando di Azure o usare la Azure cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **prova** nei comandi dell'interfaccia della riga di comando che seguono. Selezionando **try viene** richiamata una cloud Shell a cui è possibile accedere con l'account Azure.

1. Se si usa l'interfaccia della riga di comando in locale in bash, accedere ad Azure con `az login`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare il comando [AZ Network NIC-IP-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) per annullare l'associazione di un indirizzo IP pubblico da una configurazione IP. L'esempio seguente annulla l'associazione di un indirizzo IP pubblico denominato *myVMPublicIP* dalla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* collegata a una macchina virtuale denominata *MyVM* in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAdress
   ```

   Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [AZ VM NIC list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente, *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [AZ Network NIC IP-Config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

## <a name="powershell"></a>PowerShell

Installare [PowerShell](/powershell/azure/install-az-ps)o usare il Azure cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il pulsante **prova** nei comandi di PowerShell seguenti. Selezionando **try viene** richiamata una cloud Shell a cui è possibile accedere con l'account Azure.

1. Se si usa PowerShell in locale, accedere ad Azure con `Connect-AzAccount`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per ottenere un'interfaccia di rete. Impostare il valore di indirizzo IP pubblico su null, quindi usare il comando [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) per scrivere la nuova configurazione IP nell'interfaccia di rete.

   Nell'esempio seguente viene annullato l'associazione di un indirizzo IP pubblico denominato *myVMPublicIP* da un'interfaccia di rete denominata *myVMVMNic* collegata a una macchina virtuale denominata *myVM*. Tutte le risorse si trovano in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Nell'output sono incluse una o più righe simili all'esempio seguente. Nell'output di esempio, *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Nell'output sono incluse una o più righe simili all'esempio seguente. Nell'output di esempio, *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md).
