---
title: Assegnare e rimuovere l'assegnazione di ruoli di amministratore con Azure PowerShell-Azure Active Directory | Microsoft Docs
description: Per coloro che gestiscono spesso le assegnazioni di ruolo, è ora possibile gestire i membri di un ruolo amministratore Azure AD con Azure PowerShell.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4bddf84720265afe361dff665f10ff8184f6f6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706491"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>Assegnare i ruoli di amministratore di Azure Active Directory tramite PowerShell

È possibile automatizzare il modo in cui si assegnano i ruoli agli account utente usando Azure PowerShell. Questo articolo usa il modulo [Azure Active Directory PowerShell versione 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

## <a name="prepare-powershell"></a>Preparare PowerShell

In primo luogo è necessario [scaricare il modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

```powershell
install-module azuread
import-module azuread
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa dei cmdlet nel modulo Azure AD, consultare la documentazione di riferimento online per [Azure Active Directory PowerShell versione 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

## <a name="permissions-required"></a>Autorizzazioni necessarie

Connettersi al tenant di Azure AD usando un account di amministratore globale per assegnare o rimuovere i ruoli.

## <a name="assign-a-single-role"></a>Assegnare un singolo ruolo

Per assegnare un ruolo, è innanzitutto necessario ottenere il nome visualizzato e il nome del ruolo che si sta assegnando. Quando si dispone del nome visualizzato dell'account e del nome del ruolo, utilizzare i cmdlet seguenti per assegnare il ruolo all'utente.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>Assegnare un ruolo a un'entità servizio

Esempio di assegnazione di un'entità servizio a un ruolo.

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>Più assegnazioni di ruolo

Esempi di assegnazione e rimozione di più ruoli contemporaneamente.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

In questo esempio viene rimossa un'assegnazione di ruolo per l'utente specificato.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole

# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser

```

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
