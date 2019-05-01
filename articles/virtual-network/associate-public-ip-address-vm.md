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
ms.openlocfilehash: 69460a111e6fd879807b4025d6832b3ac515a9b4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691994"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associare un indirizzo IP pubblico a una macchina virtuale

In questo articolo descrive come associare un indirizzo IP pubblico a una macchina virtuale esistente (VM). Se si desidera connettersi a una macchina virtuale da internet, la macchina virtuale deve avere un indirizzo IP pubblico a cui è associato. Se si desidera creare una nuova macchina virtuale con un indirizzo IP pubblico, è possibile farlo usando il [portale di Azure](virtual-network-deploy-static-pip-arm-portal.md), il [interfaccia della riga di comando di Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md), o [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/ip-addresses/). È previsto un limite al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione. Per informazioni dettagliate, vedere [limiti](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

È possibile usare la [portale di Azure](#azure-portal), Azure [interfaccia della riga di comando](#azure-cli) (CLI), o [PowerShell](#powershell) per associare un indirizzo IP pubblico a una macchina virtuale.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare o cercare la macchina virtuale che si desidera aggiungere l'indirizzo IP pubblico e quindi selezionarlo.
3. Sotto **le impostazioni**, selezionare **Networking**e quindi selezionare l'interfaccia di rete si desidera aggiungere l'indirizzo IP pubblico, come illustrato nell'immagine seguente:

   ![Selezionare l'interfaccia di rete](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici associati alle interfacce di rete collegate a una VM. Nella figura precedente, la macchina virtuale ha solo un'interfaccia di rete. Se la macchina virtuale ha più interfacce di rete, vengono visualizzati tutti e selezionare l'interfaccia di rete che si desidera associare l'indirizzo IP pubblico.

4. Selezionare **configurazioni IP** e quindi selezionare una configurazione IP, come illustrato nell'immagine seguente:

   ![Selezionare la configurazione IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici associati alle configurazioni IP per un'interfaccia di rete. Nella figura precedente, l'interfaccia di rete ha una configurazione IP. Se l'interfaccia di rete ha più configurazioni IP, vengono visualizzati tutti nell'elenco e selezionare la configurazione IP che si desidera associare l'indirizzo IP pubblico.

5. Selezionare **Enabled**, quindi selezionare **indirizzo IP (*Configura le impostazioni obbligatorie*)**. Scegliere un indirizzo IP pubblico esistente, che chiude automaticamente il **Scegli indirizzo IP pubblico** casella. Se non hai ancora disponibili indirizzi IP pubblici elencati, è necessario crearne uno. Per altre informazioni, vedere [creare un indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address). Selezionare **salvare**, come illustrato nell'immagine che segue e quindi chiudere la finestra per la configurazione IP.

   ![Abilitare l'indirizzo IP pubblico](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici che vengono visualizzati sono quelli esistenti nella stessa area della macchina virtuale. Se si dispone di più indirizzi IP pubblici creati nell'area, tutti verranno visualizzati qui. Se qualsiasi sono disattivato, è perché l'indirizzo è già associato a un'altra risorsa.

6. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP, come illustrato nell'immagine seguente. Potrebbe richiedere alcuni secondi per un indirizzo IP venga visualizzato.

   ![Visualizzazione assegnato l'indirizzo IP pubblico](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco di pool di indirizzi usati in ogni area, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nel pool di utilizzati per l'area. Se è necessario l'indirizzo deve essere assegnato da un pool specifico nell'area, usare una [prefisso dell'indirizzo IP pubblico](public-ip-address-prefix.md).

7. [Consentire il traffico di rete alla macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare il [CLI Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), oppure usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il **Provalo** pulsante della riga di comando comandi che seguono. Selezionando **Provalo** richiama una Cloud Shell a cui è possibile accedere al proprio account Azure con.

1. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure con `az login`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare la [aggiornamento di az network nic ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) comando per associare un indirizzo IP pubblico a una configurazione IP. L'esempio seguente associa un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente nel gruppo di risorse denominato *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se non hai un indirizzo IP pubblico esistente, usare il [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) comandi per crearne uno. Ad esempio, il comando seguente crea un indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con valori predefiniti per diverse impostazioni che è possibile personalizzare. Per altre informazioni su tutte le impostazioni dell'indirizzo IP pubbliche, vedere [creare un indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address). L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure. Per visualizzare un elenco di pool di indirizzi usati in ogni area, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il [elenco di az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) comando per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una VM denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe che sono simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente, *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il [elenco di az network nic ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) comando per recuperare queste informazioni. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con il [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) comando. L'esempio seguente mostra gli indirizzi IP assegnati a una VM esistente denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco di pool di indirizzi usati in ogni area, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nel pool di utilizzati per l'area. Se è necessario l'indirizzo deve essere assegnato da un pool specifico nell'area, usare una [prefisso dell'indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico di rete alla macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="powershell"></a>PowerShell

Installare [PowerShell](/powershell/azure/install-az-ps), oppure usare Azure Cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il **Provalo** pulsante in PowerShell comandi che seguono. Selezionando **Provalo** richiama una Cloud Shell a cui è possibile accedere al proprio account Azure con.

1. Se si usa PowerShell in locale, accedere ad Azure con `Connect-AzAccount`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare la [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) comandi per ottenere la rete virtuale e subnet in cui l'interfaccia di rete. Successivamente, usare il [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando per ottenere un'interfaccia di rete e il [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando per ottenere un indirizzo IP pubblico esistente. Quindi usare il [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) comando per associare l'indirizzo IP pubblico alla configurazione IP e il [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) comando per scrivere la nuova configurazione IP per il interfaccia di rete.

   L'esempio seguente associa un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente in una subnet denominata *myVMSubnet* in una rete virtuale denominata *myVMVNet*. Tutte le risorse siano in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se non hai un indirizzo IP pubblico esistente, usare il [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) comandi per crearne uno. Ad esempio, il comando seguente crea una *dinamici* indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup* nel  *Stati Uniti orientali* area.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con valori predefiniti per diverse impostazioni che è possibile personalizzare. Per altre informazioni su tutte le impostazioni dell'indirizzo IP pubbliche, vedere [creare un indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address). L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure. Per visualizzare un elenco di pool di indirizzi usati in ogni area, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) comando per visualizzarli. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una VM denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     L'output include una o più righe che sono simili all'esempio seguente. Nell'output di esempio, *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome della rete virtuale o subnet in cui l'interfaccia di rete, usare il `Get-AzNetworkInterface` comando per visualizzare le informazioni. Ad esempio, il comando seguente ottiene le informazioni di rete e subnet virtuali per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     L'output include una o più righe che sono simili all'esempio seguente. Nell'output di esempio, *myVMVNET* è il nome della rete virtuale e *myVMSubnet* è il nome della subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando per recuperare queste informazioni. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     L'output include una o più righe che sono simili all'esempio seguente. Nell'output di esempio, *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con il [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando. L'esempio seguente mostra l'indirizzo assegnato a un indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.

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

   L'output include una o più righe che sono simili all'esempio seguente. Nell'output di esempio, *myVMPublicIP* è il nome dell'indirizzo IP pubblico assegnato alla configurazione IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco di pool di indirizzi usati in ogni area, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nel pool di utilizzati per l'area. Se è necessario l'indirizzo deve essere assegnato da un pool specifico nell'area, usare una [prefisso dell'indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico di rete alla macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="allow-network-traffic-to-the-vm"></a>Consentire il traffico di rete alla macchina virtuale

Prima di connettesi all'indirizzo IP pubblico da internet, assicurarsi di aver le porte necessarie aprire in qualsiasi gruppo di sicurezza di rete che si potrà è associata all'interfaccia di rete, alla subnet che l'interfaccia di rete o entrambi. Anche se i gruppi di sicurezza filtrare il traffico verso l'indirizzo IP privato dell'interfaccia di rete, una volta che il traffico internet in ingresso arriva all'indirizzo IP pubblico, Azure Converte l'indirizzo pubblico per l'indirizzo IP privato di indirizzi, pertanto se un gruppo di sicurezza di rete impedisce la il flusso del traffico, la comunicazione con l'indirizzo IP pubblico non riesce. È possibile visualizzare le regole di sicurezza effettive per un'interfaccia di rete e la subnet usando il [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), o [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Passaggi successivi

Consentire il traffico internet in ingresso alla macchina virtuale con un gruppo di sicurezza di rete. Per informazioni su come creare un gruppo di sicurezza di rete, vedere [lavorare con i gruppi di sicurezza di rete](manage-network-security-group.md#work-with-network-security-groups). Per altre informazioni sui gruppi di sicurezza di rete, vedere [gruppi di sicurezza](security-overview.md).
