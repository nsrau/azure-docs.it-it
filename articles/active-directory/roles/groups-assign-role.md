---
title: Assegnare un ruolo a un gruppo cloud in Azure Active Directory | Microsoft Docs
description: Assegnare un ruolo Azure AD a un gruppo assegnabile al ruolo in portale di Azure, PowerShell o API Graph.
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
ms.openlocfilehash: 28df0e420183239eae21aa18c807bb82b4ec7649
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377679"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Assegnare un ruolo a un gruppo cloud in Azure Active Directory

In questa sezione viene descritto in che modo un amministratore IT può assegnare il ruolo Azure Active Directory (Azure AD) a un gruppo di Azure AD.

## <a name="using-azure-ad-admin-center"></a>Uso di Azure AD interfaccia di amministrazione

L'assegnazione di un gruppo a un ruolo Azure AD è simile all'assegnazione di utenti e di entità servizio, ad eccezione del fatto che è possibile utilizzare solo i gruppi assegnabili al ruolo. Nel portale di Azure vengono visualizzati solo i gruppi assegnabili al ruolo.

1. Accedere al centro di [amministrazione di Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.

1. Selezionare **Azure Active Directory**  >  **ruoli e amministratori**e selezionare il ruolo che si desidera assegnare.

1. Nella pagina ** _nome ruolo_*_ selezionare > _* Aggiungi assegnazione**.

   ![Aggiungere la nuova assegnazione di ruolo](./media/groups-assign-role/add-assignment.png)

1. Selezionare il gruppo. Vengono visualizzati solo i gruppi che possono essere assegnati ai ruoli Azure AD.

    [![Per una nuova assegnazione di ruolo vengono visualizzati solo i gruppi assegnabili.](./media/groups-assign-role/eligible-groups.png "Per una nuova assegnazione di ruolo vengono visualizzati solo i gruppi assegnabili.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Selezionare **Aggiungi**.

Per ulteriori informazioni sull'assegnazione delle autorizzazioni per i ruoli, vedere [assegnare ruoli di amministratore e non amministratore agli utenti](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creare un gruppo che può essere assegnato al ruolo

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Ottenere la definizione di ruolo per il ruolo che si vuole assegnare

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Creare un gruppo a cui è possibile assegnare Azure AD ruolo

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
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

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Creare l'assegnazione di ruolo

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
