---
title: 'Azure Active Directory Domain Services: sincronizzazione con ambito | Microsoft Docs'
description: Configurare la sincronizzazione con ambito da Azure AD nei domini gestiti
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: a6f56a9c855e296fb49b1c24d09189177175c9d6
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041867"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Configurare la sincronizzazione con ambito da Azure AD nel dominio gestito
Questo articolo illustra come configurare solo account utente specifici per sincronizzarli dalla directory di Azure AD nel dominio gestito di Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Sincronizzazione con ambito basata sui gruppi
Per impostazione predefinita, tutti gli utenti e i gruppi all'interno della directory di Azure AD sono sincronizzati nel dominio gestito. Se il dominio gestito viene usato solo da alcuni utenti, è possibile sincronizzare solo quegli account utente specifici. Con la sincronizzazione con ambito basata sui gruppi è possibile farlo. Una volta configurati, solo gli account utente appartenenti ai gruppi specificati vengono sincronizzati nel dominio gestito.

La tabella riportata di seguito consente di determinare come usare la sincronizzazione con ambito:

| **Stato corrente** | **Stato desiderato** | **Configurazione necessaria** |
| --- | --- | --- |
| Il dominio gestito esistente è configurato per sincronizzare tutti i gruppi e account utente. | Si desidera sincronizzare nel dominio gestito solo gli account utente appartenenti a gruppi specifici. | [Eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md). Quindi seguire le istruzioni riportate in questo articolo per crearlo nuovamente con la sincronizzazione con ambito configurata. |
| Non si dispone di un dominio gestito esistente. | Si desidera creare un nuovo dominio gestito e sincronizzare solo gli account utente appartenenti a gruppi specifici. | Seguire le istruzioni riportate in questo articolo per creare un nuovo dominio gestito con la sincronizzazione con ambito configurata. |
| Il dominio gestito esistente è configurato per sincronizzare solo gli account appartenenti a gruppi specifici. | Si desidera modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati nel dominio gestito. | Seguire le istruzioni riportate in questo articolo per modificare la sincronizzazione con ambito. |

> [!WARNING]
> **La modifica dell'ambito di sincronizzazione causa la risincronizzazione del dominio gestito.**
>
 * Quando si modifica l'ambito di sincronizzazione per un dominio gestito, si verifica una risincronizzazione completa.
 * Gli oggetti non più necessari nel dominio gestito vengono eliminati. Vengono creati nuovi oggetti nel dominio gestito.
 * La risincronizzazione può richiedere molto tempo per essere completata, a seconda del numero di oggetti (utenti, gruppi e appartenenze a gruppi) nel dominio gestito e nella directory di Azure AD. Per le directory di grandi dimensioni con molte centinaia di migliaia di oggetti, la risincronizzazione potrebbe richiedere alcuni giorni.
>
>


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization"></a>Creare un nuovo dominio gestito e abilitare la sincronizzazione con ambito basata sui gruppi
Usare PowerShell per completare questa serie di passaggi. Consultare le istruzioni in [Abilitare Azure Active Directory Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md). In questo articolo un paio di passaggi sono stati leggermente modificati per configurare la sincronizzazione con ambito.

Completare i passaggi seguenti per configurare la sincronizzazione con ambito basata sui gruppi nel dominio gestito:

1. Completare le attività seguenti:
  * [Attività 1: Installare i moduli di PowerShell necessari](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
  * [Attività 2: Creare l'entità servizio richiesta nella directory di Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
  * [Attività 3: Creare e configurare il gruppo "AAD DC Administrators"](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
  * [Attività 4: Registrare il provider di risorse di Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
  * [Attività 5: Creare un gruppo di risorse](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
  * [Attività 6: Creare e configurare la rete virtuale](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Selezionare i gruppi che si desidera sincronizzare e indicare il nome visualizzato dei gruppi che si desidera sincronizzare nel proprio dominio gestito.

3. Salvare lo [script nella sezione seguente](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) in un file denominato ```Select-GroupsToSync.ps1```. Eseguire lo script come indicato sotto:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", “GroupName1”, “GroupName2”)
  ```

  > [!WARNING]
  > **Non dimenticare di includere il gruppo "AAD DC Administrators".**
  >
  > È necessario includere il gruppo "AAD DC Administrators" nell'elenco dei gruppi configurati per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito sarà inutilizzabile.
  >

4. Creare un nuovo dominio gestito e abilitare la sincronizzazione con ambito basata sui gruppi per il dominio gestito. Includere la proprietà ```"filteredSync" = "Enabled"``` nel parametro ```Properties```. Vedere ad esempio il frammento di script seguente, copiato dall'[Attività 7: Eseguire il provisioning del dominio gestito di Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

  ```powershell
  $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
  $ManagedDomainName = "contoso100.com"
  $ResourceGroupName = "ContosoAaddsRg"
  $VnetName = "DomainServicesVNet_WUS"
  $AzureLocation = "westus"

  # Enable Azure AD Domain Services for the directory.
  New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
  ```

  > [!TIP]
  > Non dimenticare di includere ```"filteredSync" = "Enabled"``` nel parametro ```-Properties```, in modo che la sincronizzazione con ambito sia abilitata per il dominio gestito.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script per selezionare i gruppi da sincronizzare nel dominio gestito (Select-GroupsToSync.ps1)
Salvare lo script seguente in un file (```Select-GroupsToSync.ps1```). Questo script configura Azure AD Domain Services per sincronizzare i gruppi selezionati nel dominio gestito. Tutti gli account utente appartenenti ai gruppi specificati verranno sincronizzati nel dominio gestito.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occured assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Modificare la sincronizzazione con ambito basata sui gruppi
Per modificare l'elenco di gruppi i cui utenti devono essere sincronizzati nel dominio gestito, eseguire nuovamente lo [script di PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) e specificare il nuovo elenco di gruppi. Ricordarsi di specificare sempre il gruppo "AAD DC Administrators" in questo elenco.

> [!WARNING]
> **Non dimenticare di includere il gruppo "AAD DC Administrators".**
>
> È necessario includere il gruppo "AAD DC Administrators" nell'elenco dei gruppi configurati per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito sarà inutilizzabile.
>


## <a name="disable-group-based-scoped-synchronization"></a>Disabilitare la sincronizzazione con ambito basata sui gruppi
Usare lo script di PowerShell seguente per disabilitare la sincronizzazione con ambito basata sui gruppi per il dominio gestito:

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sulla sincronizzazione in Azure AD Domain Services](active-directory-ds-synchronization.md)
* [Abilitare Azure Active Directory Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md)
