---
title: Annullare l'associazione di un indirizzo IP pubblico a una macchina virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come annullare l'associazione di un indirizzo IP pubblico a una macchina virtuale
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: b171699a0c578b3761e58f6e0e977199369864a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709964"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Annullare l'associazione di un indirizzo IP pubblico a una macchina virtuale di Azure 

Questo articolo illustra come annullare l'associazione di un indirizzo IP pubblico a una macchina virtuale (VM) di Azure.

Per annullare l'associazione di un indirizzo IP pubblico a una macchina virtuale, è possibile usare il [portale di Azure](#azure-portal), l'[interfaccia della riga di comando](#azure-cli) di Azure o [PowerShell](#powershell).

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare o cercare la macchina virtuale per la quale si vuole annullare l'associazione dell'indirizzo IP pubblico e quindi selezionarla.
3. Nella pagina della macchina virtuale selezionare **Panoramica** e quindi l'indirizzo IP pubblico, come illustrato nell'immagine seguente:

   ![Selezionare l'indirizzo IP pubblico](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Nella pagina dell'indirizzo IP pubblico selezionare **Panoramica** e quindi **Annulla associazione**, come illustrato nell'immagine seguente:

    ![Annullare l'associazione dell'indirizzo IP pubblico](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. In **Annulla associazione dell'indirizzo IP** selezionare **Sì**.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare l'[interfaccia della riga di comando di Azure ](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) o usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi dell'interfaccia della riga di comando che seguono. Selezionando **Prova** viene richiamata un'istanza di Cloud Shell, a cui è possibile accedere con l'account Azure.

1. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure con `az login`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare il comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) per annullare l'associazione di un indirizzo IP pubblico a una configurazione IP. L'esempio seguente annulla l'associazione di un indirizzo IP pubblico denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* collegata a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) per visualizzarle. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) per recuperarle. Ad esempio, il comando seguente elenca i nomi delle configurazioni di indirizzi IP pubblici per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se non si conosce il nome di una configurazione di un indirizzo IP pubblico per un'interfaccia di rete, usare il comando [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) per recuperarle. Ad esempio, il comando seguente elenca i nomi delle configurazioni di indirizzi IP pubblici per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installare [PowerShell](/powershell/azure/install-az-ps) o usare Azure Cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi di PowerShell che seguono. Selezionando **Prova** viene richiamata un'istanza di Cloud Shell, a cui è possibile accedere con l'account Azure.

1. Se si usa PowerShell in locale, accedere ad Azure con `Connect-AzAccount`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per ottenere un'interfaccia di rete. Impostare il valore di indirizzo IP pubblico su null e quindi usare il comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) per scrivere la nuova configurazione IP nell'interfaccia di rete.

   L'esempio seguente annulla l'associazione di un indirizzo IP pubblico denominato *myVMPublicIP* a un'interfaccia di rete denominata *myVMVMNic* collegata a una macchina virtuale denominata *myVM*. Tutte le risorse si trovano in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) per visualizzarle. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per recuperarlo. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md).
