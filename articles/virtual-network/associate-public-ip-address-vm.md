---
title: Associare un indirizzo IP pubblico a una macchina virtuale
titlesuffix: Azure Virtual Network
description: Informazioni su come associare un indirizzo IP pubblico a una macchina virtuale.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647459"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associare un indirizzo IP pubblico a una macchina virtuale

In questo articolo viene illustrato come associare un indirizzo IP pubblico a una macchina virtuale (VM) esistente. Se si vuole connettersi a una macchina virtuale da Internet, alla macchina virtuale deve essere associato un indirizzo IP pubblico. Se si vuole creare una nuova macchina virtuale con un indirizzo IP pubblico, è possibile farlo usando il portale di [Azure,](virtual-network-deploy-static-pip-arm-portal.md)l'interfaccia della riga di [comando (CLI)](virtual-network-deploy-static-pip-arm-cli.md)di Azure o [PowerShell.](virtual-network-deploy-static-pip-arm-ps.md) Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/ip-addresses/). Esiste un limite al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione. Per informazioni dettagliate, vedere [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

È possibile usare il portale di [Azure,](#azure-portal) [l'interfaccia della riga](#azure-cli) di comando di Azure o [PowerShell](#powershell) per associare un indirizzo IP pubblico a una macchina virtuale.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Individuare o cercare la macchina virtuale a cui si vuole aggiungere l'indirizzo IP pubblico e quindi selezionarlo.
3. In **Impostazioni**selezionare **Rete**e quindi selezionare l'interfaccia di rete a cui si desidera aggiungere l'indirizzo IP pubblico, come illustrato nella figura seguente:

   ![Seleziona interfaccia di rete](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici sono associati alle interfacce di rete collegate a una macchina virtuale. Nell'immagine precedente, la macchina virtuale ha una sola interfaccia di rete. Se la macchina virtuale ha più interfacce di rete, verranno visualizzate tutte e si selezionerebbe l'interfaccia di rete a cui si vuole associare l'indirizzo IP pubblico.

4. Selezionare **Configurazioni IP** e quindi selezionare una configurazione IP, come illustrato nell'immagine seguente:Select IP configurations and then select an IP configuration, as shown in the following picture:

   ![Selezionare la configurazione IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici sono associati alle configurazioni IP per un'interfaccia di rete. Nell'immagine precedente, l'interfaccia di rete ha una configurazione IP. Se l'interfaccia di rete disponeva di più configurazioni IP, tutte verranno visualizzate nell'elenco e si selezionerebbe la configurazione IP a cui si desidera associare l'indirizzo IP pubblico.

5. Selezionare **Abilitato**, quindi selezionare **Indirizzo IP ( Configura impostazioni*necessarie*)**. Scegliere un indirizzo IP pubblico esistente, che chiude automaticamente la casella **Scegli indirizzo IP pubblico.** Se non sono elencati indirizzi IP pubblici disponibili, è necessario crearne uno. Per ulteriori informazioni, vedere [Creare un indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address). Selezionare **Salva**, come illustrato nell'immagine seguente, quindi chiudere la casella relativa alla configurazione IP.

   ![Abilita indirizzo IP pubblico](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici visualizzati sono quelli presenti nella stessa area della macchina virtuale. Se nell'area sono stati creati più indirizzi IP pubblici, verranno visualizzati tutti qui. Se alcuni sono in grigio, è perché l'indirizzo è già associato a una risorsa diversa.

6. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP, come illustrato nell'immagine seguente. La visualizzazione di un indirizzo IP potrebbe richiedere alcuni secondi.

   ![Visualizzare l'indirizzo IP pubblico assegnato](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure.The address is assigned from a pool of addresses used in each Azure region. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nei pool utilizzati per l'area. Se è necessario che l'indirizzo venga assegnato da un pool specifico nell'area, utilizzare un [prefisso indirizzo IP pubblico](public-ip-address-prefix.md).

7. [Consentire il traffico](#allow-network-traffic-to-the-vm) di rete alla macchina virtuale con le regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure o usare Azure Cloud Shell.Install the Azure CLI , or use the Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **Prova lo** nei comandi dell'interfaccia della riga di comando che seguono. Selezionando **Prova** richiama una Cloud Shell con cui è possibile accedere all'account Azure.

1. Se si usa l'interfaccia della riga `az login`di comando in locale in Bash, accedere ad Azure con .
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Utilizzare il comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) per associare un indirizzo IP pubblico a una configurazione IP. Nell'esempio seguente viene associato un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se non si dispone di un indirizzo IP pubblico esistente, utilizzare il comando [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per crearne uno. Ad esempio, il comando seguente crea un indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con i valori predefiniti per diverse impostazioni che è possibile personalizzare. Per ulteriori informazioni su tutte le impostazioni degli indirizzi IP pubblici, vedere [Creare un indirizzo IP pubblico.](virtual-network-public-ip-address.md#create-a-public-ip-address) L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure.The address is assigned from a pool of public IP addresses used for each Azure region. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente, *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, utilizzare il comando [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con il comando [az vm list-ip-addresses.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) L'esempio seguente mostra gli indirizzi IP assegnati a una macchina virtuale esistente denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure.The address is assigned from a pool of addresses used in each Azure region. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nei pool utilizzati per l'area. Se è necessario che l'indirizzo venga assegnato da un pool specifico nell'area, utilizzare un [prefisso indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico](#allow-network-traffic-to-the-vm) di rete alla macchina virtuale con le regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="powershell"></a>PowerShell

Installare PowerShell o usare Azure Cloud Shell.Install [PowerShell](/powershell/azure/install-az-ps), or use the Azure Cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi di PowerShell seguenti. Selezionando **Prova** richiama una Cloud Shell con cui è possibile accedere all'account Azure.

1. Se si usa PowerShell in `Connect-AzAccount`locale, accedere ad Azure con .
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Utilizzare i comandi [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) per ottenere la rete virtuale e la subnet in cui si trova l'interfaccia di rete. Successivamente, utilizzare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per ottenere un'interfaccia di rete e il comando [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere un indirizzo IP pubblico esistente. Utilizzare quindi il comando [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) per associare l'indirizzo IP pubblico alla configurazione IP e il comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) per scrivere la nuova configurazione IP nell'interfaccia di rete.

   Nell'esempio seguente viene associato un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente in una subnet denominata *myVMSubnet* in una rete virtuale denominata *myVMVNet*. Tutte le risorse si trovano in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se non si dispone di un indirizzo IP pubblico esistente, utilizzare il comando [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) per crearne uno. Ad esempio, il comando seguente crea un indirizzo IP pubblico *dinamico* denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup* nell'area *eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con i valori predefiniti per diverse impostazioni che è possibile personalizzare. Per ulteriori informazioni su tutte le impostazioni degli indirizzi IP pubblici, vedere [Creare un indirizzo IP pubblico.](virtual-network-public-ip-address.md#create-a-public-ip-address) L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure.The address is assigned from a pool of public IP addresses used for each Azure region. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome della rete virtuale o della subnet `Get-AzNetworkInterface` in cui si trova l'interfaccia di rete, utilizzare il comando per visualizzare le informazioni. Ad esempio, il comando seguente ottiene le informazioni sulla rete virtuale e sulla subnet per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *myVMVNET* è il nome della rete virtuale e *myVMSubnet* è il nome della subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, utilizzare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per recuperarli. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con il comando [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) Nell'esempio seguente viene illustrato l'indirizzo assegnato a un indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se non si conosce il nome dell'indirizzo IP pubblico assegnato a una configurazione IP, eseguire i comandi seguenti per ottenerlo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   L'output include una o più righe simili all'esempio seguente. Nell'output di esempio, *myVMPublicIP* è il nome dell'indirizzo IP pubblico assegnato alla configurazione IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure.The address is assigned from a pool of addresses used in each Azure region. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nei pool utilizzati per l'area. Se è necessario che l'indirizzo venga assegnato da un pool specifico nell'area, utilizzare un [prefisso indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico](#allow-network-traffic-to-the-vm) di rete alla macchina virtuale con le regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="allow-network-traffic-to-the-vm"></a>Consentire il traffico di rete alla macchina virtualeAllow network traffic to the VM

Prima di connettersi all'indirizzo IP pubblico da Internet, assicurarsi di avere le porte necessarie aperte in qualsiasi gruppo di sicurezza di rete che potrebbe essere associato all'interfaccia di rete, la subnet in cui si trova l'interfaccia di rete o entrambi. Anche se i gruppi di sicurezza filtrano il traffico verso l'indirizzo IP privato dell'interfaccia di rete, una volta che il traffico Internet in ingresso arriva all'indirizzo IP pubblico, Azure converte l'indirizzo pubblico nell'indirizzo IP privato, flusso di traffico, la comunicazione con l'indirizzo IP pubblico non riesce. È possibile visualizzare le regole di sicurezza valide per un'interfaccia di rete e la relativa subnet utilizzando [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)o [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Passaggi successivi

Consentire il traffico Internet in ingresso alla macchina virtuale con un gruppo di sicurezza di rete. Per informazioni su come creare un gruppo di sicurezza di rete, vedere [Utilizzare i gruppi](manage-network-security-group.md#work-with-network-security-groups)di sicurezza di rete . Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere [Gruppi](security-overview.md)di sicurezza .
