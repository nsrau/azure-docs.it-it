---
title: Creare una route definita dall'utente per instradare il traffico di rete attraverso un'appliance di rete virtuale - PowerShell | Microsoft Docs
description: Informazioni su come creare una route definita dall'utente per eseguire l'override del routing predefinito di Azure instradando il traffico di rete attraverso un'appliance di rete virtuale.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Creare una route definita dall'utente - PowerShell

Questa esercitazione illustra come creare route definite dall'utente per il routing del traffico tra due subnet della [rete virtuale](virtual-networks-overview.md) attraverso un'appliance di rete virtuale. Un'appliance di rete virtuale è una macchina virtuale che esegue un'applicazione di rete, ad esempio un firewall. Per altre informazioni sulle appliance di rete virtuali preconfigurate distribuibili in una rete virtuale di Azure, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando si creano subnet in una rete virtuale, Azure crea [route di sistema](virtual-networks-udr-overview.md#system-routes) predefinite che consentono le comunicazioni tra le risorse in tutte le subnet, come visualizzato nell'immagine seguente:

![Route predefinite](./media/create-user-defined-route/default-routes.png)

In questa esercitazione si crea una rete virtuale con una subnet pubblica, una subnet privata e una subnet perimetrale, come illustrato nell'immagine seguente. In genere, i server Web vengono distribuiti in una subnet pubblica e un server di applicazioni o di database viene distribuito in una subnet privata. Si crea una macchina virtuale che svolge la funzione di appliance di rete virtuale nella subnet perimetrale e, facoltativamente, si crea in ogni subnet una macchina virtuale che comunica tramite l'appliance di rete virtuale. Tutto il traffico tra le subnet pubblica e privata viene instradato attraverso l'appliance, come illustrato nell'immagine seguente:

![Route definite dall'utente](./media/create-user-defined-route/user-defined-routes.png)

Questo articolo descrive la procedura per creare una route definita dall'utente tramite il modello di distribuzione Resource Manager, il modello consigliato per la creazione di route definite dall'utente. Se è necessario creare una route definita dall'utente (distribuzione classica), vedere [Create a user-defined route (classic)](virtual-network-create-udr-classic-ps.md) (Creare una route definita dall'utente - distribuzione classica). Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Creare route e un'appliance di rete virtuale

È possibile installare e configurare la versione più recente del modulo [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell sul PC oppure fare clic sul pulsante **Prova** in uno degli script per eseguirli in Azure Cloud Shell, in cui è installato il modulo AzureRM di PowerShell.
 
1. **Prerequisito**: creare una rete virtuale con due subnet completando la procedura descritta in [Crea rete virtuale](#create-a-virtual-network).
2. Se si esegue PowerShell dal computer locale, accedere ad Azure con l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando il comando `login-azurermaccount`. Se si usa Cloud Shell, si viene connessi automaticamente. È possibile che sia necessario riavviare Cloud Shell per eseguire la transizione dalla shell Bash usata durante la creazione della rete virtuale obbligatoria.
3. Impostare alcune variabili che dovranno essere usate in tutti i passaggi:

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Creare una subnet perimetrale nella rete virtuale esistente:

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Creare un indirizzo IP pubblico statico per la macchina virtuale dell'appliance di rete virtuale.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Creare la macchina virtuale dell'appliance di rete virtuale. L'appliance di rete virtuale può essere una macchina virtuale che esegue un sistema operativo Linux o Windows. Per creare la macchina virtuale, copiare lo script per il sistema operativo in uso e incollarlo nella sessione di PowerShell. Se si crea una macchina virtuale Windows, incollare lo script in un editor di testo, modificare il "valore" della variabile $cred e quindi incollare il testo modificato nella sessione di PowerShell:

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet di una rete virtuale. Creare una route per modificare l'impostazione predefinita di routing di Azure e fare in modo che il traffico dalla subnet *Public* sia instradato all'appliance di rete virtuale, anziché direttamente alla subnet *Private*.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Creare una tabella di route per la subnet *Public*.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Associare la tabella di route alla subnet Public. Se si associa una tabella di route a una subnet, Azure indirizza tutto il traffico in uscita dalla subnet in base alle route presenti nella tabella di route. Una tabella di route può essere associata a nessuna o a più subnet, mente una subnet può essere associata a nessuna o a una tabella di route.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Creare una route per instradare il traffico dalla subnet *Private* alla subnet *Public* attraverso la macchina virtuale dell'appliance di rete virtuale.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Creare la tabella di route per la subnet *Private*.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Associare la tabella di route alla subnet *Private*.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Facoltativo:** creare una macchina virtuale nelle subnet Public e Private e verificare che le comunicazioni tra le macchine virtuali vengano inoltrate attraverso l'appliance di rete virtuale, completando la procedura descritta in [Convalidare il routing](#validate-routing).
15. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-resources).

## <a name="validate-routing"></a>Convalidare il routing

1. Se non è ancora stato fatto, completare i passaggi in [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance).
2. Fare clic sul pulsante **Prova** nella finestra di dialogo visualizzata per aprire Azure Cloud Shell. Se richiesto, accedere ad Azure usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell è una shell Bash gratuita con l'interfaccia della riga di comando di Azure preinstallata. 

    Gli script seguenti creano due macchine virtuali, una nella subnet *Public* e una nella subnet *Private*. Gli script abilitano anche l'inoltro IP per l'interfaccia di rete nel sistema operativo dell'appliance virtuale di rete, per consentire al sistema operativo di inoltrare il traffico attraverso l'interfaccia di rete. In genere, un'appliance di rete virtuale di produzione ispeziona il traffico prima di inoltrarlo, ma in questa esercitazione l'appliance semplice inoltra il traffico senza ispezionarlo. 

    Fare clic sul pulsante **Copia** nello script **Linux** o **Windows** che segue e incollare il contenuto dello script in un editor di testo. Cambiare la password della variabile *adminPassword* e quindi incollare lo script in Azure Cloud Shell. Eseguire lo script per il sistema operativo selezionato al momento della creazione dell'appliance di rete virtuale nel passaggio 7 di [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Convalidare la comunicazione tra le macchine virtuali nelle subnet Public e Private. 

    - Aprire una connessione [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) o [Desktop remoto](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) all'indirizzo IP pubblico della macchina virtuale *myVm-Public*.
    - Al prompt dei comandi della macchina virtuale *myVm-Public* immettere `ping myVm-Private`. Si ricevono risposte perché l'appliance virtuale di rete inoltra il traffico dalla subnet pubblica alla subnet privata.
    - Dalla macchina virtuale *myVm-Public* eseguire un tracciamento delle route tra le macchine virtuali nelle subnet pubblica e privata. Immettere il comando appropriato tra quelli seguenti, a seconda del sistema operativo installato nelle macchine virtuali nelle subnet Public e Private:
        - **Windows**: al prompt dei comandi eseguire il comando `tracert myvm-private`.
        - **Ubuntu**: eseguire il comando `tracepath myvm-private`.
      Il traffico passa attraverso 10.0.2.4 (l'appliance di rete virtuale) prima di raggiungere 10.0.1.4 (la macchina virtuale nella subnet privata). 
    - Completare i passaggi precedenti eseguendo il collegamento alla macchina virtuale *myVm-Private* ed effettuando ping alla macchina virtuale *myVm-Public*. Il tracciamento route visualizza le comunicazioni che viaggiano attraverso 10.0.2.4 prima di raggiungere 10.0.0.4 (la macchina virtuale nella subnet Public).
    
      > [!NOTE]
      > I passaggi precedenti consentono di verificare il routing tra indirizzi IP privati di Azure. Se si vuole inoltrare il traffico a indirizzi IP pubblici o eseguire la funzione di proxy tramite un'appliance virtuale di rete:
      > - L'appliance deve offrire funzionalità di conversione degli indirizzi di rete o di proxy. In caso di conversione degli indirizzi di rete, l'appliance deve convertire da sola l'indirizzo IP di origine e quindi inoltrare la richiesta all'indirizzo IP pubblico. Sia che l'appliance abbia convertito l'indirizzo di origine o che abbia svolto la funzione di proxy, Azure converte l'indirizzo IP privato dell'appliance virtuale di rete in un indirizzo IP pubblico. Per altre informazioni sui diversi metodi usati da Azure per convertire gli indirizzi IP privati in indirizzi IP pubblici, vedere [Informazioni sulle connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Una route aggiuntiva nella tabella di route, ad esempio una con prefisso 0.0.0.0/0, tipo hop successivo VirtualAppliance e indirizzo IP hop successivo 10.0.2.4 (nello script di esempio precedente).
      >
    - **Facoltativo**: usare la funzionalità Hop successivo di Network Watcher di Azure per convalidare l'hop successivo tra due macchine virtuali in Azure. Prima di usare Network Watcher è necessario [creare un'istanza di Network Watcher di Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per l'area in cui si intende usarlo. In questa esercitazione viene usata l'area degli Stati Uniti orientali. Dopo aver abilitato un'istanza di Network Watcher per l'area, immettere il comando seguente per visualizzare le informazioni sull'hop successivo tra le macchine virtuali nelle subnet Public e Private:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       L'output restituisce *10.0.2.4* come **nextHopIpAddress** e *VirtualAppliance* come **nextHopType**.

> [!NOTE]
> Per illustrare i concetti di questa esercitazione, gli indirizzi IP pubblici vengono assegnati alle macchine virtuali nelle subnet Public e Private e tutti gli accessi alle porte di rete sono abilitati in Azure per entrambe le macchine virtuali. Quando si creano macchine virtuali per ambienti di produzione, è probabile che non si assegnino indirizzi IP pubblici a tali macchine e che il traffico di rete alla subnet Private venga filtrato implementando un'appliance di rete virtuale all'ingresso della subnet o assegnando un gruppo di sicurezza di rete alle subnet, all'interfaccia di rete o a entrambe. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Per questa esercitazione è necessaria una rete virtuale con due subnet. Fare clic sul pulsante **Prova** nella finestra di dialogo seguente per creare rapidamente una rete virtuale. Quando si fa clic su **Prova** viene aperto [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Azure Cloud Shell esegue PowerShell o una shell Bash. In questa sezione viene usata la shell Bash per creare la rete virtuale. La shell Bash include l'installazione dell'interfaccia della riga di comando di Azure. Se richiesto da Cloud Shell, accedere ad Azure usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Per creare la rete virtuale usata in questa esercitazione, fare clic sul pulsante **Copia** nella finestra seguente e quindi incollare lo script in Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Per altre informazioni su come usare il portale, PowerShell o un modello Azure Resource Manager per creare una rete virtuale, vedere [Crea rete virtuale](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Eliminare le risorse

Al termine di questa esercitazione, è possibile eliminare le risorse che sono state create per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso. In PowerShell immettere il comando seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Creare un'[appliance di rete virtuale a disponibilità elevata](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- In molti casi alle appliance di rete virtuali sono assegnate più interfacce di rete e più indirizzi IP. Informazioni su come [aggiungere interfacce di rete a una macchina virtuale esistente](virtual-network-network-interface-vm.md#vm-add-nic) e su come [aggiungere indirizzi IP a un'interfaccia di rete esistente](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile associare almeno due interfacce di rete a una macchina virtuale di qualsiasi dimensione, ma il numero massimo di interfacce di rete varia a seconda delle dimensioni della macchina virtuale. Per informazioni sul numero di interfacce di rete supportate dalle macchine virtuali di diverse dimensioni, vedere le dimensioni delle macchine virtuali per [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Creare una route definita dall'utente per indirizzare il tunneling del traffico locale su una [connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
