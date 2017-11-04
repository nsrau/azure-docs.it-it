---
title: Creare una route definita dall'utente per instradare il traffico di rete attraverso un'appliance di rete virtuale - Interfaccia della riga di comando di Azure | Microsoft Docs
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
ms.openlocfilehash: affa68b6aeedb031914b12dac711d93c7ed4a47a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>Creare una route definita dall'utente - Interfaccia della riga di comando di Azure

Questa esercitazione illustra come creare route definite dall'utente per il routing del traffico tra due subnet della [rete virtuale](virtual-networks-overview.md) attraverso un'appliance di rete virtuale. Un'appliance di rete virtuale è una macchina virtuale che esegue un'applicazione di rete, ad esempio un firewall. Per altre informazioni sulle appliance di rete virtuali preconfigurate distribuibili in una rete virtuale di Azure, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando si creano subnet in una rete virtuale, Azure crea [route di sistema](virtual-networks-udr-overview.md#system-routes) predefinite che consentono le comunicazioni tra le risorse in tutte le subnet, come visualizzato nell'immagine seguente:

![Route predefinite](./media/create-user-defined-route/default-routes.png)

In questa esercitazione si crea una rete virtuale con una subnet pubblica, una subnet privata e una subnet perimetrale, come illustrato nell'immagine seguente. In genere, i server Web vengono distribuiti in una subnet pubblica e un server di applicazioni o di database viene distribuito in una subnet privata. Si crea una macchina virtuale che svolge la funzione di appliance di rete virtuale nella subnet perimetrale e, facoltativamente, si crea in ogni subnet una macchina virtuale che comunica tramite l'appliance di rete virtuale. Tutto il traffico tra le subnet pubblica e privata viene instradato attraverso l'appliance, come illustrato nell'immagine seguente:

![Route definite dall'utente](./media/create-user-defined-route/user-defined-routes.png)

Questo articolo descrive la procedura per creare una route definita dall'utente tramite il modello di distribuzione Resource Manager, il modello consigliato per la creazione di route definite dall'utente. Se è necessario creare una route definita dall'utente (distribuzione classica), vedere [Create a user-defined route (classic)](virtual-network-create-udr-classic-cli.md) (Creare una route definita dall'utente - distribuzione classica). Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Creare route e un'appliance di rete virtuale

I comandi dell'interfaccia della riga di comando di Azure sono gli stessi, indipendentemente dall'esecuzione dei comandi stessi in Windows, Linux o macOS. Esistono tuttavia differenze di scripting tra le shell dei sistemi operativi. Gli script nei passaggi seguenti richiedono l'installazione dell'interfaccia della riga di comando di Azure e l'esecuzione di comandi in una shell Bash. È possibile [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) sul PC oppure fare clic sul pulsante **Prova** in uno degli script per eseguirli in Azure Cloud Shell.
 
1. **Prerequisito**: creare una rete virtuale con due subnet completando la procedura descritta in [Crea rete virtuale](#create-a-virtual-network).
2. Se si esegue l'interfaccia della riga di comando di Azure dal computer, accedere ad Azure con il comando `az login`. Se si usa Cloud Shell, si viene connessi automaticamente.
3. Impostare alcune variabili che dovranno essere usate in tutti i passaggi rimanenti:

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. Creare una subnet *perimetrale* nella rete virtuale creata nella sezione dei prerequisiti:

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. Creare la macchina virtuale dell'appliance di rete virtuale. Assegnare indirizzi IP statici pubblici e privati all'interfaccia di rete creata dall'interfaccia della riga di comando. Gli indirizzi statici non cambiano per tutta la durata della macchina virtuale. L'appliance di rete virtuale può essere una macchina virtuale che esegue un sistema operativo Linux o Windows. Per creare la macchina virtuale, copiare lo script per il sistema operativo in uso e incollarlo nell'interfaccia della riga di comando. Se si crea una macchina virtuale Windows, incollare lo script in un editor di testo, modificare il valore della variabile *AdminPassword* e quindi incollare il testo modificato nell'interfaccia della riga di comando.

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. Abilitare l'inoltro IP per l'interfaccia di rete dell'appliance di rete virtuale. Se si abilita l'inoltro IP per un'interfaccia di rete, Azure non controlla l'indirizzo IP di origine/destinazione. Se non si abilita questa impostazione, il traffico destinato a un indirizzo IP diverso dalla scheda di interfaccia di rete che lo riceve viene eliminato da Azure.

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. Creare una tabella di route per la subnet *Public*.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet di una rete virtuale. Creare una route per modificare l'impostazione predefinita di routing di Azure e fare in modo che il traffico dalla subnet Public sia instradato attraverso l'appliance di rete virtuale anziché direttamente alla subnet Private.

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. Associare la tabella di route *myRouteTable-Public* alla subnet *Public*. Se si associa una tabella di route a una subnet, Azure indirizza tutto il traffico in uscita dalla subnet in base alle route presenti nella tabella di route. Una tabella di route può essere associata a nessuna o a più subnet, mente una subnet può essere associata a nessuna o a una tabella di route.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. Creare la tabella di route per la subnet *Private*.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. Creare una route per instradare il traffico dalla subnet *Private* alla subnet *Public* attraverso la macchina virtuale dell'appliance di rete virtuale.

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. Associare la tabella di route alla subnet *Private*.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **Facoltativo:** creare una macchina virtuale nelle subnet Public e Private e verificare che le comunicazioni tra le macchine virtuali vengano inoltrate attraverso l'appliance di rete virtuale, completando la procedura descritta in [Convalidare il routing](#validate-routing).
14. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-resources).

## <a name="validate-routing"></a>Convalidare il routing

1. Se non è ancora stato fatto, completare i passaggi in [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance).
2. Fare clic sul pulsante **Prova** nella finestra di dialogo visualizzata per aprire Azure Cloud Shell. Se richiesto, accedere ad Azure usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell è una shell Bash gratuita con l'interfaccia della riga di comando di Azure preinstallata. 

    Gli script seguenti creano due macchine virtuali, una nella subnet *Public* e una nella subnet *Private*. Gli script abilitano anche l'inoltro IP per l'interfaccia di rete nel sistema operativo dell'appliance virtuale di rete, per consentire al sistema operativo di inoltrare il traffico attraverso l'interfaccia di rete. In genere, un'appliance di rete virtuale di produzione ispeziona il traffico prima di inoltrarlo, ma in questa esercitazione l'appliance semplice inoltra il traffico senza ispezionarlo. 

    Fare clic sul pulsante **Copia** nello script **Linux** o **Windows** che segue e incollare il contenuto dello script in un editor di testo. Cambiare la password della variabile *adminPassword* e quindi incollare lo script in Azure Cloud Shell. Eseguire lo script per il sistema operativo selezionato al momento della creazione dell'appliance di rete virtuale nel passaggio 5 di [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance). 

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
    - Da un prompt dei comandi nella macchina virtuale *myVm-Public* immettere `ping myVm-Private`. Si ricevono risposte perché l'appliance di rete virtuale inoltra il traffico dalla subnet pubblica alla subnet privata.
    - Dalla macchina virtuale *myVm-Public* eseguire un tracciamento delle route tra le macchine virtuali nelle subnet pubblica e privata. Immettere il comando appropriato tra quelli seguenti, a seconda del sistema operativo installato nelle macchine virtuali nelle subnet Public e Private:
        - **Windows**: dal prompt dei comandi eseguire il comando `tracert myvm-private`.
        - **Ubuntu**: eseguire il comando `tracepath myvm-private`.
      Il traffico passa attraverso 10.0.2.4 (l'appliance di rete virtuale) prima di raggiungere 10.0.1.4 (la macchina virtuale nella subnet privata). 
    - Completare i passaggi precedenti eseguendo il collegamento alla macchina virtuale *myVm-Private* ed effettuando ping alla macchina virtuale *myVm-Public*. Il tracciamento delle route mostra le comunicazioni che viaggiano attraverso 10.0.2.4 prima di raggiungere 10.0.0.4 (la macchina virtuale nella subnet Public).
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

Al termine di questa esercitazione, è possibile eliminare le risorse che sono state create per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso. Nella sessione dell'interfaccia della riga di comando immettere il comando seguente:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- Creare un'[appliance di rete virtuale a disponibilità elevata](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- In molti casi alle appliance di rete virtuali sono assegnate più interfacce di rete e più indirizzi IP. Informazioni su come [aggiungere interfacce di rete a una macchina virtuale esistente](virtual-network-network-interface-vm.md#vm-add-nic) e su come [aggiungere indirizzi IP a un'interfaccia di rete esistente](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile associare almeno due interfacce di rete a una macchina virtuale di qualsiasi dimensione, ma il numero massimo di interfacce di rete varia a seconda delle dimensioni della macchina virtuale. Per informazioni sul numero di interfacce di rete supportate dalle macchine virtuali di diverse dimensioni, vedere le dimensioni delle macchine virtuali per [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Creare una route definita dall'utente per indirizzare il tunneling del traffico locale su una [connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
