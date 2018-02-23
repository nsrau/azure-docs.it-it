---
title: Creare una route definita dall'utente per instradare il traffico di rete attraverso un'appliance di rete virtuale - Portale di Azure | Microsoft Docs
description: Informazioni su come creare una route definita dall'utente per eseguire l'override del routing predefinito di Azure e instradare il traffico di rete attraverso un'appliance di rete virtuale.
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
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Creare una route definita dall'utente - Portale di Azure

In questa esercitazione si apprende come creare route definite dall'utente per il routing del traffico tra due subnet [rete virtuale](virtual-networks-overview.md) attraverso un'appliance di rete virtuale. Un'appliance di rete virtuale è una macchina virtuale che esegue un'applicazione di rete, ad esempio un firewall. Per altre informazioni sulle appliance di rete virtuali preconfigurate distribuibili in una rete virtuale di Azure, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando si creano subnet in una rete virtuale, Azure crea [route di sistema](virtual-networks-udr-overview.md#system-routes) predefinite che consentono le comunicazioni tra le risorse in tutte le subnet, come visualizzato nell'immagine seguente:

![Route predefinite](./media/create-user-defined-route/default-routes.png)

In questa esercitazione si crea una rete virtuale con una subnet pubblica, una subnet privata e una subnet perimetrale, come illustrato nell'immagine seguente. In genere, i server Web vengono distribuiti in una subnet pubblica e un server di applicazioni o di database viene distribuito in una subnet privata. Si crea una macchina virtuale che svolge la funzione di appliance di rete virtuale nella subnet perimetrale e, facoltativamente, si crea in ogni subnet una macchina virtuale che comunica tramite l'appliance di rete virtuale. Tutto il traffico tra le subnet pubblica e privata viene instradato attraverso l'appliance, come illustrato nell'immagine seguente:

![Route definite dall'utente](./media/create-user-defined-route/user-defined-routes.png)

Questo articolo descrive la procedura per creare una route definita dall'utente tramite il modello di distribuzione Resource Manager, il modello consigliato per la creazione di route definite dall'utente. Se è necessario creare una route definita dall'utente (distribuzione classica), vedere [Create a user-defined route (classic)](virtual-network-create-udr-classic-ps.md) (Creare una route definita dall'utente - distribuzione classica). Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Creare route e un'appliance di rete virtuale

1. **Prerequisito**: creare una rete virtuale con due subnet completando i passaggi in [Crea rete virtuale](#create-a-virtual-network).
2. Dopo aver creato la rete virtuale, in un Web browser accedere al [portale di Azure](https://portal.azure.com). Accedere usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Il gruppo di risorse è stato creato come parte del prerequisito.
4. Fare clic su **myVnet** come illustrato nell'immagine seguente:

    ![Impostazioni dell'interfaccia di rete](./media/create-user-defined-route/virtual-network.png)

5. Creare una subnet per l'appliance di rete virtuale:
 
    - In **myVnet** fare clic su **Subnet** sotto **IMPOSTAZIONI** sul lato sinistro.
    - Fare clic su **+ Subnet** come illustrato nell'immagine seguente:

        ![Subnet](./media/create-user-defined-route/subnets.png) 
    - Immettere i valori seguenti in **Aggiungi subnet**, quindi fare clic su **OK**:

        |Impostazione|Valore|
        |-----|-----|
        |NOME|Rete perimetrale|
        |Intervallo di indirizzi (blocco CIDR)|10.0.2.0/24|

6. Creare una macchina virtuale per l'appliance di rete virtuale:

    - Sul lato sinistro del portale fare clic su **+ Nuovo**, fare clic su **Calcolo**, quindi fare clic su **Windows Server 2016 Datacenter** o su **Ubuntu Server 16.04 LTS** (Server Ubuntu 16.04 LTS).
    - Immettere i valori seguenti nel pannello **Informazioni di base** visualizzato e quindi fare clic su **OK**.

        |Impostazione|Valore|
        |---|---|
        |NOME|myVm-Nva|
        |Nome utente|azureuser|
        |Password e Conferma password|Una password a scelta|
        |Sottoscrizione|Selezionare la propria sottoscrizione|
        |Gruppo di risorse|Fare clic su **Usa esistente**, quindi selezionare **myResourceGroup**|
        |Località|Stati Uniti orientali|
    - Nel pannello **Scegli una dimensione** fare clic su **DS1_V2 Standard** e quindi su **Seleziona**.
    - Nel pannello **Impostazioni** visualizzato fare clic su **Rete virtuale**. Nel pannello **Scegli rete virtuale** visualizzato fare clic su **myVnet**.
    - Nel pannello **Impostazioni** fare clic su **Subnet**. Fare clic su **DMZ** nel pannello **Scegli subnet** visualizzato. 
    - Mantenere le impostazioni predefinite per le altre opzioni e fare clic su **OK**.
    - Fare clic su **Crea** nel pannello **Crea** visualizzato. La distribuzione della macchina virtuale richiede alcuni minuti.

    > [!NOTE]
    > Oltre a creare la macchina virtuale, per impostazione predefinita il portale di Azure crea un indirizzo IP pubblico e lo assegna alla macchina virtuale. Se si sta distribuendo la macchina virtuale in un ambiente di produzione è possibile scegliere di non assegnare un indirizzo IP pubblico alla macchina virtuale. Sarà comunque possibile accedere all'appliance di rete virtuale da altre macchine virtuali all'interno della rete virtuale. Per altre informazioni sugli indirizzi IP pubblici, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](virtual-network-public-ip-address.md).

7. Assegnare un indirizzo IP privato statico e abilitare l'inoltro IP per l'interfaccia di rete creata dal portale nel passaggio precedente. 
    - Nella casella **Cerca risorse** nella parte superiore del portale digitare *myVm-Nva*.
    - Fare clic su **myVm-Nva** quando la voce viene visualizzata nei risultati della ricerca.
    - Fare clic su **Rete** in **IMPOSTAZIONI** sul lato sinistro.
    - Fare clic sul nome dell'interfaccia di rete in **myVm-Nva - Interfacce di rete**. Il nome è **myvm-nva***X*, dove *X* è un numero assegnato dal portale.
    - Fare clic su **Configurazioni IP** in **IMPOSTAZIONI** per l'interfaccia di rete selezionata, come visualizzato nell'immagine seguente:

        ![Impostazioni dell'interfaccia di rete](./media/create-user-defined-route/network-interface-settings.png)
        
    - Fare clic su **Abilitato** per l'impostazione **Inoltro IP**, quindi fare clic su **Salva**. L'inoltro IP deve essere abilitato per ogni interfaccia di rete che riceve traffico non indirizzato a un indirizzo IP assegnato all'interfaccia stessa. L'attivazione dell'inoltro IP disabilita il controllo di origine/destinazione di Azure per l'interfaccia di rete.
    - Fare clic su **ipconfig1** nell'elenco di configurazioni IP.
    - Fare clic su **Statico** per l'impostazione **Assegnazione** dell'indirizzo IP privato in **ipconfig1**, come illustrato nell'immagine seguente:

        ![Configurazione IP](./media/create-user-defined-route/ip-configuration.png)
    - Come illustrato nell'immagine precedente, immettere *10.0.2.4* per **Indirizzo IP** in **Impostazioni dell'indirizzo IP privato**. L'assegnazione di un indirizzo IP statico all'interfaccia di rete garantisce che l'indirizzo non cambi per la durata della macchina virtuale alla quale è associata l'interfaccia di rete. L'indirizzo immesso non è attualmente assegnato a un'altra risorsa nella subnet DMZ che include l'interfaccia di rete. 
    - Per salvare la configurazione, fare clic su **Salva** in **ipconfig1**. Non chiudere la finestra ipconfig1 fino a quando non si riceve nel portale una notifica di conferma del salvataggio dell'interfaccia di rete.
 
8. Creare due tabelle di route. Le tabelle di route contengono zero o più route:

    - Sul lato sinistro del portale fare clic su **+Nuovo** > **Rete** > **Tabella di route**.
    - Immettere i valori seguenti in **Crea tabella di route** e quindi fare clic su **Crea**:

        |Impostazione|Valore|
        |---|---|
        |NOME|myRouteTable-Public|
        |Sottoscrizione|Selezionare la propria sottoscrizione|
        |Gruppo di risorse|Selezionare **Usa esistente** e quindi selezionare **myResourceGroup**|
        |Località|Stati Uniti orientali|
    
    - Completare di nuovo le fasi del passaggio 8 ma assegnare alla tabella di route il nome *myRouteTable-Private*.
9. Aggiungere le route alla tabella di route *myRouteTable-Public* e associare la tabella di route alla subnet *Public* (pubblica):

    - Nella casella **Cerca risorse** nella parte superiore del portale digitare *myRouteTable-Public*. Fare clic su **myRouteTable-Public** quando la voce viene visualizzata nei risultati della ricerca.
    - In **myRouteTable-Public** fare clic su **Route** nell'elenco **IMPOSTAZIONI**.
    - Fare clic su **+ Aggiungi** in **myRouteTable-Public - Route**.
    -  Per impostazione predefinita Azure instrada il traffico fra tutte le subnet di una rete virtuale. Creare una route per modificare l'impostazione predefinita di routing di Azure e fare in modo che il traffico dalla subnet *Public* (pubblica) sia instradato attraverso l'appliance virtuale di rete anziché direttamente alla subnet *Private* (privata). Immettere i valori seguenti nel pannello **Aggiungi route** visualizzato e quindi fare clic su **OK**:

        |Impostazione|Valore|Spiegazione|
        |---|---|---|
        |Nome route|ToPrivateSubnet|Questa route indirizza il traffico alla subnet Private attraverso l'appliance di rete virtuale.|
        |Prefisso indirizzo|10.0.1.0/24| Tutto il traffico inviato a qualsiasi indirizzo incluso in questo prefisso indirizzo (10.0.1.0 - 10.0.1.254) viene inviato all'appliance di rete virtuale.|
        |Tipo hop successivo| Selezionare **Appliance virtuale**||
        |Indirizzo hop successivo|10.0.2.4| Indirizzo IP privato statico dell'interfaccia di rete collegata all'appliance di rete virtuale. L'unico tipo di hop per il quale è possibile specificare un indirizzo è **Appliance virtuale**.|

    - In **myRouteTable-Public** fare clic su **Subnet** in **Impostazioni**. 
    - Fare clic su **+ Associa** in **myRouteTable-Public - Subnet**.
    - Fare clic su **Rete virtuale** in **Associa subnet** e quindi fare clic su **myVnet**.
    - Fare clic su **Subnet** in **Associa subnet** e quindi fare clic su **Pubblico** in **Scegli subnet**. 
    - Per salvare la configurazione fare clic su **OK** in **Associa subnet**. A una subnet può essere associata una o nessuna tabella di route.
10. Completare di nuovo il passaggio 9 cercando **myRouteTable-Private**, creando una route con le impostazioni seguenti, quindi associando la tabella di route alla subnet **Private** della rete virtuale **myVnet**:

    |Impostazione|Valore|Spiegazione|
    |---|---|---|
    |Nome route|ToPublicSubnet|Questa route indirizza il traffico alla subnet pubblica attraverso l'appliance di rete virtuale.|
    |Prefisso indirizzo|10.0.0.0/24| Tutto il traffico inviato a qualsiasi indirizzo incluso in questo prefisso indirizzo (10.0.0.0 - 10.0.1.254) viene inviato all'appliance di rete virtuale.|
    |Tipo hop successivo| Selezionare **Appliance virtuale**||
    |Indirizzo hop successivo|10.0.2.4||

    Il traffico di rete tra qualsiasi risorsa nelle subnet Private e Public passa attraverso l'appliance di rete virtuale. 
11. **Facoltativo:** creare una macchina virtuale nelle subnet Public e Private e verificare che le comunicazioni tra le macchine virtuali vengano inoltrate attraverso l'appliance di rete virtuale, completando la procedura descritta in [Convalidare il routing](#validate-routing). 
12. **Facoltativo:** eliminare le risorse create in questa esercitazione completando la procedura descritta in [Eliminare le risorse](#delete-resources).

## <a name="validate-routing"></a>Convalidare il routing

1. Se non è ancora stato fatto, completare i passaggi in [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance).
2. Fare clic sul pulsante **Prova** nella finestra di dialogo visualizzata per aprire Azure Cloud Shell. Se richiesto, accedere ad Azure usando l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell è una shell Bash gratuita con l'interfaccia della riga di comando di Azure preinstallata. 

    Gli script seguenti creano due macchine virtuali, una nella subnet *Public* e una nella subnet *Private*. Gli script abilitano anche l'inoltro IP per l'interfaccia di rete nel sistema operativo dell'appliance virtuale di rete, per consentire al sistema operativo di inoltrare il traffico attraverso l'interfaccia di rete. In genere, un'appliance di rete virtuale di produzione ispeziona il traffico prima di inoltrarlo, ma in questa esercitazione l'appliance semplice inoltra il traffico senza ispezionarlo. 

    Fare clic sul pulsante **Copia** nello script **Linux** o **Windows** che segue e incollare il contenuto dello script in un editor di testo. Cambiare la password della variabile *adminPassword*, quindi incollare lo script in Azure Cloud Shell. Eseguire lo script per il sistema operativo selezionato al momento della creazione dell'appliance di rete virtuale nel passaggio 6 di [Creare route e un'appliance di rete virtuale](#create-routes-and-network-virtual-appliance). 

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
    - **Facoltativo**: per convalidare l'hop successivo tra due macchine virtuali in Azure, usare la funzionalità Hop successivo di Network Watcher di Azure. Prima di usare Network Watcher è necessario [creare un'istanza di Network Watcher di Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per l'area in cui si vuole usarlo. In questa esercitazione viene usata l'area degli Stati Uniti orientali. Dopo aver abilitato un'istanza di Network Watcher per l'area, immettere il comando seguente per visualizzare le informazioni sull'hop successivo tra le macchine virtuali nelle subnet Public e Private:
     
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

Al termine di questa esercitazione, è possibile eliminare le risorse che sono state create per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso. Con il portale attivo, completare i passaggi seguenti:

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Creare un'[appliance di rete virtuale a disponibilità elevata](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- In molti casi alle appliance di rete virtuali sono assegnate più interfacce di rete e più indirizzi IP. Informazioni su come [aggiungere interfacce di rete a una macchina virtuale esistente](virtual-network-network-interface-vm.md#vm-add-nic) e su come [aggiungere indirizzi IP a un'interfaccia di rete esistente](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile associare almeno due interfacce di rete a una macchina virtuale di qualsiasi dimensione, ma il numero massimo di interfacce di rete varia a seconda delle dimensioni della macchina virtuale. Per informazioni sul numero di interfacce di rete supportate dalle macchine virtuali di diverse dimensioni, vedere le dimensioni delle macchine virtuali per [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Creare una route definita dall'utente per indirizzare il tunneling del traffico locale su una [connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
