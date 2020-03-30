---
title: Dissociate a public IP address from an Azure VM
titlesuffix: Azure Virtual Network
description: Informazioni su come dissociare un indirizzo IP pubblico da una macchina virtualeLearn how to dissociate a public IP address from a VM
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
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900751"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Dissociate a public IP address from an Azure VM 

In questo articolo viene illustrato come dissociare un indirizzo IP pubblico da una macchina virtuale di Azure.In this article, you learn how to dissociate a public IP address from an Azure virtual machine (VM).

È possibile usare il portale di [Azure,](#azure-portal) [l'interfaccia della riga](#azure-cli) di comando di Azure o [PowerShell](#powershell) per dissociare un indirizzo IP pubblico da una macchina virtuale.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Individuare o cercare la macchina virtuale da cui si desidera dissociare l'indirizzo IP pubblico e quindi selezionarlo.
3. Nella pagina VM selezionare **Panoramica**, selezionare l'indirizzo IP pubblico come illustrato nell'immagine seguente:In the VM page, select Overview , select the public IP address as shown in the following picture:

   ![Seleziona IP pubblico](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Nella pagina Indirizzo IP pubblico selezionare **Panoramica**e quindi **Dissociato**, come illustrato nella figura seguente:

    ![IP pubblico dissociato](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. In **Dissocia indirizzo IP pubblico**selezionare **Sì**.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure o usare Azure Cloud Shell.Install the Azure CLI , or use the Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **Prova lo** nei comandi dell'interfaccia della riga di comando che seguono. Selezionando **Prova** richiama una Cloud Shell con cui è possibile accedere all'account Azure.

1. Se si usa l'interfaccia della riga `az login`di comando in locale in Bash, accedere ad Azure con .
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Utilizzare il comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) per dissociare un indirizzo IP pubblico da una configurazione IP. Nell'esempio seguente viene dissociato un indirizzo IP pubblico denominato *myVMPublicIP* dalla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* collegata a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente, *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, utilizzare il comando [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP pubbliche per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se non si conosce il nome di una configurazione IP pubblica per un'interfaccia di rete, utilizzare il comando [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP pubbliche per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installare PowerShell o usare Azure Cloud Shell.Install [PowerShell](/powershell/azure/install-az-ps), or use the Azure Cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi di PowerShell seguenti. Selezionando **Prova** richiama una Cloud Shell con cui è possibile accedere all'account Azure.

1. Se si usa PowerShell in `Connect-AzAccount`locale, accedere ad Azure con .
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Utilizzare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per ottenere un'interfaccia di rete. Impostare il valore indirizzo IP pubblico su null e quindi utilizzare il comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) per scrivere la nuova configurazione IP nell'interfaccia di rete.

   Nell'esempio seguente viene dissociato un indirizzo IP pubblico denominato *myVMPublicIP* da un'interfaccia di rete denominata *myVMVMNic* collegata a una macchina virtuale denominata *myVM*. Tutte le risorse si trovano in un gruppo di risorse denominato *myResourceGroup*.
  
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

     L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, utilizzare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [associare un indirizzo IP pubblico a una macchina virtuale.](associate-public-ip-address-vm.md)
