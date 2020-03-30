---
title: Rimuovere i limiti per la creazione di registrazioni di app - Azure AD Documenti Microsoft
description: Assegnare un ruolo personalizzato per concedere registrazioni di app senza restrizioni in Azure AD Active DirectoryAssign a custom role to grant unrestricted app registrations in the Azure AD Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559046"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Guida introduttiva: Concedere l'autorizzazione per creare registrazioni illimitate delle app

In questa guida introduttiva verrà creato un ruolo personalizzato con l'autorizzazione per creare un numero illimitato di registrazioni di app e quindi assegnare tale ruolo a un utente. L'utente assegnato può quindi usare il portale di Azure AD, Azure AD PowerShell o l'API Microsoft Graph per creare le registrazioni delle applicazioni. A differenza del ruolo di sviluppatore di applicazioni incorporato, questo ruolo personalizzato concede la possibilità di creare un numero illimitato di registrazioni di applicazioni. Il ruolo Sviluppo applicazione concede la possibilità, ma il numero totale di oggetti creati è limitato a 250 per impedire di raggiungere la quota di oggetti a livello di [directory.](directory-service-limits-restrictions.md)

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisite"></a>Prerequisito

Il ruolo con privilegi minimi necessario per creare e assegnare ruoli personalizzati di Azure AD è l'amministratore del ruolo con privilegi.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Creare un nuovo ruolo personalizzato usando il portale di Azure ADCreate a new custom role using the Azure AD portal

1. Accedere [all'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con le autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione di Azure AD.
1. Selezionare **Azure Active Directory**, ruoli e **amministratori**e quindi Nuovo **ruolo personalizzato**.

    ![Creare o modificare ruoli dalla pagina Ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **Nozioni di base** specificare "Application Registration Creator" come nome del ruolo e "Can create an unlimited number of application registrations" per la descrizione del ruolo, quindi selezionare **Next**.

    ![specificare un nome e una descrizione per un ruolo personalizzato nella scheda Nozioni di base](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Nella scheda **Autorizzazioni** immettere "microsoft.directory/applications/create" nella casella di ricerca, quindi selezionare le caselle di controllo accanto alle autorizzazioni desiderate, quindi scegliere **Avanti**.

    ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda Autorizzazioni](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Nella scheda **Revisione e creazione** esaminare le autorizzazioni e selezionare **Crea**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Assegnare il ruolo a un utente tramite il portale di Azure ADAssign the role to a user using the Azure AD portal

1. Accedere [all'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con le autorizzazioni di amministratore del ruolo Privileged o di amministratore globale nell'organizzazione di Azure AD.
1. Selezionare **Azure Active Directory** e quindi Ruoli e **amministratori**.
1. Selezionare il ruolo Creatore registrazione applicazioni e selezionare **Aggiungi assegnazione**.
1. Selezionare l'utente desiderato e fare clic su **Seleziona** per aggiungere l'utente al ruolo.

La procedura è stata completata. In questa guida introduttiva è stato creato correttamente un ruolo personalizzato con l'autorizzazione per creare un numero illimitato di registrazioni di app e quindi assegnare tale ruolo a un utente.

> [!TIP]
> Per assegnare il ruolo a un'applicazione usando il portale di Azure AD, immettere il nome dell'applicazione nella casella di ricerca della pagina di assegnazione. Le applicazioni non vengono visualizzate nell'elenco per impostazione predefinita, ma vengono restituite nei risultati della ricerca.

### <a name="app-registration-permissions"></a>Autorizzazioni di registrazione dell'app

Sono disponibili due autorizzazioni per concedere la possibilità di creare registrazioni di applicazioni, ognuna con un comportamento diverso.

- microsoft.directory/applications/createAsOwner: l'assegnazione di questa autorizzazione comporta l'aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata verrà conteggiata rispetto alla quota di 250 oggetti creati dal creatore.
- microsoft.directory/applicationPolicies/create: l'assegnazione di questa autorizzazione comporta l'aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata non verrà conteggiata rispetto alla quota di 250 oggetti creati dal creatore. Utilizzare questa autorizzazione con attenzione, perché non vi è nulla che impedisce all'assegnatario di creare registrazioni di app fino a quando non viene raggiunta la quota a livello di directory. Se vengono assegnate entrambe le autorizzazioni, questa autorizzazione ha la precedenza.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Creare un ruolo personalizzato usando Azure AD PowerShellCreate a custom role using Azure AD PowerShell

Creare un nuovo ruolo usando lo script di PowerShell seguente:Create a new role using the following PowerShell script:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assegnare il ruolo personalizzato usando Azure AD PowerShellAssign the custom role using Azure AD PowerShell

#### <a name="prepare-powershell"></a>Preparare PowerShellPrepare PowerShell

Installare innanzitutto il modulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importare quindi il modulo di anteprima di Azure AD PowerShell usando il comando seguente:Then import the Azure AD PowerShell preview module, using the following command:

```powershell
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, far corrispondere la versione restituita dal comando seguente a quella elencata di seguito:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Assegnare il ruolo personalizzatoAssign the custom role

Assegnare il ruolo usando lo script di PowerShell seguente:Assign the role using the below PowerShell script:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Creare un ruolo personalizzato usando l'API Microsoft GraphCreate a custom role using Microsoft Graph API

Richiesta HTTP per creare il ruolo personalizzato.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Corpo

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Assegnare il ruolo personalizzato tramite l'API Microsoft GraphAssign the custom role using Microsoft Graph API

L'assegnazione di ruolo combina un ID entità di sicurezza (che può essere un utente o un'entità servizio), un ID definizione di ruolo (ruolo) e un ambito di risorsa di Azure AD.

Richiesta HTTP per assegnare un ruolo personalizzato.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
