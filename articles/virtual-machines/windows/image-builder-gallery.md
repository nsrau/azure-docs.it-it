---
title: Usare Azure Image Builder con una raccolta immagini per macchine virtuali Windows (anteprima)
description: Creare versioni di immagini della raccolta condivisa di Azure usando Azure Image Builder e Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 0db618d63964c6f271f9a14b91849e704cfd7820
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077511"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine Windows e distribuirla in una raccolta immagini condivise 

Questo articolo illustra come usare Azure Image Builder e Azure PowerShell per creare una versione dell'immagine di una [raccolta immagini condivise](shared-image-galleries.md) e quindi distribuirla a livello globale. Questa operazione può essere eseguita anche con l'[interfaccia della riga di comando di Azure](../linux/image-builder-gallery.md).

Per configurare l'immagine, si userà un modello con estensione json. Il file con estensione json usato è [helloImageTemplateforSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Si eseguiranno il download e la modifica di una versione locale del modello, per cui questo articolo è stato scritto usando una sessione locale di PowerShell.

Per distribuire l'immagine in una raccolta immagini condivise, il modello usa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) come valore per la sezione `distribute`.

Azure Image Builder esegue automaticamente Sysprep per generalizzare l'immagine. Si tratta di un comando Sysprep generico, di cui è possibile eseguire l'[override](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) se necessario. 

Prestare attenzione al numero di volte in cui si eseguono personalizzazioni. È possibile eseguire il comando Sysprep fino a 8 volte in una singola immagine Windows. Dopo aver eseguito Sysprep 8 volte, è necessario ricreare l'immagine Windows. Per altre informazioni, vedere [Limiti del numero di volte in cui è possibile eseguire Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrare le funzionalità
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Verificare lo stato della registrazione della funzionalità.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Attendere che `RegistrationState` diventi `Registered` prima di continuare con il passaggio successivo.

Controllare le registrazioni del provider. Verificare che ognuna restituisca `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se non restituiscono `Registered`, usare il comando seguente per registrare i provider:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Creare le variabili

Alcune informazioni verranno usate ripetutamente, pertanto verranno create alcune variabili in cui archiviarle. Sostituire i valori per le variabili, ad esempio `username` e `vmpassword`, con le proprie informazioni.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Creare un'identità assegnata dall'utente e impostare autorizzazioni per il gruppo di risorse
Image Builder userà l'[identità utente](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) specificata per inserire l'immagine nella raccolta immagini condivise di Azure. In questo esempio si creerà una definizione di ruolo di Azure che include azioni granulari per eseguire la distribuzione dell'immagine nella raccolta immagini condivise. La definizione del ruolo verrà quindi assegnata all'identità utente.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Assegnare le autorizzazioni per l'identità per la distribuzione di immagini

Questo comando scarica un modello di definizione di ruolo di Azure e lo aggiorna con i parametri specificati in precedenza.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Creare la Raccolta immagini condivise

Per usare Image Builder con una raccolta immagini condivise, è necessario avere una raccolta immagini esistente e una definizione di immagine. Image Builder non crea autonomamente la raccolta immagini e la definizione di immagine.

Se non si ha già una raccolta e una definizione di immagine da usare, iniziare creando questi elementi. In primo luogo creare un raccolta di immagini.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Scaricare e configurare il modello

Scaricare il modello con estensione json e configurarlo con le variabili.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Creare la versione di immagine

Il modello deve essere inviato al servizio, in modo da scaricare eventuali artefatti dipendenti, ad esempio gli script, e archiviarli nel gruppo di risorse di staging, con prefisso *IT_* .

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Per compilare l'immagine, è necessario richiamare 'Run' sul modello.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

La creazione dell'immagine e la sua replica in entrambe le aree possono richiedere del tempo. Attendere il completamento di questa parte prima di procedere alla creazione di una macchina virtuale.

Per informazioni sulle opzioni disponibili per automatizzare il recupero dello stato di compilazione dell'immagine, vedere il file [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) per questo modello in GitHub.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione dell'immagine creata da Azure Image Builder.

Ottenere la versione dell'immagine creata.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Creare la macchina virtuale nella seconda area in cui è stata replicata l'immagine.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione
Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della VM. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:

```console
dir c:\
```

Verrà visualizzata una directory denominata `buildActions` creata durante la personalizzazione dell'immagine.


## <a name="clean-up-resources"></a>Pulire le risorse
Se si vuole provare a ripetere la personalizzazione per creare una nuova versione della stessa immagine, **ignorare questo passaggio** e passare [Usare Azure Image Builder per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).


Questa operazione eliminerà l'immagine creata e tutti gli altri file di risorse. Assicurarsi che la distribuzione sia stata completata prima di eliminare le risorse.

Eliminare prima il modello di gruppo di risorse; in caso contrario, il gruppo di risorse di staging (*IT_* ) usato da Azure Image Builder non verrà pulito.

Ottenere il valore ResourceID del modello di immagine. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Eliminare il modello di immagine.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Elimina assegnazione ruolo

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Rimuovere le definizioni

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Eliminare l'identità

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

Eliminare il gruppo di risorse.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare la versione dell'immagine creata, vedere [Usare Azure Image Builder per creare un'altra versione dell'immagine](image-builder-gallery-update-image-version.md).
