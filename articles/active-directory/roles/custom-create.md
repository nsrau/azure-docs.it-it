---
title: Creare ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure AD | Microsoft Docs
description: Creare e assegnare ruoli di Azure AD personalizzati con ambito a livello di risorsa per le risorse di Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 507ef55a6fa3976475dbf08f88ee36cd1977464d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421023"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Creare e assegnare un ruolo personalizzato in Azure Active Directory

Questo articolo descrive come creare nuovi ruoli personalizzati in Azure Active Directory (Azure AD). Per le nozioni di base sui ruoli personalizzati, vedere la [panoramica dei ruoli personalizzati](custom-overview.md). Il ruolo può essere assegnato solo a livello di directory o di risorsa di registrazione dell'app.

I ruoli personalizzati possono essere creati nella scheda [Ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) della pagina di panoramica di Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Creare un ruolo nel portale di Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creare un nuovo ruolo personalizzato per concedere l'accesso per la gestione delle registrazioni di app

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory** > **Ruoli e amministratori** > **Nuovo ruolo personalizzato**.

   ![Creare e modificare i ruoli nella pagina Ruoli e amministratori](./media/custom-create/new-custom-role.png)

1. Nella scheda **Generale** specificare un nome e una descrizione per il ruolo e quindi fare clic su **Avanti**.

   ![Specificare un nome e una descrizione per un ruolo personalizzato nella scheda Generale](./media/custom-create/basics-tab.png)

1. Nella scheda **Autorizzazioni** selezionare le autorizzazioni necessarie per gestire le credenziali e le proprietà di base delle registrazioni di app. Per una descrizione dettagliata di ogni autorizzazione, vedere [Sottotipi per la registrazione di applicazioni e autorizzazioni in Azure Active Directory](custom-available-permissions.md).
   1. Immettere prima "credentials" nella barra di ricerca e selezionare l'autorizzazione `microsoft.directory/applications/credentials/update`.

      ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda Autorizzazioni](./media/custom-create/permissions-tab.png)

   1. Immettere quindi "basic" nella barra di ricerca, selezionare l'autorizzazione `microsoft.directory/applications/basic/update` e quindi fare clic su **Avanti**.
1. Nella scheda **Rivedi e crea** rivedere le autorizzazioni e selezionare **Crea**.

Il ruolo personalizzato sarà visualizzato nell'elenco dei ruoli disponibili da assegnare.

## <a name="create-a-role-using-powershell"></a>Creare un ruolo con PowerShell

### <a name="prepare-powershell"></a>Preparare PowerShell

È necessario prima [scaricare il modulo PowerShell di Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

``` PowerShell
Install-Module AzureADPreview
Import-Module AzureADPreview
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

``` PowerShell
Get-Module AzureADPreview
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
    > `"templateId": "GUID"`È un parametro facoltativo che viene inviato nel corpo a seconda del requisito. Se si ha la necessità di creare più ruoli personalizzati con parametri comuni, è preferibile creare un modello e definire un `templateId` valore. È possibile generare un `templateId` valore in anticipo utilizzando il cmdlet di PowerShell `(New-Guid).Guid` . 

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

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con le autorizzazioni per sviluppatori di applicazioni nell'organizzazione Azure ad.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare la registrazione dell'app a cui si concede l'accesso per la gestione. Potrebbe essere necessario selezionare **Tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app presenti nell'organizzazione di Azure AD.

    ![Selezionare la registrazione dell'app come ambito di risorsa per un'assegnazione di ruolo](./media/custom-create/appreg-all-apps.png)

1. Nella registrazione dell'app selezionare **Ruoli e amministratori**. Se non è stato ancora creato un ruolo, tornare alle istruzioni nella [procedura precedente](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selezionare il ruolo per aprire la pagina **Assegnazioni**.
1. Selezionare **Aggiungi assegnazione** per aggiungere un utente. All'utente verranno assegnate tutte le autorizzazioni limitatamente alla registrazione dell'app selezionata.

## <a name="next-steps"></a>Passaggi successivi

- Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](permissions-reference.md).
- Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](/azure/active-directory/fundamentals/users-default-permissions?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).
