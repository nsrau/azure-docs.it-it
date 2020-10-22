---
title: Rimuovere le assegnazioni di ruolo da un gruppo in Azure Active Directory | Microsoft Docs
description: Anteprima dei ruoli di Azure AD personalizzati per la delega della gestione delle identità. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f441930d9d99f35c2e53bb040b0db0a427659
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377568"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Rimuovere le assegnazioni di ruolo da un gruppo in Azure Active Directory

Questo articolo descrive come un amministratore IT può rimuovere Azure AD ruoli assegnati ai gruppi. Nel portale di Azure è ora possibile rimuovere le assegnazioni di ruolo dirette e indirette a un utente. Se a un utente viene assegnato un ruolo da un'appartenenza a un gruppo, rimuovere l'utente dal gruppo per rimuovere l'assegnazione di ruolo.

## <a name="using-azure-admin-center"></a>Uso dell'interfaccia di amministrazione di Azure

1. Accedere al centro di [amministrazione di Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.

1. Selezionare **ruoli e amministratori** > **_nome ruolo_*_.

1. Selezionare il gruppo da cui si desidera rimuovere l'assegnazione di ruolo e selezionare _ * Rimuovi assegnazione * *.

   ![Rimuovere un'assegnazione di ruolo da un gruppo selezionato.](./media/groups-remove-assignment/remove-assignment.png)

1. Quando viene richiesto di confermare l'azione, selezionare **Sì**.

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creare un gruppo che può essere assegnato al ruolo

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Ottenere la definizione di ruolo a cui si vuole assegnare il gruppo

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Rimuovere l'assegnazione di ruolo

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Creare un gruppo a cui è possibile assegnare un ruolo Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Ottenere la definizione del ruolo

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Creare l'assegnazione di ruolo

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Elimina assegnazione ruolo

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
