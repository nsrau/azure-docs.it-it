---
title: Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell
description: Viene descritto come creare e gestire macchine virtuali (VM) in un dispositivo GPU Pro Azure Stack Edge utilizzando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: aa492acdedc2d131d28c894031de2181e87a2f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890693"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

Questa esercitazione descrive come creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge Pro usando Azure PowerShell.

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione della VM

Il flusso di lavoro di distribuzione è illustrato nel diagramma seguente.

![Flusso di lavoro di distribuzione della VM](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>Eseguire una query per la sottoscrizione incorporata nel dispositivo

Per Azure Resource Manager, è supportata una sola sottoscrizione fissa visibile per l'utente. Questa sottoscrizione è univoca per ogni dispositivo e il nome della sottoscrizione o l'ID sottoscrizione non può essere modificato.

Questa sottoscrizione contiene tutte le risorse create per la creazione di macchine virtuali. 

> [!IMPORTANT]
> Questa sottoscrizione non è connessa o correlata alla sottoscrizione di Azure e si trova localmente nel dispositivo.

Questa sottoscrizione verrà usata per distribuire le macchine virtuali.

1.  Per elencare questa sottoscrizione, digitare:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Ottenere l'elenco dei provider di risorse registrati in esecuzione nel dispositivo. Questo elenco include in genere risorse di calcolo, rete e archiviazione.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > I provider di risorse sono pre-registrati e non possono essere modificati o modificati.
    
    Di seguito è riportato un output di esempio:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure, ad esempio account di archiviazione, disco, disco gestito.

> [!IMPORTANT]
> Tutte le risorse vengono create nella stessa posizione del dispositivo e la posizione è impostata su **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Di seguito è riportato un output di esempio.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un nuovo account di archiviazione usando il gruppo di risorse creato nel passaggio precedente. Si tratta di un **account di archiviazione locale** che verrà usato per caricare l'immagine del disco virtuale per la VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Solo gli account di archiviazione locali, ad esempio l'archiviazione con ridondanza locale (Standard_LRS o Premium_LRS), possono essere creati tramite Azure Resource Manager. Per creare account di archiviazione a più livelli, vedere la procedura in [aggiungere, connettersi agli account di archiviazione in Azure stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Di seguito è riportato un output di esempio.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Per ottenere la chiave dell'account di archiviazione, eseguire il `Get-AzureRmStorageAccountKey` comando. Di seguito è riportato un esempio di output di questo comando.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>Aggiungere l'URI del BLOB al file hosts

È già stato aggiunto l'URI del BLOB nel file hosts per il client usato per connettersi all'archiviazione BLOB nella sezione [modificare il file host per la risoluzione dei nomi di endpoint](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Questa è la voce per l'URI del BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>Installare i certificati

Se si usa *https*, è necessario installare i certificati appropriati nel dispositivo. In questo caso, installare il certificato dell'endpoint BLOB. Per ulteriori informazioni, vedere How to create and upload Certificates in [Manage Certificates](azure-stack-edge-j-series-manage-certificates.md).

## <a name="upload-a-vhd"></a>Caricare il VHD

Copiare le immagini del disco da usare nei BLOB di pagine nell'account di archiviazione locale creato nei passaggi precedenti. È possibile usare uno strumento come [AzCopy](../storage/common/storage-use-azcopy-v10.md) per caricare il disco rigido virtuale nell'account di archiviazione creato nei passaggi precedenti. 

Prima di usare AzCopy, verificare che [AzCopy sia configurato correttamente](#configure-azcopy) per l'uso con la versione dell'API REST di archiviazione BLOB in uso con il dispositivo Azure stack Edge Pro.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Impostare `BlobType` su pagina per la creazione di un disco gestito fuori dal disco rigido virtuale. Impostare `BlobType` su Block quando si scrive in account di archiviazione a livelli usando AzCopy.

È possibile scaricare le immagini disco dal Marketplace. Per i passaggi dettagliati, vedere [ottenere l'immagine del disco virtuale da Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Di seguito è riportato un esempio di output con AzCopy 7,3. Per altre informazioni su questo comando, vedere [caricare un file VHD nell'account di archiviazione usando AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>Creare dischi gestiti dal disco rigido virtuale

Creare un disco gestito dal disco rigido virtuale caricato.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Di seguito è riportato un output di esempio: 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Di seguito è riportato un output di esempio. Per ulteriori informazioni su questo cmdlet, vedere [New-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Creare un'immagine di VM dal disco gestito dell'immagine

Usare il comando seguente per creare un'immagine di macchina virtuale dal disco gestito. Sostituire i valori in \< \> con i nomi scelti.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Di seguito è riportato un output di esempio. Per ulteriori informazioni su questo cmdlet, vedere [New-AzureRmImage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Creare una VM con le risorse create in precedenza

È necessario creare una rete virtuale e associare un'interfaccia di rete virtuale prima di creare e distribuire la VM.

> [!IMPORTANT]
> Quando si crea una rete virtuale e un'interfaccia di rete virtuale, si applicano le regole seguenti:
> - È possibile creare solo un VNET (anche tra gruppi di risorse), che deve corrispondere esattamente alla rete logica in termini di spazio degli indirizzi.
> -   In VNET sarà consentita una sola subnet. La subnet deve corrispondere esattamente allo stesso spazio di indirizzi del vnet.
> -   Durante la creazione del vNIC sarà consentito solo il metodo di allocazione statica e l'utente dovrà specificare un indirizzo IP privato.

 
**Creare un VNET**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Creare una vNIC usando l'ID subnet VNET**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Di seguito è riportato l'output di esempio di questi comandi:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Facoltativamente, durante la creazione di un vNIC per una macchina virtuale, è possibile passare l'indirizzo IP pubblico. In questo caso, l'indirizzo IP pubblico restituirà l'indirizzo IP privato. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Creare una macchina virtuale**

È ora possibile usare l'immagine di macchina virtuale per creare una macchina virtuale e collegarla alla rete virtuale creata in precedenza.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Connettersi a una macchina virtuale

A seconda del fatto che sia stata creata una macchina virtuale Windows o Linux, la procedura per la connessione può essere diversa.

### <a name="connect-to-linux-vm"></a>Connettersi a una VM Linux

Seguire questa procedura per connettersi a una VM Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Connettersi a una macchina virtuale Windows

Per connettersi a una macchina virtuale Windows, seguire questa procedura.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>Gestisci macchina virtuale

La sezione seguente descrive alcune delle operazioni comuni relative alla macchina virtuale che verrà creata nel dispositivo Azure Stack Edge Pro.

### <a name="list-vms-running-on-the-device"></a>Elencare le macchine virtuali in esecuzione nel dispositivo

Per restituire un elenco di tutte le macchine virtuali in esecuzione nel dispositivo Azure Stack Edge Pro, eseguire il comando seguente.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Accendere la macchina virtuale

Eseguire il cmdlet seguente per attivare una macchina virtuale in esecuzione nel dispositivo:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


Per ulteriori informazioni su questo cmdlet, vedere [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0).

### <a name="suspend-or-shut-down-the-vm"></a>Sospendere o arrestare la VM

Eseguire il cmdlet seguente per arrestare o arrestare una macchina virtuale in esecuzione nel dispositivo:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


Per ulteriori informazioni su questo cmdlet, vedere il [cmdlet Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0).

### <a name="add-a-data-disk"></a>Aggiungere un disco dati

Se i requisiti del carico di lavoro nella macchina virtuale aumentano, potrebbe essere necessario aggiungere un disco dati.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Eseguire il cmdlet seguente per rimuovere una macchina virtuale dal dispositivo:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Per ulteriori informazioni su questo cmdlet, vedere il [cmdlet Remove-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0).


## <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

La dimensione di VM determina la quantità di risorse di calcolo, come CPU, GPU e memoria, che viene resa disponibile per la VM. Le macchine virtuali dovrebbero essere create usando una dimensione di VM adeguata per il carico di lavoro. Anche se tutti i computer verranno eseguiti sullo stesso hardware, le dimensioni dei computer hanno limiti diversi per l'accesso al disco, che consentono di gestire l'accesso globale al disco tra le macchine virtuali. Se un carico di lavoro aumenta, è possibile ridimensionare anche una macchina virtuale esistente.

Le macchine virtuali serie dv2 standard seguenti sono supportate per la creazione nel dispositivo Azure Stack Edge Pro.

### <a name="dv2-series"></a>Serie Dv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4/4 x 500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8/8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16/16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32/32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>Serie DSv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Serie Dv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8/8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16/16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32/32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>Serie DSv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Per altre informazioni, vedere la [serie dv2 in per utilizzo generico dimensioni delle macchine virtuali](../virtual-machines/dv2-dsv2-series.md#dv2-series).

## <a name="unsupported-vm-operations-and-cmdlets"></a>Operazioni e cmdlet non supportati per le macchine virtuali

L'estensione, i set di scalabilità, i set di disponibilità e gli snapshot non sono supportati.

## <a name="configure-azcopy"></a>Configurare AzCopy

Quando si installa la versione più recente di AzCopy, sarà necessario configurare AzCopy per assicurarsi che corrisponda alla versione dell'API REST di archiviazione BLOB del dispositivo Azure Stack Edge Pro.

Nel client usato per accedere al dispositivo Azure Stack Edge Pro, impostare una variabile globale in modo che corrisponda alla versione dell'API REST di archiviazione BLOB.

### <a name="on-windows-client"></a>Nel client Windows 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>Sul client Linux

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

Per verificare se la variabile di ambiente per AzCopy è stata impostata correttamente, seguire questa procedura:

1. Eseguire "azcopy ENV"
2. Trovare il `AZCOPY_DEFAULT_SERVICE_API_VERSION` parametro. Il valore deve essere impostato nei passaggi precedenti.


## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
