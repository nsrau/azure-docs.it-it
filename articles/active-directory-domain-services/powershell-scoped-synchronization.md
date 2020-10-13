---
title: Sincronizzazione con ambito usando PowerShell per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come usare Azure AD PowerShell per configurare la sincronizzazione con ambito da Azure AD a un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: joflore
ms.openlocfilehash: dc6ebb88440ecdccc3ea78d85f0b6186100da01a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967884"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Configurare la sincronizzazione con ambito da Azure AD a Azure Active Directory Domain Services tramite Azure AD PowerShell

Per fornire servizi di autenticazione, Azure Active Directory Domain Services (Azure AD DS) sincronizza gli utenti e i gruppi da Azure AD. In un ambiente ibrido, è possibile sincronizzare gli utenti e i gruppi di un ambiente di Active Directory Domain Services locale (AD DS) con Azure AD utilizzando Azure AD Connect, quindi sincronizzati con Azure AD DS.

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD vengono sincronizzati con un dominio gestito Azure AD DS. Se si hanno esigenze specifiche, è invece possibile scegliere di sincronizzare solo un set di utenti definito.

Questo articolo illustra come creare un dominio gestito che usa la sincronizzazione con ambito e quindi modificare o disabilitare il set di utenti con ambito usando Azure AD PowerShell. È anche possibile [completare questi passaggi usando il portale di Azure][scoped-sync].

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un Azure Active Directory Domain Services dominio gestito][tutorial-create-instance].
* Per modificare l'ambito di sincronizzazione di Azure AD DS, è necessario disporre dei privilegi di *amministratore globale* nel tenant di Azure ad.

## <a name="scoped-synchronization-overview"></a>Panoramica della sincronizzazione con ambito

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD sono sincronizzati con un dominio gestito. Se solo alcuni utenti devono accedere al dominio gestito, è possibile sincronizzare solo gli account utente. Questa sincronizzazione con ambito è basata sul gruppo. Quando si configura la sincronizzazione con ambito basata su gruppo, solo gli account utente che appartengono ai gruppi specificati vengono sincronizzati con il dominio gestito. I gruppi annidati non sono sincronizzati, ma solo i gruppi specifici selezionati.

È possibile modificare l'ambito di sincronizzazione quando si crea il dominio gestito o una volta distribuito. È anche possibile modificare l'ambito di sincronizzazione in un dominio gestito esistente senza che sia necessario ricrearlo.

Per ulteriori informazioni sul processo di sincronizzazione, vedere informazioni sulla [sincronizzazione in Azure ad Domain Services][concepts-sync].

> [!WARNING]
> La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Si applicano le considerazioni seguenti:
>
>  * Quando si modifica l'ambito di sincronizzazione per un dominio gestito, si verifica una risincronizzazione completa.
>  * Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati. Vengono creati nuovi oggetti nel dominio gestito.

## <a name="powershell-script-for-scoped-synchronization"></a>Script di PowerShell per la sincronizzazione con ambito

Per configurare la sincronizzazione con ambito usando PowerShell, salvare prima di tutto lo script seguente in un file denominato `Select-GroupsToSync.ps1` .

Questo script configura Azure AD DS per sincronizzare i gruppi selezionati da Azure AD. Tutti gli account utente che fanno parte dei gruppi specificati vengono sincronizzati con il dominio gestito.

Questo script viene usato nei passaggi aggiuntivi di questo articolo.

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
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Abilita sincronizzazione con ambito

Per abilitare la sincronizzazione con ambito gruppo per un dominio gestito, completare i passaggi seguenti:

1. Per prima cosa impostare *"filteredSync" = "Enabled"* sulla risorsa Azure AD DS, quindi aggiornare il dominio gestito.

    Quando richiesto, specificare le credenziali di un *amministratore globale* per accedere al tenant di Azure ad usando il cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Specificare ora l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito.

    Eseguire lo `Select-GroupsToSync.ps1` script e specificare l'elenco di gruppi da sincronizzare. Nell'esempio seguente i gruppi da sincronizzare sono *GroupName1* e *GroupName2*.

    > [!WARNING]
    > È necessario includere il gruppo di *amministratori di AAD DC* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito è inutilizzabile.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="modify-scoped-synchronization"></a>Modificare la sincronizzazione con ambito

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito, eseguire `Select-GroupsToSync.ps1` lo script e specificare il nuovo elenco di gruppi da sincronizzare.

Nell'esempio seguente i gruppi da sincronizzare non includono più *GroupName2*e ora includono *GroupName3*.

> [!WARNING]
> È necessario includere il gruppo di *amministratori di AAD DC* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito è inutilizzabile.

Quando richiesto, specificare le credenziali di un *amministratore globale* per accedere al tenant di Azure ad usando il cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="disable-scoped-synchronization"></a>Disabilitare la sincronizzazione con ambito

Per disabilitare la sincronizzazione con ambito gruppo per un dominio gestito, impostare *"filteredSync" = "disabled"* sulla risorsa Azure AD DS, quindi aggiornare il dominio gestito. Al termine, tutti gli utenti e i gruppi vengono impostati per la sincronizzazione da Azure AD.

Quando richiesto, specificare le credenziali di un *amministratore globale* per accedere al tenant di Azure ad usando il cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sincronizzazione, vedere informazioni sulla [sincronizzazione in Azure ad Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
