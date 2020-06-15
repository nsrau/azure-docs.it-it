---
title: Creare ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure AD | Microsoft Docs
description: Creare e assegnare ruoli di Azure AD personalizzati con ambito a livello di risorsa per le risorse di Azure Active Directory.
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
ms.openlocfilehash: 2682a85f88a537630fbca86dd55541a152d8f37e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758640"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Creare e assegnare un ruolo personalizzato in Azure Active Directory

Questo articolo descrive come creare nuovi ruoli personalizzati in Azure Active Directory (Azure AD). Per le nozioni di base sui ruoli personalizzati, vedere la [panoramica dei ruoli personalizzati](roles-custom-overview.md). Il ruolo può essere assegnato solo a livello di directory o di risorsa di registrazione dell'app.

I ruoli personalizzati possono essere creati nella scheda [Ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) della pagina di panoramica di Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Creare un ruolo nel portale di Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creare un nuovo ruolo personalizzato per concedere l'accesso per la gestione delle registrazioni di app

1. Accedere all' [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com)  con autorizzazioni di amministratore ruolo con privilegi o amministratore globale nell'organizzazione di Azure AD.
1. Selezionare **Azure Active Directory** > **Ruoli e amministratori** > **Nuovo ruolo personalizzato**.

   ![Creare e modificare i ruoli nella pagina Ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **Generale** specificare un nome e una descrizione per il ruolo e quindi fare clic su **Avanti**.

   ![Specificare un nome e una descrizione per un ruolo personalizzato nella scheda Generale](./media/roles-create-custom/basics-tab.png)

1. Nella scheda **Autorizzazioni** selezionare le autorizzazioni necessarie per gestire le credenziali e le proprietà di base delle registrazioni di app. Per una descrizione dettagliata di ogni autorizzazione, vedere [Sottotipi per la registrazione di applicazioni e autorizzazioni in Azure Active Directory](./roles-custom-available-permissions.md).
   1. Immettere prima "credentials" nella barra di ricerca e selezionare l'autorizzazione `microsoft.directory/applications/credentials/update`.

      ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda Autorizzazioni](./media/roles-create-custom/permissions-tab.png)

   1. Immettere quindi "basic" nella barra di ricerca, selezionare l'autorizzazione `microsoft.directory/applications/basic/update` e quindi fare clic su **Avanti**.
1. Nella scheda **Rivedi e crea** rivedere le autorizzazioni e selezionare **Crea**.

Il ruolo personalizzato sarà visualizzato nell'elenco dei ruoli disponibili da assegnare.

## <a name="create-a-role-using-powershell"></a>Creare un ruolo con PowerShell

### <a name="prepare-powershell"></a>Preparare PowerShell

È necessario prima [scaricare il modulo PowerShell di Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

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

Creare un nuovo ruolo con lo script PowerShell seguente:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assegnare il ruolo personalizzato con Azure AD PowerShell

Assegnare il ruolo con lo script PowerShell seguente:

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

## <a name="create-a-role-with-graph-api"></a>Creare un ruolo con API Graph

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

  > [!Note]
  > "templateId": "GUID" è un parametro facoltativo inviato nel corpo a seconda del requisito. Se è presente un requisito per la creazione di più ruoli personalizzati diversi con parametri comuni, è consigliabile creare un modello e definire un elemento templateId. È possibile generare un elemento templateId prima di usare il cmdlet PowerShell (New-Guid).Guid. 

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


## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Assegnare un ruolo personalizzato con ambito limitato a una risorsa

Analogamente ai ruoli predefiniti, i ruoli personalizzati vengono assegnati per impostazione predefinita a livello di organizzazione predefinita per concedere autorizzazioni di accesso per tutte le registrazioni di app dell'organizzazione. Tuttavia, a differenza dei ruoli predefiniti, i ruoli personalizzati possono anche essere assegnati a livello di una singola risorsa di Azure AD. In questo modo, è possibile concedere all'utente l'autorizzazione per aggiornare le credenziali e le proprietà di base di una singola app senza dover creare un secondo ruolo personalizzato.

1. Accedere all' [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con le autorizzazioni di sviluppatore di applicazioni dell'organizzazione di Azure AD.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare la registrazione dell'app a cui si concede l'accesso per la gestione. Potrebbe essere necessario selezionare **Tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app presenti nell'organizzazione di Azure AD.

    ![Selezionare la registrazione dell'app come ambito di risorsa per un'assegnazione di ruolo](./media/roles-create-custom/appreg-all-apps.png)

1. Nella registrazione dell'app selezionare **Ruoli e amministratori**. Se non è stato ancora creato un ruolo, tornare alle istruzioni nella [procedura precedente](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selezionare il ruolo per aprire la pagina **Assegnazioni**.
1. Selezionare **Aggiungi assegnazione** per aggiungere un utente. All'utente verranno assegnate tutte le autorizzazioni limitatamente alla registrazione dell'app selezionata.

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](directory-assign-admin-roles.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
