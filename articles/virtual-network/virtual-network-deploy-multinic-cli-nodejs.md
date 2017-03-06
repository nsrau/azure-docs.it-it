---
title: "Creare una VM con più schede di interfaccia di rete - interfaccia della riga di comando 1.0 di Azure| Documentazione Microsoft"
description: "Informazioni su come creare una VM con più schede di interfaccia di rete usando l&quot;interfaccia della riga di comando 1.0 di Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b95bcb38664718bf25ec6981c803415790c6da3d
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-10"></a>Creare una VM con più schede di interfaccia di rete usando l'interfaccia della riga di comando 1.0 di Azure

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md).  Questo articolo illustra il modello di distribuzione Resource Manager che Microsoft consiglia di usare per le distribuzioni più recenti in sostituzione del [modello di distribuzione classica](virtual-network-deploy-multinic-classic-cli.md).
>

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per i server Web e un gruppo di risorse denominato *IaaSStory-BackEnd* per i server di database. È possibile completare questa attività usando l'interfaccia della riga di comando di Azure 1.0 (questo articolo) o l'[interfaccia della riga di comando di Azure 2.0](virtual-network-deploy-static-pip-arm-cli.md). I valori in "" per le variabili nei passaggi seguenti creano le risorse con le impostazioni usate nello scenario. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

## <a name="prerequisites"></a>Prerequisiti
Prima di creare i server di database, è necessario creare il gruppo di risorse *IaaSStory* con tutte le risorse richieste per questo scenario. Per creare le risorse, seguire questa procedura:

