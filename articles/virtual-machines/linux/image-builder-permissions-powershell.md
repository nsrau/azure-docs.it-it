---
title: Configurare le autorizzazioni del servizio Generatore di immagini di Azure con PowerShell
description: Configurare i requisiti per il servizio Generatore di immagini di VM di Azure, inclusi i privilegi e le autorizzazioni con PowerShell
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: cfe3efc77e065ac3685b72d0eab501034609b59b
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068263"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Configurare le autorizzazioni del servizio Generatore di immagini di Azure con PowerShell

Il servizio Generatore di immagini di Azure richiede la configurazione di autorizzazioni e privilegi prima di creare un'immagine. Le sezioni seguenti illustrano in dettaglio come configurare possibili scenari usando PowerShell.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrare le funzionalità

Per prima cosa, è necessario registrarsi per il servizio Generatore di immagini di Azure. La registrazione concede al servizio l'autorizzazione per creare, gestire ed eliminare un gruppo di risorse di staging. Il servizio dispone inoltre dei diritti per aggiungere risorse al gruppo necessario per la compilazione dell'immagine.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente di Azure

Azure Image Builder richiede la creazione di un' [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure Image Builder usa l'identità gestita assegnata dall'utente per leggere immagini, scrivere immagini e accedere agli account di archiviazione di Azure. Si concede l'autorizzazione di identità per eseguire azioni specifiche nella sottoscrizione.

> [!NOTE]
> In precedenza, generatore di immagini di Azure usava il nome dell'entità servizio (SPN) di Azure Image Builder per concedere le autorizzazioni ai gruppi di risorse immagine. L'utilizzo del nome SPN verrà deprecato. Usare invece un'identità gestita assegnata dall'utente.

L'esempio seguente illustra come creare un'identità gestita assegnata dall'utente di Azure. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Resource group\> | Gruppo di risorse in cui creare l'identità gestita assegnata dall'utente. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Per altre informazioni sulle identità assegnate dall'utente di Azure, vedere la documentazione di [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) per informazioni su come creare un'identità.

## <a name="allow-image-builder-to-distribute-images"></a>Consenti a Generatore di immagini di distribuire immagini

Per consentire a Generatore immagini di Azure di distribuire immagini (immagini gestite/raccolta immagini condivise), il servizio Generatore di immagini di Azure deve essere autorizzato a inserire le immagini in questi gruppi di risorse. Per concedere le autorizzazioni necessarie, è necessario creare un'identità gestita assegnata dall'utente e concedere i diritti per il gruppo di risorse in cui viene compilata l'immagine. Il generatore di immagini di **Azure non è** autorizzato ad accedere alle risorse di altri gruppi di risorse nella sottoscrizione. È necessario eseguire azioni esplicite per consentire l'accesso per evitare che le compilazioni abbiano esito negativo.

Non è necessario concedere i diritti di collaboratore identità gestita assegnato dall'utente nel gruppo di risorse per distribuire le immagini. Tuttavia, l'identità gestita assegnata dall'utente deve disporre delle seguenti `Actions` autorizzazioni di Azure nel gruppo di risorse di distribuzione:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Se si esegue la distribuzione in una raccolta di immagini condivise, è necessario anche:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Autorizzazione per personalizzare le immagini esistenti

Per consentire a Generatore immagini di Azure di creare immagini da immagini personalizzate di origine (immagini gestite/raccolta immagini condivise), è necessario che il servizio Azure Image Builder possa leggere le immagini in questi gruppi di risorse. Per concedere le autorizzazioni necessarie, è necessario creare un'identità gestita assegnata dall'utente e concedere i diritti per il gruppo di risorse in cui si trova l'immagine.

Compilare da un'immagine personalizzata esistente:

```Actions
Microsoft.Compute/galleries/read
```

Compilare da una versione della raccolta immagini condivisa esistente:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Autorizzazione per personalizzare le immagini nel reti virtuali

Il generatore di immagini di Azure è in grado di distribuire e usare un VNET esistente nella sottoscrizione, consentendo così alle personalizzazioni di accedere alle risorse connesse.

Non è necessario concedere i diritti di collaboratore identità gestite assegnati dall'utente nel gruppo di risorse per distribuire una macchina virtuale in una VNET esistente. Tuttavia, l'identità gestita assegnata dall'utente deve disporre delle seguenti `Actions` autorizzazioni di Azure per il gruppo di risorse VNET:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Creare una definizione di ruolo di Azure

Gli esempi seguenti creano una definizione di ruolo di Azure dalle azioni descritte nelle sezioni precedenti. Gli esempi vengono applicati a livello di gruppo di risorse. Valutare e verificare se gli esempi sono sufficientemente granulari per le proprie esigenze. Per lo scenario in uso, potrebbe essere necessario affinarlo in una raccolta di immagini condivise specifica.

Le azioni dell'immagine consentono le operazioni di lettura e scrittura. Decidere cosa è appropriato per l'ambiente in uso. Ad esempio, creare un ruolo per consentire a Generatore di immagini di Azure di leggere immagini dal gruppo di risorse *esempio-RG-1* e scrivere immagini nell'esempio di gruppo di risorse *-RG-2*.

### <a name="custom-image-azure-role-example"></a>Esempio di ruolo di Azure per immagini personalizzate

Nell'esempio seguente viene creato un ruolo di Azure per l'uso e la distribuzione di un'immagine personalizzata di origine. Si concede quindi il ruolo personalizzato all'identità gestita assegnata dall'utente per il generatore di immagini di Azure.

Per semplificare la sostituzione dei valori nell'esempio, impostare prima le variabili seguenti. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Subscription ID\> | L'ID sottoscrizione di Azure |
| \<Resource group\> | Gruppo di risorse per l'immagine personalizzata |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Esempio di ruolo di Azure VNET esistente

Nell'esempio seguente viene creato un ruolo di Azure per l'uso e la distribuzione di un'immagine VNET esistente. Si concede quindi il ruolo personalizzato all'identità gestita assegnata dall'utente per il generatore di immagini di Azure.

Per semplificare la sostituzione dei valori nell'esempio, impostare prima le variabili seguenti. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Subscription ID\> | L'ID sottoscrizione di Azure |
| \<Resource group\> | Gruppo di risorse VNET |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di Azure Image Builder](image-builder-overview.md).