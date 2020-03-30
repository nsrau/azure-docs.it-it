---
title: Creare ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure AD. Documenti Microsoft
description: Creare e assegnare ruoli di Azure AD personalizzati con ambito di risorsa nelle risorse di Azure Active Directory.Create and assign custom Azure AD roles with resource scope on Azure Active Directory resources.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025282"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Creare e assegnare un ruolo personalizzato in Azure Active DirectoryCreate and assign a custom role in Azure Active Directory

Questo articolo descrive come creare nuovi ruoli personalizzati in Azure Active Directory (Azure AD). Per le nozioni di base sui ruoli personalizzati, vedere Panoramica dei [ruoli personalizzati.](roles-custom-overview.md) Il ruolo può essere assegnato solo nell'ambito a livello di directory o in un ambito di risorse di registrazione dell'app.

I ruoli personalizzati possono essere creati nella scheda [Ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) della pagina Panoramica di Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Creare un ruolo nel portale di AzureCreate a role in the Azure portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creare un nuovo ruolo personalizzato per concedere l'accesso per gestire le registrazioni delle appCreate a new custom role to grant access to manage app registrations

1. Accedere [all'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con le autorizzazioni di amministratore del ruolo Privileged o di amministratore globale nell'organizzazione di Azure AD.
1. Selezionare**Ruoli e amministratori** > di **Azure Active Directory** > **Nuovo ruolo personalizzato**.

   ![Creare o modificare ruoli dalla pagina Ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **Nozioni di base** specificare un nome e una descrizione per il ruolo e quindi fare clic su **Avanti**.

   ![specificare un nome e una descrizione per un ruolo personalizzato nella scheda Nozioni di base](./media/roles-create-custom/basics-tab.png)

1. Nella scheda **Autorizzazioni** selezionare le autorizzazioni necessarie per gestire le proprietà di base e le proprietà delle credenziali delle registrazioni di app. Per una descrizione dettagliata di ogni autorizzazione, vedere Sottotipi e autorizzazioni per la registrazione delle applicazioni [in Azure Active Directory.](./roles-custom-available-permissions.md)
   1. In primo luogo, immettere "credenziali" `microsoft.directory/applications/credentials/update` nella barra di ricerca e selezionare l'autorizzazione.

      ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda Autorizzazioni](./media/roles-create-custom/permissions-tab.png)

   1. Successivamente, immettere "basic" nella barra `microsoft.directory/applications/basic/update` di ricerca, selezionare l'autorizzazione e quindi fare clic su **Avanti**.
1. Nella scheda **Revisione e creazione** esaminare le autorizzazioni e selezionare **Crea**.

Il ruolo personalizzato verrà visualizzato nell'elenco dei ruoli disponibili da assegnare.

## <a name="create-a-role-using-powershell"></a>Creare un ruolo tramite PowerShellCreate a role using PowerShell

### <a name="prepare-powershell"></a>Preparare PowerShellPrepare PowerShell

Innanzitutto, è necessario [scaricare il modulo PowerShell](https://www.powershellgallery.com/packages/AzureADPreview)di Azure AD Preview .

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Creare un nuovo ruolo usando lo script di PowerShell seguente:Create a new role using the following PowerShell script:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assegnare il ruolo personalizzato usando Azure AD PowerShellAssign the custom role using Azure AD PowerShell

Assegnare il ruolo usando lo script di PowerShell seguente:Assign the role using the below PowerShell script:

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

## <a name="create-a-role-with-graph-api"></a>Creare un ruolo con l'API GraphCreate a role with Graph API

1. Creare la definizione di ruolo.

    Richiesta HTTP per creare una definizione di ruolo personalizzata.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Corpo

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Creare l'assegnazione di ruolo.

    Richiesta HTTP per creare una definizione di ruolo personalizzata.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Corpo

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Assegnare un ruolo personalizzato con ambito a una risorsaAssign a custom role scoped to a resource

Analogamente ai ruoli predefiniti, i ruoli personalizzati vengono assegnati per impostazione predefinita nell'ambito predefinito a livello di organizzazione per concedere le autorizzazioni di accesso su tutte le registrazioni di app nell'organizzazione. A differenza dei ruoli predefiniti, i ruoli personalizzati possono anche essere assegnati nell'ambito di una singola risorsa di Azure AD. In questo modo è possibile concedere all'utente l'autorizzazione per aggiornare le credenziali e le proprietà di base di una singola app senza dover creare un secondo ruolo personalizzato.

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con le autorizzazioni per sviluppatori di applicazioni nell'organizzazione di Azure AD.
1. Selezionare **Registrazioni app**.
1. Selezionare la registrazione dell'app a cui si sta concedendo l'accesso da gestire. Potrebbe essere necessario selezionare **Tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni delle app nell'organizzazione di Azure AD.

    ![Selezionare la registrazione dell'app come ambito di risorsa per un'assegnazione di ruolo](./media/roles-create-custom/appreg-all-apps.png)

1. Nella registrazione dell'app selezionare **Ruoli e amministratori**. Se non ne è già stato creato uno, le istruzioni sono riportate nella [procedura precedente.](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)

1. Selezionare il ruolo per aprire la pagina **Assegnazioni.**
1. Selezionare **Aggiungi assegnazione** per aggiungere un utente. All'utente verranno concesse tutte le autorizzazioni solo per la registrazione dell'app selezionata.

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
