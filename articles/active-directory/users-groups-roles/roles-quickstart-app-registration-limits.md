---
title: Concedere a una singola autorizzazione per superare le restrizioni per le registrazioni di app-Azure Active Directory | Microsoft Docs
description: Assegnare un ruolo personalizzato per concedere registrazioni di app senza restrizioni nell'Azure AD Active Directory.
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
ms.openlocfilehash: c91de3de743d168bea207f27fb162486ea625a63
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026276"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Avvio rapido: Concedere l'autorizzazione per creare registrazioni di app illimitate

In questa Guida introduttiva verrà creato un ruolo personalizzato con l'autorizzazione per creare un numero illimitato di registrazioni per l'app e quindi assegnare tale ruolo a un utente. L'utente assegnato può quindi usare il portale di Azure AD, Azure AD PowerShell, Azure AD API Graph o l'API Microsoft Graph per creare registrazioni di applicazioni. Diversamente dal ruolo predefinito per sviluppatori di applicazioni, questo ruolo personalizzato concede la possibilità di creare un numero illimitato di registrazioni di applicazioni. Il ruolo sviluppatore di applicazioni concede la possibilità, ma il numero totale di oggetti creati è limitato a 250 per impedire che raggiunga [la quota di oggetti a livello di directory](directory-service-limits-restrictions.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisite"></a>Prerequisito

Il ruolo con privilegi minimi necessario per creare e assegnare Azure AD ruoli personalizzati è l'amministratore del ruolo con privilegi.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Creare un nuovo ruolo personalizzato usando il portale di Azure AD

1. Accedere all'interfaccia di [amministrazione di Azure AD](https://aad.portal.azure.com)@no__t le autorizzazioni di amministratore del ruolo con privilegi di 1With o amministratore globale nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory**, selezionare **ruoli e amministratori**, quindi selezionare **nuovo ruolo personalizzato**.

    ![Creazione o modifica dei ruoli dalla pagina ruoli e amministratori](./media/roles-create-custom/new-custom-role.png)

1. Nella scheda **nozioni di base** fornire "Application Registration Creator" per il nome del ruolo e "può creare un numero illimitato di registrazioni di applicazioni" per la descrizione del ruolo, quindi selezionare **Avanti**.

    ![specificare un nome e una descrizione per un ruolo personalizzato nella scheda nozioni di base](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Nella scheda **autorizzazioni** immettere "Microsoft. directory/Applications/create" nella casella di ricerca, quindi selezionare le caselle di controllo accanto alle autorizzazioni desiderate e quindi fare clic su **Avanti**.

    ![Selezionare le autorizzazioni per un ruolo personalizzato nella scheda autorizzazioni](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Nella scheda **Verifica e crea** verificare le autorizzazioni e selezionare **Crea**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Assegnare il ruolo a un utente usando il portale di Azure AD

1. Accedere all'interfaccia di [amministrazione di Azure AD](https://aad.portal.azure.com)@no__t 1With amministratore del ruolo con privilegi o autorizzazioni di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory** , quindi selezionare **ruoli e amministratori**.
1. Selezionare il ruolo Autore registrazione applicazione e selezionare **Aggiungi assegnazione**.
1. Selezionare l'utente desiderato e fare clic su **Seleziona** per aggiungere l'utente al ruolo.

La procedura è stata completata. In questa Guida introduttiva è stato creato un ruolo personalizzato con l'autorizzazione per creare un numero illimitato di registrazioni per l'app e quindi assegnare tale ruolo a un utente.

> [!TIP]
> Per assegnare il ruolo a un'applicazione usando il portale di Azure AD, immettere il nome dell'applicazione nella casella di ricerca della pagina di assegnazione. Per impostazione predefinita, le applicazioni non vengono visualizzate nell'elenco, ma vengono restituite nei risultati della ricerca.

### <a name="app-registration-permissions"></a>Autorizzazioni di registrazione dell'app

Sono disponibili due autorizzazioni per concedere la possibilità di creare registrazioni di applicazioni, ognuna con un comportamento diverso.

- Microsoft. directory/Applications/createAsOwner: L'assegnazione di questa autorizzazione comporta l'aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata verrà conteggiata in base alla quota degli oggetti creati 250 dell'autore.
- Microsoft. directory/applicationPolicies/crea: L'assegnazione di questa autorizzazione comporta la mancata aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata non verrà conteggiata con la quota degli oggetti creati 250 dell'autore. Usare questa autorizzazione con cautela, perché non ci sono elementi che impediscono all'assegnatario di creare registrazioni di app fino a quando non viene raggiunta la quota a livello di directory. Se entrambe le autorizzazioni sono assegnate, questa autorizzazione avrà la precedenza.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Creare un ruolo personalizzato usando Azure AD PowerShell

Creare un nuovo ruolo usando il seguente script di PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assegnare il ruolo personalizzato usando Azure AD PowerShell

#### <a name="prepare-powershell"></a>Preparare PowerShell

Per prima cosa, installare il modulo Azure AD PowerShell dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importare quindi il modulo di anteprima di Azure AD PowerShell usando il comando seguente:

```powershell
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, associare la versione restituita dal comando seguente a quella elencata qui:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Assegnare il ruolo personalizzato

Assegnare il ruolo utilizzando lo script PowerShell seguente:

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Creare un ruolo personalizzato usando Microsoft Graph API

Richiesta HTTP per creare il ruolo personalizzato.

INSERISCI

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Assegnare il ruolo personalizzato usando Microsoft Graph API

L'assegnazione di ruolo combina un ID dell'entità di sicurezza, che può essere un utente o un'entità servizio, un ID di definizione del ruolo (ruolo) e un ambito di risorse Azure AD.

Richiesta HTTP per assegnare un ruolo personalizzato.

INSERISCI

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

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
