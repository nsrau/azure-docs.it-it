---
title: "Creare una VM (classica) con più schede di interfaccia di rete - interfaccia della riga di comando 1.0 di Azure| Documentazione Microsoft"
description: "Informazioni su come creare una VM (classica) con più schede di interfaccia di rete usando l'interfaccia della riga di comando 1.0 di Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b62421b7289650818748d0016dccfdf42ef0a768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Creare una VM (classica) con più schede di interfaccia di rete usando l'interfaccia della riga di comando 1.0 di Azure

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

È possibile creare macchine virtuali (VM) in Azure e collegare più interfacce di rete (NIC) a ciascuna delle macchine virtuali. Più schede di interfaccia rete consentono la separazione dei tipi di traffico tra schede di interfaccia di rete. Ad esempio, una scheda di interfaccia di rete può comunicare con Internet, mentre un'altra comunica solo con le risorse interne non connesse a Internet. La possibilità di separare il traffico di rete tra più schede di interfaccia di rete è necessaria per molte appliance virtuali di rete, ad esempio soluzioni di ottimizzazione WAN e di distribuzione delle applicazioni.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come seguire questa procedura con il [modello di distribuzione Resource Manager](virtual-network-deploy-multinic-arm-cli.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per i server Web e un gruppo di risorse denominato *IaaSStory-BackEnd* per i server di database.

## <a name="prerequisites"></a>Prerequisiti
Prima di creare i server di database, è necessario creare il gruppo di risorse *IaaSStory* con tutte le risorse richieste per questo scenario. Per creare le risorse, seguire questa procedura. Creare una rete virtuale seguendo la procedura riportata nell'articolo [Creare una rete virtuale](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuire le macchine virtuali di back-end
Le macchine virtuali di back-end dipendono dalla creazione delle risorse seguenti:

* **Account di archiviazione per i dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare che la posizione di Azure distribuita supporti l'archiviazione premium.
* **Schede di rete**. Ogni macchina virtuale ha due schede di rete, una per l'accesso al database e una per la gestione.
* **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - avviare lo script
È possibile scaricare lo script di bash completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Per modificare lo script da usare nell'ambiente, seguire questa procedura:

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - creare le risorse necessarie per le macchine virtuali
1. Creare un nuovo servizio cloud per tutte le macchine virtuali di back-end. Si noti l'utilizzo della `$backendCSName` variabile per il nome del gruppo di risorse e `$location` per l'area di Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Creare un account di archiviazione premium per i dischi dati e del sistema operativo da usare mediante le macchine virtuali.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Passaggio 3 - creare macchine virtuali con più NIC
1. Avviare un ciclo per creare più macchine virtuali, in base alle `numberOfVMs` variabili.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Per ogni macchina virtuale, specificare il nome e l’indirizzo IP di ciascuna delle due schede di rete.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Creare la macchina virtuale Si noti l'utilizzo del parametro `--nic-config`, contenente un elenco di tutte le schede di rete con nome, subnet e indirizzo IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Per ogni macchina virtuale, creare due dischi dati.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script.
Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal terminale **Bash** . Verrà visualizzato l'output iniziale, come illustrato di seguito.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Dopo alcuni minuti, l'esecuzione verrà terminata e il resto dell'output verrà visualizzato come mostrato di seguito.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
