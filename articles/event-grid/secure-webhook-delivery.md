---
title: Distribuzione sicura dei webhook con Azure AD in griglia di eventi di Azure
description: Viene descritto come recapitare gli eventi agli endpoint HTTPS protetti da Azure Active Directory tramite griglia di eventi di Azure
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 0320e78e6b436f6ba1c0a6ca1bfec81eb974e106
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812200"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Pubblicare eventi per endpoint protetti di Azure Active Directory

Questo articolo descrive come sfruttare i vantaggi di Azure Active Directory per proteggere la connessione tra la sottoscrizione di eventi e l'endpoint del webhook. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Questo articolo usa la portale di Azure per la dimostrazione, tuttavia la funzionalità può anche essere abilitata usando l'interfaccia della riga di comando, PowerShell o gli SDK.


## <a name="create-an-azure-ad-application"></a>Creare un'applicazione Azure AD

Iniziare creando un'applicazione Azure AD per l'endpoint protetto. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare l'API protetta affinché venga chiamata da un'app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Abilitare griglia di eventi per l'uso dell'applicazione Azure AD
Questa sezione illustra come abilitare griglia di eventi per l'uso dell'applicazione Azure AD. 

> [!NOTE]
> Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles).

### <a name="connect-to-your-azure-tenant"></a>Connettersi al tenant di Azure
Per prima cosa, connettersi al tenant di Azure usando il `Connect-AzureAD` comando. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Creare un'entità servizio Microsoft. EventGrid
Eseguire lo script seguente per creare l'entità servizio per **Microsoft. EventGrid** , se non esiste già. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Creazione di un ruolo per l'applicazione   
Eseguire lo script seguente per creare un ruolo per l'applicazione Azure AD. In questo esempio, il nome del ruolo è: **AzureEventGridSecureWebhook**. Modificare gli script di PowerShell `$myTenantId` in modo da usare l'ID Tenant Azure ad e `$myAzureADApplicationObjectId` con l'ID oggetto dell'applicazione Azure ad

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Aggiungere un'entità servizio griglia di eventi al ruolo    
Eseguire ora il `New-AzureADServiceAppRoleAssignment` comando per assegnare l'entità servizio griglia di eventi al ruolo creato nel passaggio precedente. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Eseguire i comandi seguenti per restituire le informazioni che si utilizzeranno nei passaggi successivi. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Configurare la sottoscrizione di eventi

Nel flusso di creazione per la sottoscrizione di eventi selezionare il tipo di endpoint ' Web Hook '. Una volta assegnato l'URI dell'endpoint, fare clic sulla scheda funzionalità aggiuntive nella parte superiore del pannello crea sottoscrizioni di eventi.

![Selezionare il webhook del tipo di endpoint](./media/secure-webhook-delivery/select-webhook.png)

Nella scheda funzionalità aggiuntive selezionare la casella ' Usa autenticazione AAD ' e configurare l'ID tenant e l'ID applicazione:

* Copiare l'ID tenant Azure AD dall'output dello script e immetterlo nel campo ID tenant di AAD.
* Copiare l'ID dell'applicazione Azure AD dall'output dello script e immetterlo nel campo dell'ID applicazione di AAD.

    ![Usare l'azione Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