1. Passare alla [pagina del modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina del modello, a destra del **gruppo di risorse padre**, fare clic su **Distribuisci in Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di anteprima di Azure per distribuire il gruppo di risorse.

> [!IMPORTANT]
> Assicurarsi che i nomi degli account di archiviazione siano univoci. In Azure non sono infatti ammessi nomi di account di archiviazione duplicati.
> 

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Creare le macchine virtuali di back-end
Le macchine virtuali di back-end dipendono dalla creazione delle risorse seguenti:

* **Account di archiviazione per i dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare che la posizione di Azure distribuita supporti l'archiviazione premium.
* **Schede di rete**. Ogni macchina virtuale ha due schede di rete, una per l'accesso al database e una per la gestione.
* **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - avviare lo script
È possibile scaricare lo script di bash completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

    ```azurecli
    existingRGName="IaaSStory"
    location="westus"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    remoteAccessNSGName="NSG-RemoteAccess"
    ```
2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

    ```azurecli
    backendRGName="IaaSStory-Backend"
    prmStorageAccountName="wtestvnetstorageprm"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    publisher="Canonical"
    offer="UbuntuServer"
    sku="14.04.2-LTS"
    version="latest"
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskName="datadisk"
    nicNamePrefix="NICDB"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

3. Recuperare l'ID per la subnet `BackEnd` in cui verranno create le macchine virtuali. È necessario farlo poiché le schede di rete associate a questa subnet si trovano in un gruppo di risorse diverso.

    ```azurecli
    subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
            --vnet-name $vnetName \
            --name $backendSubnetName|grep Id)"
    subnetId=${subnetId#*/}
    ```

   > [!TIP]
   > Il primo comando precedente usa [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) e [manipolazione delle stringhe](http://tldp.org/LDP/abs/html/string-manipulation.html) (in particolare, la rimozione di sottostringhe).
   >

4. Recuperare l'ID per l’NSG `NSG-RemoteAccess` . È necessario farlo poiché le schede di rete associate a tale NSG sono in un gruppo di risorse diverso.

    ```azurecli
    nsgId="$(azure network nsg show --resource-group $existingRGName \
        --name $remoteAccessNSGName|grep Id)"
        nsgId=${nsgId#*/}
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - creare le risorse necessarie per le macchine virtuali

1. Creare un nuovo gruppo di risorse per tutte le risorse back-end. Si noti l'utilizzo della variabile `$backendRGName` per il nome del gruppo di risorse e `$location` per l'area di Azure.

    ```azurecli
    azure group create $backendRGName $location
    ```

2. Creare un account di archiviazione premium per i dischi dati e il sistema operativo da usare per le macchine virtuali.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --resource-group $backendRGName \
        --location $location \
        --type PLRS
    ```

3. Creare un set di disponibilità per le macchine virtuali.

    ```azurecli
    azure availset create --resource-group $backendRGName \
        --location $location \
        --name $avSetName
    ```

### <a name="step-3---create-the-nics-and-back-end-vms"></a>Passaggio 3 - Creare le schede di rete e le macchine virtuali di back-end

1. Avviare un ciclo per creare più macchine virtuali, in base alle `numberOfVMs` variabili.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Per ogni macchina virtuale, creare una scheda di rete per l'accesso al database.

    ```azurecli
    nic1Name=$nicNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x
    azure network nic create --name $nic1Name \
        --resource-group $backendRGName \
        --location $location \
        --private-ip-address $ipAddress1 \
        --subnet-id $subnetId
    ```

3. Per ogni macchina virtuale, creare una scheda di rete per l'accesso remoto. Si noti il parametro `--network-security-group` utilizzato per associare la scheda di rete a un NSG.

    ```azurecli
    nic2Name=$nicNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    azure network nic create --name $nic2Name \
        --resource-group $backendRGName \
        --location $location \
        --private-ip-address $ipAddress2 \
        --subnet-id $subnetId $vnetName \
        --network-security-group-id $nsgId
    ```

4. Creare la macchina virtuale.

    ```azurecli
    azure vm create --resource-group $backendRGName \
        --name $vmNamePrefix$suffixNumber \
        --location $location \
        --vm-size $vmSize \
        --subnet-id $subnetId \
        --availset-name $avSetName \
        --nic-names $nic1Name,$nic2Name \
        --os-type linux \
        --image-urn $publisher:$offer:$sku:$version \
        --storage-account-name $prmStorageAccountName \
        --storage-account-container-name vhds \
        --os-disk-vhd $osDiskName$suffixNumber.vhd \
        --admin-username $username \
        --admin-password $password
    ```

5. Per ogni macchina virtuale, creare due dischi dati e terminare il ciclo con il comando `done` .

    ```azurecli
    azure vm disk attach-new --resource-group $backendRGName \
        --vm-name $vmNamePrefix$suffixNumber \
        --storage-account-name $prmStorageAccountName \
        --storage-account-container-name vhds \
        --vhd-name $dataDiskName$suffixNumber-1.vhd \
        --size-in-gb $diskSize \
        --lun 0

    azure vm disk attach-new --resource-group $backendRGName \
        --vm-name $vmNamePrefix$suffixNumber \        
        --storage-account-name $prmStorageAccountName \
        --storage-account-container-name vhds \
        --vhd-name $dataDiskName$suffixNumber-2.vhd \
        --size-in-gb $diskSize \
        --lun 1
        done
    ```

### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script.
Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal terminale **Bash** . Verrà visualizzato l'output iniziale, come illustrato di seguito.
   
        info:    Executing command group create
        info:    Getting resource group IaaSStory-Backend
        info:    Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command availset create
        info:    Looking up the availability set "ASDB"
        info:    Creating availability set "ASDB"
        info:    availset create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-DA"
        info:    Creating network interface "NICDB1-DA"
        info:    Looking up the network interface "NICDB1-DA"
        data:    Id                              : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
        data:    Name                            : NICDB1-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-RA"
        info:    Creating network interface "NICDB1-RA"
        info:    Looking up the network interface "NICDB1-RA"
        data:    Id                              : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
        data:    Name                            : NICDB1-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.54
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB1"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB1-DA"
        info:    Looking up the NIC "NICDB1-RA"
        info:    Creating VM "DB1"
2. Dopo alcuni minuti, l'esecuzione verrà terminata e il resto dell'output verrà visualizzato come mostrato di seguito.
   
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-DA"
        info:    Creating network interface "NICDB2-DA"
        info:    Looking up the network interface "NICDB2-DA"
        data:    Id                              : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
        data:    Name                            : NICDB2-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.5
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-RA"
        info:    Creating network interface "NICDB2-RA"
        info:    Looking up the network interface "NICDB2-RA"
        data:    Id                              : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
        data:    Name                            : NICDB2-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.55
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB2"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB2-DA"
        info:    Looking up the NIC "NICDB2-RA"
        info:    Creating VM "DB2"
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK


