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
ms.openlocfilehash: dcf4bc04a1c111e545d545af64f3940d3aa4585d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707708"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Creare un ruolo personalizzato e assegnare un ambito di risorse in Azure Active Directory

Questo articolo descrive come creare nuovi ruoli personalizzati in Azure Active Directory (Azure AD). I ruoli personalizzati possono essere creati nella scheda **ruoli e amministratori** della pagina Panoramica Azure ad o [nella pagina di registrazione dell'applicazione](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). Il ruolo può essere assegnato a livello di directory o solo per le registrazioni dell'app.

Per ulteriori informazioni, vedere [Cenni preliminari sui ruoli personalizzati](roles-custom-overview.md) per le nozioni di base dei ruoli personalizzati.

## <a name="using-the-azure-ad-portal"></a>Uso del portale Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creare un nuovo ruolo personalizzato per concedere l'accesso per gestire le registrazioni dell'app

1. Accedere al [centro](https://aad.portal.azure.com) di amministrazione di Azure ad con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare ruoli **Azure Active Directory** > **e amministratori** > **nuovo ruolo personalizzato**.

   ![Creazione o modifica dei ruoli dalla pagina ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **nozioni di base** specificare un nome e una descrizione per il ruolo.

   ![specificare un nome e una descrizione per un ruolo personalizzato nella scheda nozioni di base](./media/roles-create-custom/basics-tab.png)

1. Per selezionare le autorizzazioni necessarie per gestire le credenziali di registrazione delle app e le proprietà di base, ad esempio nome:
   1. Immettere "credentials" nella barra di ricerca e selezionare `microsoft.directory/applications/credentials/update` l'autorizzazione.

      ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda autorizzazioni](./media/roles-create-custom/permissions-tab.png)

   1. Immettere "Basic" nella barra di ricerca, selezionare l' `microsoft.directory/applications/basic/update` autorizzazione e quindi fare clic su **Avanti**.
1. Nella scheda **Verifica e crea** verificare le autorizzazioni e selezionare **Crea**.

Il ruolo personalizzato sarà visualizzato nell'elenco dei ruoli disponibili da assegnare.

## <a name="assign-a-role-scoped-to-a-resource"></a>Assegnare un ruolo con ambito a una risorsa

Analogamente ai ruoli predefiniti, i ruoli personalizzati possono essere assegnati nell'ambito dell'organizzazione predefinito per concedere l'accesso a tutte le registrazioni dell'app. Tuttavia, i ruoli personalizzati possono essere assegnati anche nell'ambito dell'oggetto. In questo modo è possibile assegnare all'assegnatario l'autorizzazione per aggiornare le credenziali e le proprietà di base di una singola app senza dover creare un secondo ruolo personalizzato.

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con le autorizzazioni per sviluppatori di applicazioni nell'organizzazione Azure ad.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare la registrazione dell'app a cui si concede l'accesso per la gestione. Potrebbe essere necessario selezionare **tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app nell'organizzazione Azure ad.

    ![Selezionare la registrazione dell'app come ambito di risorsa per un'assegnazione di ruolo](./media/roles-create-custom/appreg-all-apps.png)

1. Nella registrazione dell'app selezionare **ruoli e amministratori**. Se non ne è già stato creato uno, le istruzioni sono incluse nella [procedura precedente](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Se si assegna questo ruolo dal contesto di una registrazione di app aperta, l'assegnatario ha le autorizzazioni per la registrazione di una singola app. Il ruolo che si sta assegnando viene visualizzato nell'elenco a ogni registrazione dell'app. Questo modello di accesso, in cui a un proprietario può essere assegnata l'autorizzazione per Azure AD risorse specifiche per ruoli, è simile al modello usato nel controllo degli accessi in base al ruolo di [Azure](../../role-based-access-control/overview.md)

1. Selezionare il ruolo per aprire la pagina assegnazioni.
1. Selezionare **Aggiungi assegnazione** per aggiungere un utente. All'utente non verranno concesse autorizzazioni per alcuna registrazione dell'app diversa da quella selezionata.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Creare un ruolo personalizzato usando Azure AD PowerShell

### <a name="prepare-powershell"></a>Preparare PowerShell

In primo luogo è necessario [scaricare il modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

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

### <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Creare un nuovo ruolo usando il seguente script di PowerShell:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
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
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/basic/update",
                    "microsoft.directory/applications/credentials/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<GET NEW GUID AND INSERT HERE>",
    "version":"1"
    }
    ```

1. Creare l'assegnazione di ruolo.

    Richiesta HTTP per creare una definizione di ruolo personalizzata.

    INSERISCI

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScopes":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
