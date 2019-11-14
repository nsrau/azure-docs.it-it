---
title: Assegnare ruoli personalizzati usando Azure PowerShell-Azure AD | Microsoft Docs
description: Gestire i membri di un ruolo personalizzato amministratore di Azure AD con Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025326"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Assegnare ruoli personalizzati con ambito di risorse usando PowerShell in Azure Active Directory

Questo articolo descrive come creare un'assegnazione di ruolo a livello di organizzazione in Azure Active Directory (Azure AD). L'assegnazione di un ruolo a livello di organizzazione concede l'accesso all'intera organizzazione Azure AD. Per creare un'assegnazione di ruolo con un ambito di una singola risorsa di Azure AD, vedere [come creare un ruolo personalizzato e assegnarlo all'ambito della risorsa](roles-create-custom.md). Questo articolo usa il modulo [Azure Active Directory PowerShell versione 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Per ulteriori informazioni sui ruoli di amministratore di Azure AD, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Connettersi al tenant di Azure AD usando un account di amministratore globale per assegnare o rimuovere i ruoli.

## <a name="prepare-powershell"></a>Preparare PowerShell

Installare il modulo Azure AD PowerShell dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importare quindi il modulo di anteprima di Azure AD PowerShell usando il comando seguente:

``` PowerShell
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, associare la versione restituita dal comando seguente a quella elencata qui:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa dei cmdlet nel modulo Azure AD, vedere la documentazione di riferimento online per il [modulo Azure ad Preview](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Assegnare un ruolo a un utente o a un'entità servizio con ambito di risorsa

1. Aprire il modulo di PowerShell Azure AD Preview.
1. Eseguire l'accesso eseguendo il comando `Connect-AzureAD`.
1. Creare un nuovo ruolo usando lo script di PowerShell seguente.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Per assegnare il ruolo a un'entità servizio anziché a un utente, usare il [cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operazioni su RoleDefinition

Gli oggetti di definizione dei ruoli contengono la definizione del ruolo predefinito o personalizzato, insieme alle autorizzazioni concesse da tale assegnazione di ruolo. Questa risorsa consente di visualizzare le definizioni di ruolo personalizzate e directoryRoles predefinite (visualizzate in formato equivalente roleDefinition). Attualmente, un'organizzazione Azure AD può avere un massimo di 30 RoleDefinitions personalizzati definiti.

### <a name="create-operations-on-roledefinition"></a>Creare operazioni su RoleDefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Operazioni di lettura su RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Operazioni di aggiornamento in RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Operazioni DELETE in RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operazioni su RoleAssignment

Le assegnazioni di ruolo contengono informazioni che collegano una determinata entità di sicurezza (un'entità servizio dell'utente o dell'applicazione) a una definizione di ruolo. Se necessario, è possibile aggiungere un ambito di una singola risorsa Azure AD per le autorizzazioni assegnate.  La limitazione dell'ambito delle autorizzazioni è supportata per i ruoli predefiniti e personalizzati.

### <a name="create-operations-on-roleassignment"></a>Creazione di operazioni su RoleAssignment

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Operazioni di lettura su RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Operazioni DELETE su RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Passaggi successivi

- Condividi con noi nel [Forum sui ruoli amministrativi Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e sulle assegnazioni di ruolo di amministratore di Azure AD, vedere [assegnare ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
