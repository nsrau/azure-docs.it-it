---
title: "Creare e gestire VM Windows in Azure che usano più schede di interfaccia di rete | Microsoft Docs"
description: "Informazioni su come creare e gestire una VM Windows a cui sono collegate più schede di interfaccia di rete usando i modelli di Azure PowerShell o Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/05/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 92f5181dbf36ef0f7e2568d557faa7c5f2144ad9
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Creare e gestire una macchina virtuale Windows che ha più schede di interfaccia di rete
Alle macchine virtuali (VM) in Azure possono essere collegate più schede di interfaccia di rete virtuale. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end oppure una rete dedicata a una soluzione di monitoraggio o backup. Questo articolo illustra come creare una macchina virtuale a cui sono collegate più schede di interfaccia di rete e come aggiungere o rimuovere le schede di interfaccia di rete da una VM esistente. Le differenti [dimensioni della macchina virtuale](sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

Per informazioni dettagliate, incluse quelle sulla creazione di più schede di interfaccia di rete negli script di PowerShell, vedere [Distribuzione di macchine virtuali con più schede di interfaccia di rete](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="prerequisites"></a>Prerequisiti
Verificare di aver prima [installato e configurato la versione più recente di Azure PowerShell](/powershell/azure/overview).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Creare una macchina virtuale con più NIC
Creare prima un gruppo di risorse. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *EastUs*:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Creare la rete virtuale e le subnet
Negli scenari comuni una rete virtuale ha due o più subnet. Una subnet può essere dedicata al traffico front-end e l'altra al traffico back-end. Per connettersi a entrambe le subnet, si usano quindi più schede di interfaccia di rete nella VM.

1. Definire due subnet della rete virtuale con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L'esempio seguente definisce le subnet per *mySubnetFrontEnd* e *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Creare la rete virtuale e le subnet con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Creare più schede di rete
Creare due schede di interfaccia di rete con [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Collegare una scheda di interfaccia di rete alla subnet front-end e l'altra alla subnet back-end. L'esempio seguente crea le schede di interfaccia di rete denominate *myNic1* e *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le VM. L'articolo dettagliato sulla [macchina virtuale con più schede di interfaccia di rete](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) offre una guida alla creazione di un gruppo di sicurezza di rete e all'assegnazione delle schede di interfaccia di rete.

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale
Ora è possibile iniziare con la configurazione della macchina virtuale. Ad ogni dimensione della macchina virtuale corrisponde un limite del numero totale di schede di rete che è possibile aggiungere. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](sizes.md).

1. Impostare le credenziali della VM sulla variabile `$cred` come indicato di seguito:

    ```powershell
    $cred = Get-Credential
    ```

2. Definire la VM con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). L'esempio seguente definisce una VM denominata *myVM* e usa una dimensione della VM che supporta fino a due schede di interfaccia di rete (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Creare il resto della configurazione della VM con [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) e [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). L'esempio seguente crea una VM Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Collegare le due schede di interfaccia di rete create prima con [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Creare infine la VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>Aggiungere una scheda di interfaccia di rete a una VM esistente
Per aggiungere una scheda di interfaccia di rete virtuale a una VM esistente, si dealloca la VM, si aggiunge la scheda di interfaccia di rete virtuale, quindi si avvia la VM.

1. Deallocare la VM con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L'esempio seguente dealloca la VM denominata *myVM* in *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Ottenere la configurazione esistente della VM con [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). L'esempio seguente ottiene le informazioni relative alla macchina virtuale denominata *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. L'esempio seguente crea una scheda di interfaccia di rete virtuale con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface), denominata *myNic3* e collegata *mySubnetBackEnd*. La scheda di interfaccia di rete virtuale viene quindi collegata alla VM denominata *myVM* in *myResourceGroup* con [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Schede di interfaccia di rete virtuale primarie
    Una delle schede di interfaccia di rete in una VM con più schede di interfaccia di rete deve essere primaria. Se una delle schede di interfaccia di rete virtuale esistenti nella VM è già impostata come primaria, è possibile saltare questo passaggio. L'esempio seguente presuppone che due schede di interfaccia di rete virtuale siano ora presenti in una VM e che si voglia aggiungere la prima scheda di interfaccia di rete (`[0]`) come primaria:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Avviare la VM con [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>Rimuovere una scheda di interfaccia di rete da una VM esistente
Per rimuovere una scheda di interfaccia di rete virtuale da una VM esistente, si dealloca la VM, si rimuove la scheda di interfaccia di rete virtuale, quindi si avvia la VM.

1. Deallocare la VM con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L'esempio seguente dealloca la VM denominata *myVM* in *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Ottenere la configurazione esistente della VM con [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). L'esempio seguente ottiene le informazioni relative alla macchina virtuale denominata *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Ottenere informazioni sulla rimozione della scheda di interfaccia di rete con [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). L'esempio seguente ottiene informazioni su *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Rimuovere la scheda di interfaccia di rete con [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) e quindi aggiornare la VM con [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). L'esempio seguente rimuove *myNic3* ottenuta da `$nicId` nel passaggio precedente:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Avviare la VM con [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Creare più schede di interfaccia di rete con i modelli
I modelli di Azure Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di interfaccia di rete. I modelli di Resource Manager usano i file JSON dichiarativi per definire l'ambiente. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). È possibile usare *copy* per specificare il numero di istanze da creare:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Per altre informazioni, vedere [Creazione di più istanze con *copy*](../../resource-group-create-multiple.md). 

È anche possibile usare `copyIndex()` per aggiungere un numero a un nome di risorsa. È quindi possibile creare *myNic1*, *MyNic2* e così via. Il codice seguente illustra un esempio di aggiunta del valore di indice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di interfaccia di rete tramite i modelli di Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere [Dimensioni per le macchine virtuali Windows](sizes.md) se si deve creare una VM con più schede di interfacce di rete. Prestare attenzione al numero massimo di schede di interfaccia di rete supportato per ogni dimensione della macchina virtuale. 



