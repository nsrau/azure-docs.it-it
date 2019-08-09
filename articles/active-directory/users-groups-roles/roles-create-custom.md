---
title: Creare una definizione di ruolo personalizzata in Azure AD controllo degli accessi in base al ruolo-Azure Active Directory | Microsoft Docs
description: Creare ruoli di Azure AD personalizzati con ambito di risorse sulle risorse Azure Active Directory.
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
ms.openlocfilehash: c1166839608c709db9aa052d6d0db5221fa15354
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880738"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Creare un ruolo personalizzato e assegnarlo all'ambito della risorsa in Azure Active Directory

Questo articolo descrive come creare nuovi ruoli personalizzati in Azure Active Directory (Azure AD). I ruoli personalizzati possono essere creati nella scheda [ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) della pagina Panoramica Azure ad. Il ruolo può essere assegnato solo a livello di directory o a un ambito di risorsa di registrazione dell'app.

Per ulteriori informazioni, vedere [Cenni preliminari sui ruoli personalizzati](roles-custom-overview.md) per le nozioni di base dei ruoli personalizzati.

## <a name="using-the-azure-ad-portal"></a>Uso del portale Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creare un nuovo ruolo personalizzato per concedere l'accesso per gestire le registrazioni dell'app

1. Accedere al [centro](https://aad.portal.azure.com) di amministrazione di Azure ad con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare ruoli **Azure Active Directory** > **e amministratori** > **nuovo ruolo personalizzato**.

   ![Creazione o modifica dei ruoli dalla pagina ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **nozioni di base** specificare un nome e una descrizione per il ruolo, quindi fare clic su **Avanti**.

   ![specificare un nome e una descrizione per un ruolo personalizzato nella scheda nozioni di base](./media/roles-create-custom/basics-tab.png)

1. Nella scheda **autorizzazioni** selezionare le autorizzazioni necessarie per gestire le proprietà di base e le credenziali delle registrazioni dell'app. Per una descrizione dettagliata di ogni autorizzazione, vedere [sottotipi e autorizzazioni per la registrazione di applicazioni in Azure Active Directory](./roles-custom-available-permissions.md).
   1. Immettere innanzitutto "credentials" nella barra di ricerca e selezionare l' `microsoft.directory/applications/credentials/update` autorizzazione.

      ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda autorizzazioni](./media/roles-create-custom/permissions-tab.png)

   1. Immettere quindi "Basic" nella barra di ricerca, selezionare l' `microsoft.directory/applications/basic/update` autorizzazione e quindi fare clic su **Avanti**.
1. Nella scheda **Verifica e crea** verificare le autorizzazioni e selezionare **Crea**.

Il ruolo personalizzato sarà visualizzato nell'elenco dei ruoli disponibili da assegnare.

## <a name="assign-a-role-scoped-to-a-resource"></a>Assegnare un ruolo con ambito a una risorsa

Analogamente ai ruoli predefiniti, i ruoli personalizzati possono essere assegnati a livello di organizzazione per concedere l'accesso a tutte le registrazioni di app. Tuttavia, i ruoli personalizzati possono essere assegnati anche nell'ambito della risorsa. In questo modo è possibile assegnare all'assegnatario l'autorizzazione per aggiornare le credenziali e le proprietà di base di una singola app senza dover creare un secondo ruolo personalizzato.

1. Se non è già stato fatto, accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni per sviluppatori di applicazioni nell'organizzazione Azure ad.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare la registrazione dell'app a cui si concede l'accesso per la gestione. Potrebbe essere necessario selezionare **tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app nell'organizzazione Azure ad.

    ![Selezionare la registrazione dell'app come ambito di risorsa per un'assegnazione di ruolo](./media/roles-create-custom/appreg-all-apps.png)

1. Nella registrazione dell'app selezionare **ruoli e amministratori**. Se non ne è già stato creato uno, le istruzioni sono incluse nella [procedura precedente](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selezionare il ruolo per aprire la pagina assegnazioni.
1. Selezionare **Aggiungi assegnazione** per aggiungere un utente. All'utente non verranno concesse autorizzazioni per alcuna registrazione dell'app diversa da quella selezionata.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Creare un ruolo personalizzato usando Azure AD PowerShell

### <a name="prepare-powershell"></a>Preparare PowerShell

Per prima cosa, è necessario [scaricare il modulo PowerShell di Azure ad Preview](https://www.powershellgallery.com/packages/AzureADPreview).

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

Creare un nuovo ruolo usando il seguente script di PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assegnare il ruolo personalizzato usando Azure AD PowerShell

Assegnare il ruolo utilizzando lo script PowerShell seguente:

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

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Creare un ruolo personalizzato usando Microsoft Graph API

1. Creare la definizione di ruolo.

    Richiesta HTTP per creare una definizione di ruolo personalizzata.

    INSERISCI

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

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

    INSERISCI

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
