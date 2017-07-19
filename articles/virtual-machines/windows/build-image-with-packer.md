---
title: Come creare immagini di macchine virtuali di Windows in Azure con Packer | Microsoft Docs
description: Informazioni su come usare Packer per creare immagini di macchine virtuali di Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/13/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 035286e74e66cf1eb12a51551fa55a2a0ea5517c
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Come usare Packer per creare immagini di macchine virtuali di Windows in Azure
Ogni macchina virtuale (VM, Virtual Machine) in Azure viene creata a partire da un'immagine che ne definisce la distribuzione di Windows e la versione del sistema operativo. Le immagini possono includere applicazioni e configurazioni preinstallate. In Microsoft Azure Marketplace sono disponibili molte prime immagini e immagini di terze parti per i sistemi operativi e gli ambienti applicativi più diffusi. In alternativa, è possibile creare immagini personalizzate su misura per le proprie esigenze. Questo articolo illustra in dettaglio come definire e compilare immagini personalizzate in Azure tramite lo strumento open source [Packer](https://www.packer.io/).


## <a name="create-supporting-azure-resources"></a>Creare risorse di supporto di Azure
Durante il processo di compilazione della macchina virtuale di origine Packer crea risorse di Azure temporanee. Per acquisire la macchina virtuale di origine per usarla come immagine, è necessario definire un gruppo di risorse e un account di archiviazione, all'interno dei quali verrà archiviato l'output del processo di compilazione di Packer.

Creare prima di tutto un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

Creare quindi un account di archiviazione con [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). I nomi degli account di archiviazione devono essere univoci, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole. L'esempio seguente crea un account di archiviazione denominato *mystorageaccount*:

```powershell
$storageAccountName = "mystorageaccount"
New-AzureRmStorageAccount -ResourceGroupName $rgName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"
```


## <a name="create-azure-credentials"></a>Creare credenziali di Azure
Per eseguire l'autenticazione con Azure, Packer usa un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come Packer. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente.

Creare un'entità servizio con [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) e assegnare le autorizzazioni per consentire all'entità servizio di creare e gestire risorse con [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" -Password "P@ssw0rd!"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Per eseguire l'autenticazione in Azure è anche necessario ottenere l'ID del tenant e l'ID della sottoscrizione di Azure con [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId
$sub.SubscriptionId
```

Questi due ID verranno usati nel passaggio successivo.


## <a name="define-packer-template"></a>Definire un modello di Packer
Per compilare immagini, è necessario creare un modello come file JSON. Nel modello si definiscono i compilatori e gli strumenti di provisioning che eseguono il processo di compilazione effettivo. Packer è dotato di uno [strumento di provisioning per Azure](https://www.packer.io/docs/builders/azure.html) che consente di definire risorse di Azure, ad esempio le credenziali delle entità servizio create nel passaggio precedente.

Creare un file con nome *windows.json* e incollare al suo interno il contenuto seguente. Immettere valori personalizzati per i parametri seguenti:

| Parametro       | Origine |
|-----------------|----------------------------------------------------|
| *client_id*      | ID entità servizio di visualizzazione con `$sp.applicationId` |
| *client_secret*  | Password specificata in`$securePassword` |
| *tenant_id*      | Output del comando `$sub.TenantId` |
| *subscription_id* | Output del comando `$sub.SubscriptionId` |
| *object_id*       | ID oggetto entità servizio di visualizzazione con `$sp.Id` |
| *storage_account* | Nome specificato in `$storageAccountName` |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force}",
      "& $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /shutdown /quiet"
    ]
  }]
}
```

Questo modello compila una macchina virtuale di Windows Server 2016, installa IIS e quindi generalizza la macchina virtuale con Sysprep.


## <a name="build-packer-image"></a>Compilare l'immagine in Packer
Se Packer non è installato nel computer locale, [seguire le istruzioni di installazione di Packer](https://www.packer.io/docs/install/index.html).

Compilare l'immagine specificando il file del modello di Packer come segue:

```bash
./packer build windows.json
```

Ecco un esempio di output dei comandi precedenti:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the certificate's URL ...
==> azure-arm:  -> Key Vault Name        : 'pkrkvbxpchwtl43'
==> azure-arm:  -> Key Vault Secret Name : 'packerKeyVaultSecret'
==> azure-arm:  -> Certificate URL       : 'https://pkrkvbxpchwtl43.vault.azure.net/secrets/packerKeyVaultSecret/6c12261b552c48ebadb7d4a88d99d011'
==> azure-arm: Setting the certificate's URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '40.121.160.214'
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /tmp/packer-powershell-provisioner759984171
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04"><Obj S="progress" RefId="0"><TN RefId="0"><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N="SourceId">1</
I64><PR N="Record"><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd?se=2017-07-13T22%3A25%3A02Z&sig=BYAbOXakavYV%2FbdWYfGqUIdsz2GXivbk0hxG0
5Mc09k%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json?se=2017-07-13T22%3A25%3A02Z&sig=wDMO3aSifbWLSISwoUOfkDMc5z7iKbGV
3us64gGvvlw%3D&sp=r&sr=b&sv=2015-02-21
```

Packer impiega alcuni minuti per compilare la macchina virtuale, eseguire gli strumenti di provisioning e pulire la distribuzione.


## <a name="create-azure-image"></a>Creare l'immagine di Azure
L'output dal processo di compilazione di Packer è un disco rigido virtuale nell'account di archiviazione specificato. Creare un'immagine di Azure da questo disco rigido virtuale con [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) e specificare il percorso `OSDiskUri` indicato alla fine dell'output di compilazione di Packer. L'esempio seguente crea un'immagine denominata `myImage`:

```powershell
$osVhdUri = "https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd"
$imageName = "myImage"

$imageConfig = New-AzureRmImageConfig -Location $location
$imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig `
    -OsType "Windows" `
    -OsState "Generalized" `
    -BlobUri $osVhdUri
$image = New-AzureRmImage -ImageName $imageName `
    -ResourceGroupName $rgName `
    -Image $imageConfig
```

Questa immagine può essere usata per creare macchine virtuali nella sottoscrizione di Azure. La creazione di macchine virtuali non è limitata allo stesso gruppo di risorse dell'immagine di origine.


## <a name="create-vm-from-azure-image"></a>Creare una macchina virtuale da un'immagine di Azure
Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential).

```powershell
$cred = Get-Credential
```

È ora possibile creare una macchina virtuale dall'immagine con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVM* da *myImage*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod "Static" `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
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
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

La creazione della macchina virtuale richiede alcuni minuti.


## <a name="test-vm-and-iis"></a>Testare la macchina virtuale e IIS
Ottenere l'indirizzo IP pubblico della macchina virtuale con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP per *myPublicIP* creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIP" | select "IpAddress"
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser.

![Sito IIS predefinito](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Passaggi successivi
In questo esempio Packer è stato usato per creare un'immagine di macchina virtuale con IIS già installato. È possibile usare questa immagine di macchina virtuale insieme a flussi di lavoro di distribuzione esistenti, ad esempio per distribuire app in macchine virtuali create dall'immagine con Team Services, Ansible, Chef o Puppet.

Per altri modelli di Packer di esempio per distribuzioni di Windows di altro tipo, vedere [questo repository di GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
