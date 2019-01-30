---
title: Creare una macchina virtuale Windows con PowerShell in Azure Stack | Microsoft Docs
description: Creare una macchina virtuale Windows con PowerShell in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9b9a041f1e4269538488d17ee73a7ffdd138c8ab
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251814"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Guida introduttiva: creare una macchina virtuale Windows Server usando PowerShell in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Windows Server 2016 usando PowerShell per Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. Questo articolo fornisce anche i passaggi per:

* Connettersi alla macchina virtuale con un client remoto.
* Installare il server web IIS e visualizzare la home page predefinita.
* Pulire le risorse.

>[!NOTE]
 È possibile eseguire i passaggi descritti in questo articolo da Azure Stack Development Kit o da un client esterno basato su Windows, se si è connessi tramite VPN.

## <a name="prerequisites"></a>Prerequisiti

* Assicurarsi che l'operatore di Azure Stack è stato aggiunto il **Windows Server 2016** immagini nel Marketplace di Azure Stack.

* Azure Stack richiede una versione specifica di Azure PowerShell per creare e gestire le risorse. Se non hai configurato per lo Stack di Azure PowerShell, seguire la procedura per [installare](azure-stack-powershell-install.md) PowerShell.

* Con Stack di Azure PowerShell impostare, è necessario connettersi all'ambiente Azure Stack. Per istruzioni, vedere [connettersi ad Azure Stack con PowerShell come utente](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui Azure Stack le risorse vengono distribuite e gestite. Il kit di sviluppo o il sistema integrato Azure Stack, eseguire il blocco di codice seguente per creare un gruppo di risorse. Vengono assegnati i valori per tutte le variabili in questo documento, è possibile usare questi valori oppure assegnare nuovi valori.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Creare le risorse di archiviazione

Creare un account di archiviazione e un contenitore di archiviazione per archiviare l'immagine di Windows Server 2016.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Creare risorse di rete

Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per fornire la connettività di rete alla macchina virtuale.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Creare un gruppo di sicurezza di rete e una regola del gruppo di sicurezza di rete

Il gruppo di sicurezza di rete protegge la macchina virtuale con le regole in ingresso e in uscita. Consente di creare una regola in ingresso per la porta 3389 per consentire le connessioni Desktop remoto in ingresso e una regola in ingresso per la porta 80 per consentire il traffico web in ingresso.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Creare una scheda di rete per la macchina virtuale

La scheda di rete connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una configurazione di macchina virtuale. Questa configurazione include le impostazioni usate quando si distribuisce la macchina virtuale. Ad esempio: le credenziali, le dimensioni e l'immagine di macchina virtuale.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

In remoto alla macchina virtuale creata nel passaggio precedente, è necessario l'indirizzo IP pubblico. Eseguire il comando seguente per ottenere l'indirizzo IP pubblico della macchina virtuale:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Usare il comando seguente per creare una sessione Desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo publicIPAddress della macchina virtuale. Quando richiesto, immettere il nome utente e password usati durante la creazione della macchina virtuale.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installare IIS tramite PowerShell

Dopo avere eseguito l'accesso alla macchina virtuale di Azure, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web. Aprire un prompt di PowerShell ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Con IIS installato e con la porta 80 aperta sulla macchina virtuale, è possibile usare un web browser preferito per visualizzare la pagina iniziale di IIS predefinita. Usare la *publicIpAddress* è documentato nella sezione precedente per visitare la pagina predefinita.

![Sito IIS predefinito](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Eliminare la macchina virtuale

Quando non sono più necessari, usare il comando seguente per rimuovere il gruppo di risorse che contiene la macchina virtuale e le relative risorse correlate:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una semplice macchina virtuale Windows. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
