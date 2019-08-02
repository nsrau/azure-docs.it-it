---
title: Visualizzare le autorizzazioni dei ruoli di amministratore nell'interfaccia di amministrazione-Azure Active Directory | Microsoft Docs
description: È ora possibile visualizzare e gestire i membri di un ruolo amministratore Azure AD nell'interfaccia di amministrazione di Azure AD.
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
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707552"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Visualizzare le assegnazioni di ruolo personalizzate in Azure Active Directory

Questo articolo descrive come visualizzare i ruoli personalizzati assegnati in Azure Active Directory (Azure AD). In Azure Active Directory (Azure AD) i ruoli possono essere assegnati a livello di directory o con un ambito di una singola applicazione. Le assegnazioni di ruolo nell'ambito della directory vengono aggiunte all'elenco di assegnazioni di ruolo applicazione singola, ma le assegnazioni di ruolo nell'ambito dell'applicazione singola non vengono aggiunte all'elenco di assegnazioni a livello di directory.

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Visualizzare le assegnazioni di un ruolo con ambito di directory tramite il portale di Azure AD

1. Accedere al [centro](https://aad.portal.azure.com) di amministrazione di Azure ad con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory**, selezionare **ruoli e amministratori**, quindi selezionare un ruolo per aprirlo e visualizzarne le proprietà.
1. Selezionare **assegnazioni** per visualizzare le assegnazioni per il ruolo.

    ![Visualizzare le assegnazioni di ruolo e le autorizzazioni quando si apre un ruolo dall'elenco](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Visualizzare le assegnazioni di un ruolo con ambito di directory usando Azure AD PowerShell

È possibile automatizzare il modo in cui si assegnano Azure AD ruoli di amministratore agli utenti usando Azure PowerShell. Questo articolo usa il modulo [Azure Active Directory PowerShell versione 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

### <a name="prepare-powershell"></a>Preparare PowerShell

Per prima cosa, è necessario [scaricare il modulo PowerShell di Azure ad Preview](https://www.powershellgallery.com/packages/AzureAD/).

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Visualizzare le assegnazioni di un ruolo

Esempio di visualizzazione delle assegnazioni di un ruolo.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Visualizzare le assegnazioni di un ruolo con ambito directory usando Microsoft Graph API

Richiesta HTTP per ottenere un'assegnazione di ruolo per una determinata definizione di ruolo.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Risposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Visualizzare le assegnazioni di un ruolo con ambito di applicazione singola usando il portale di Azure AD (anteprima)

1. Accedere al [centro](https://aad.portal.azure.com) di amministrazione di Azure ad con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare Azure Active Directory, selezionare **registrazioni app**, quindi selezionare la registrazione dell'app per visualizzarne le proprietà. Potrebbe essere necessario selezionare **tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app nell'organizzazione Azure ad.

    ![Creare o modificare le registrazioni di app dalla pagina Registrazioni app](./media/roles-create-custom/appreg-all-apps.png)

1. Selezionare **ruoli e amministratori**, quindi selezionare un ruolo per visualizzarne le proprietà.

    ![Visualizzare le assegnazioni dei ruoli di registrazione delle app dalla pagina Registrazioni app](./media/roles-view-assignments/appreg-assignments.png)

1. Selezionare **assegnazioni** per visualizzare le assegnazioni per il ruolo.

    ![Visualizzare le assegnazioni dei ruoli di registrazione delle app dalle proprietà di una registrazione dell'app](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
