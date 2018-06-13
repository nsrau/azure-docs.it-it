---
title: Creare una VM con un indirizzo IP pubblico statico - Azure PowerShell | Documentazione Microsoft
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante Azure PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525133"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Creare una VM con un indirizzo IP pubblico statico mediante Azure PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Avviare lo script
È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione. I valori seguenti si riferiscono allo scenario usato in questo articolo:

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Creare le risorse necessarie per la macchina virtuale
Prima di creare una VM, è necessario disporre di un gruppo di risorse, una rete virtuale, un IP pubblico e una scheda di rete utilizzabili dalla VM.

1. Creare un nuovo gruppo di risorse.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Creare rete virtuale e subnet.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Creare la risorsa di IP pubblico. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Creare l'interfaccia di rete (NIC) per la VM nella subnet creata in precedenza, con l'IP pubblico. Notare che il primo cmdlet che recupera la rete virtuale da Azure è necessario perché `Set-AzureRmVirtualNetwork` è stato eseguito per modificare la rete virtuale esistente.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Creare un account di archiviazione per ospitare l'unità del sistema operativo della VM.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Creare la VM
Ora che tutte le risorse necessarie sono presenti, è possibile creare una nuova VM.

1. Creare l'oggetto di configurazione per la VM.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Ottenere le credenziali per l'account amministratore locale della VM.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Creare un oggetto di configurazione della VM.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Impostare l'immagine del sistema operativo per la VM.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Configurare il disco del sistema operativo.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Aggiungere la scheda di rete alla VM.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Creare la macchina virtuale

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Salvare il file di script.

## <a name="run-the-script"></a>Esecuzione dello script

Dopo aver apportato le modifiche necessarie, eseguire lo script precedente. La creazione della macchina virtuale richiede alcuni minuti.

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale. È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-powershell.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Passaggi successivi

Qualsiasi traffico di rete può scorrere da e verso la VM creata in questo articolo. All'interno di un gruppo di sicurezza di rete è possibile definire regole di sicurezza in entrata e in uscita che limitano il traffico in ingresso e in uscita dall'interfaccia di rete, la subnet o entrambe le risorse. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md).