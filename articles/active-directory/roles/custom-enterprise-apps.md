---
title: Autorizzazioni per i ruoli personalizzati per le assegnazioni di accesso alle app aziendali-Azure Active Directory | Microsoft Docs
description: Creare e assegnare ruoli di Azure AD personalizzati per l'accesso alle app aziendali in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d83005599c59f6a4249014139b594764df8acf
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375280"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Assegnare ruoli personalizzati per gestire le app aziendali in Azure Active Directory

Questo articolo illustra come creare un ruolo personalizzato con le autorizzazioni per gestire le assegnazioni di app aziendali per utenti e gruppi in Azure Active Directory (Azure AD). Per gli elementi delle assegnazioni di ruoli e il significato di termini quali sottotipi, autorizzazioni e set di proprietà, vedere [Cenni preliminari sui ruoli personalizzati](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Autorizzazioni ruolo app aziendali

Questo articolo illustra due autorizzazioni per le app aziendali. In tutti gli esempi viene utilizzata l'autorizzazione Update.

* Per leggere le assegnazioni di utenti e gruppi nell'ambito, concedere l' `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` autorizzazione
* Per gestire le assegnazioni di utenti e gruppi nell'ambito, concedere l' `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` autorizzazione

La concessione dell'autorizzazione Update comporta che l'assegnatario possa gestire le assegnazioni di utenti e gruppi alle app aziendali. È possibile concedere l'ambito di assegnazioni di utenti e/o gruppi per una singola applicazione o per tutte le applicazioni. Se concesso a livello di organizzazione, l'assegnatario può gestire le assegnazioni per tutte le applicazioni. Se viene eseguita a livello di applicazione, l'assegnatario può gestire le assegnazioni solo per l'applicazione specificata.

La concessione dell'autorizzazione Update viene eseguita in due passaggi:

1. Creazione di un ruolo personalizzato con autorizzazione `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Concedere agli utenti o ai gruppi le autorizzazioni per gestire le assegnazioni di utenti e gruppi alle app aziendali. Questo avviene quando è possibile impostare l'ambito a livello dell'organizzazione o a una singola applicazione.

## <a name="use-the-azure-ad-admin-center"></a>Usare l'interfaccia di amministrazione di Azure AD

### <a name="create-a-new-custom-role"></a>Creare un nuovo ruolo personalizzato

>[!NOTE]
> I ruoli personalizzati vengono creati e gestiti a livello dell'organizzazione e sono disponibili solo dalla pagina Panoramica dell'organizzazione.

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione.
1. Selezionare **Azure Active Directory**, selezionare **Ruoli e amministratori** e quindi selezionare **Nuovo ruolo personalizzato**.

    ![Aggiungere un nuovo ruolo personalizzato dall'elenco dei ruoli in Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Nella scheda **nozioni di base** specificare "Gestisci assegnazioni utenti e gruppi" per il nome del ruolo e "concedere le autorizzazioni per gestire le assegnazioni di utenti e gruppi" per la descrizione del ruolo, quindi selezionare **Avanti**.

    ![Specificare un nome e una descrizione per il ruolo personalizzato](./media/custom-enterprise-apps/role-name-and-description.png)

1. Nella scheda **autorizzazioni** immettere "Microsoft. directory/entità servizio/appRoleAssignedTo/Update" nella casella di ricerca, quindi selezionare le caselle di controllo accanto alle autorizzazioni desiderate e quindi fare clic su **Avanti**.

    ![Aggiungere le autorizzazioni al ruolo personalizzato](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Nella scheda **Rivedi e crea** rivedere le autorizzazioni e selezionare **Crea**.

    ![A questo punto è possibile creare il ruolo personalizzato](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Assegnare il ruolo a un utente usando il portale di Azure AD

1. Accedere al centro di [amministrazione Azure ad](https://aad.portal.azure.com) con le autorizzazioni del ruolo amministratore del ruolo con privilegi.
1. Selezionare **Azure Active Directory** e quindi selezionare **Ruoli e amministratori**.
1. Selezionare il ruolo **Concedi autorizzazioni per gestire le assegnazioni di utenti e gruppi** .

    ![Aprire ruoli e amministratori e cercare il ruolo personalizzato](./media/custom-enterprise-apps/select-custom-role.png)

1. Selezionare **Aggiungi assegnazione**, selezionare l'utente desiderato e quindi fare clic su **Seleziona** per aggiungere l'assegnazione di ruolo all'utente.

    ![Aggiungere un'assegnazione per il ruolo personalizzato all'utente](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Suggerimenti per l'assegnazione

* Per concedere autorizzazioni agli assegnatari per gestire gli utenti e l'accesso ai gruppi per tutte le applicazioni aziendali a livello di organizzazione, iniziare dall'elenco di **amministratori e ruoli** a livello di organizzazione nella pagina **Panoramica** Azure ad per l'organizzazione.
* Per concedere autorizzazioni agli assegnatari per gestire gli utenti e i gruppi di accesso per un'app aziendale specifica, passare all'app in Azure AD e aprire nell'elenco **ruoli e amministratori** per l'app. Selezionare il nuovo ruolo personalizzato e completare l'assegnazione dell'utente o del gruppo. Gli assegnatari possono gestire gli utenti e l'accesso ai gruppi solo per l'app specifica.
* Per testare l'assegnazione di ruolo personalizzata, accedere come assegnatario e aprire la pagina **utenti e gruppi** di un'applicazione per verificare che l'opzione **Aggiungi utente** sia abilitata.

    ![Verificare le autorizzazioni utente](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Usare Azure AD PowerShell

Per informazioni dettagliate, vedere [creare e assegnare un ruolo personalizzato](custom-create.md) e [assegnare ruoli personalizzati con ambito di risorse usando PowerShell](custom-assign-powershell.md).

Per prima cosa, installare il modulo Azure AD PowerShell dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importare quindi il modulo di anteprima di Azure AD PowerShell usando questo comando:

```powershell
PowerShell
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, la versione restituita dal comando seguente deve corrispondere a quella elencata qui:

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Creare un nuovo ruolo con lo script PowerShell seguente:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Assegnare il ruolo personalizzato

Assegnare il ruolo usando questo script di PowerShell.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Usare l'API Microsoft Graph

Creare un ruolo personalizzato usando l'esempio fornito nell'API Microsoft Graph. Per informazioni dettagliate, vedere [creare e assegnare un ruolo personalizzato](custom-create.md) e [assegnare ruoli di amministratore personalizzati usando l'API Microsoft Graph](custom-assign-graph.md).

Richiesta HTTP per creare il ruolo personalizzato.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Assegnare il ruolo personalizzato usando l'API Microsoft Graph

L'assegnazione di ruolo combina un ID dell'entità di sicurezza, che può essere un utente o un'entità servizio, un ID di definizione del ruolo e un ambito di risorsa Azure AD. Per ulteriori informazioni sugli elementi di un'assegnazione di ruolo, vedere [Cenni preliminari sui ruoli personalizzati](custom-overview.md)

Richiesta HTTP per assegnare un ruolo personalizzato.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le autorizzazioni dei ruoli personalizzati disponibili per le app aziendali](custom-enterprise-app-permissions.md)
