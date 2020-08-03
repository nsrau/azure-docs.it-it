---
title: Creare una VM Windows con Azure Image Builder usando PowerShell
description: Creare una VM Windows con il modulo PowerShell di Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e25b2b53acdfb05af8572a01109961bf3002e429
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499433"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Anteprima: creare una VM Windows con Azure Image Builder usando PowerShell

Questo articolo illustra come creare un'immagine di Windows personalizzata usando il modulo PowerShell di generatore di immagini di VM di Azure.

> [!CAUTION]
> Azure Image Builder è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio. Non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mentre i moduli di PowerShell **AZ. ImageBuilder** e **AZ. ManagedServiceIdentity** sono in anteprima, è necessario installarli separatamente usando il `Install-Module` cmdlet con il `AllowPrerelease` parametro. Una volta che questi moduli di PowerShell diventano disponibili a livello generale, diventano parte delle future versioni del modulo AZ PowerShell e sono disponibili in modo nativo dall'interno Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Registrare le funzionalità

Se è la prima volta che si usa Azure Image Builder durante l'anteprima, registrare la nuova funzionalità **VirtualMachineTemplatePreview** .

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Verificare lo stato della registrazione della funzionalità.

> [!NOTE]
> Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per alcuni minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registrare i provider di risorse seguenti per l'uso con la sottoscrizione di Azure, se non sono già registrati.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definire le variabili

Verranno usate più volte le informazioni. Creare variabili per archiviare le informazioni.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Creare una variabile per l'ID sottoscrizione di Azure. Per verificare che la `subscriptionID` variabile contenga l'ID sottoscrizione, è possibile eseguire la seconda riga nell'esempio seguente.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

Nell'esempio seguente viene creato un gruppo di risorse basato sul nome della `$imageResourceGroup` variabile nell'area specificata nella `$location` variabile. Questo gruppo di risorse viene usato per archiviare l'artefatto del modello di configurazione dell'immagine e l'immagine.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Crea identità utente e imposta autorizzazioni ruolo

Concedere le autorizzazioni di Azure Image Builder per creare immagini nel gruppo di risorse specificato usando l'esempio seguente. Senza questa autorizzazione, il processo di compilazione dell'immagine non verrà completato correttamente.

Creare variabili per la definizione del ruolo e i nomi di identità. Questi valori devono essere univoci.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Creare un'identità utente.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Archiviare la risorsa Identity e gli ID entità nelle variabili.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Assegnare le autorizzazioni per l'identità per la distribuzione di immagini

Scaricare il file di configurazione con estensione JSON e modificarlo in base alle impostazioni definite in questo articolo.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Creare la definizione di ruolo.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Concedere la definizione del ruolo all'entità servizio del generatore di immagini.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Se viene visualizzato l'errore: "_New-AzRoleDefinition: limite della definizione del ruolo superato. Non è possibile creare più definizioni di ruolo._", vedere [risolvere i problemi relativi a RBAC di Azure](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Creare una Raccolta immagini condivise

Creare la raccolta.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Creare una definizione della raccolta.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Creare un'immagine

Creare un oggetto di origine del generatore di immagini di Azure. Vedere [trovare immagini di VM Windows in Azure Marketplace con Azure PowerShell](./cli-ps-findimage.md) per i valori dei parametri validi.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Creare un oggetto server di distribuzione di Azure Image Builder.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Creare un oggetto di personalizzazione di Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

Creare un modello di generatore di immagini di Azure.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Al termine, viene restituito un messaggio e viene creato un modello di configurazione del generatore di immagini in `$imageResourceGroup` .

Per determinare se il processo di creazione del modello ha avuto esito positivo, è possibile usare l'esempio seguente.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

In background, Image Builder crea anche un gruppo di risorse di staging nella sottoscrizione. Questo gruppo di risorse viene usato per la compilazione dell'immagine. Il formato è: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Non eliminare direttamente il gruppo di risorse di staging. Eliminare l'elemento del modello di immagine, in modo che il gruppo di risorse di staging venga eliminato.

Se il servizio segnala un errore durante l'invio del modello di configurazione immagine:

- Vedere [risoluzione dei problemi relativi a errori di compilazione di immagini VM di Azure (AIB)](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting).
- Eliminare il modello usando l'esempio seguente prima di riprovare.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Avviare la compilazione dell'immagine

Inviare la configurazione dell'immagine al servizio Generatore di immagini VM.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Attendere il completamento del processo di compilazione dell'immagine. Questo passaggio potrebbe richiedere fino a un'ora.

Se si verificano errori, vedere [risoluzione dei problemi relativi a errori di compilazione immagine di VM di Azure (AIB)](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting).

## <a name="create-a-vm"></a>Creare una macchina virtuale

Archiviare le credenziali di accesso per la macchina virtuale in una variabile. La password deve essere complessa.

```azurepowershell-interactive
$Cred = Get-Credential
```

Creare la macchina virtuale usando l'immagine creata.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione

Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della VM. All'interno della macchina virtuale, aprire PowerShell ed eseguire `Get-Content` come illustrato nell'esempio seguente:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Verrà visualizzato l'output in base al contenuto del file creato durante il processo di personalizzazione delle immagini.

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono necessarie, è possibile eliminarle eseguendo gli esempi seguenti.

### <a name="delete-the-image-builder-template"></a>Eliminare il modello di generatore di immagini

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Eliminare il gruppo di risorse immagine

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se le risorse al di fuori dell'ambito di questo articolo sono presenti nel gruppo di risorse specificato, verranno eliminate anche.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file con estensione JSON usato in questo articolo, vedere informazioni di [riferimento sui modelli di generatore di immagini](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
