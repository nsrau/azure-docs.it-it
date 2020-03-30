---
title: Recapito WebHook sicuro con Azure AD in Griglia di eventi di AzureSecure WebHook delivery with Azure AD in Azure Event Grid
description: Descrive come recapitare eventi agli endpoint HTTPS protetti da Azure Active Directory tramite Griglia di eventi di AzureDescribes how to deliver events to HTTPS endpoints protected by Azure Active Directory using Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931547"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Pubblicare eventi negli endpoint protetti di Azure Active DirectoryPublish events to Azure Active Directory protected endpoints

In questo articolo viene descritto come sfruttare Azure Active Directory per proteggere la connessione tra la sottoscrizione di eventi e l'endpoint webhook. Per una panoramica delle applicazioni di Azure AD e delle entità servizio, vedere Panoramica della piattaforma di [identità Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

Questo articolo usa il portale di Azure per la dimostrazione, tuttavia la funzionalità può essere abilitata anche tramite CLI, PowerShell o gli SDK.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Creare un'applicazione Azure ADCreate an Azure AD Application

Iniziare creando un'applicazione Azure AD per l'endpoint protetto. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare l'API protetta in modo che venga chiamata da un'app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Abilitare Griglia di eventi per usare l'applicazione Azure ADEnable Event Grid to use your Azure AD Application

Usare lo script di PowerShell riportato di seguito per creare un principio di ruolo e servizio nell'applicazione Azure AD. Sono necessari l'ID tenant e l'ID oggetto dall'applicazione Azure AD:You will need the Tenant ID and Object ID from your Azure AD Application:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Modificare le $myTenantId dello script di PowerShell per usare l'ID tenant di Azure AD.
1. Modificare le $myAzureADApplicationObjectId dello script di PowerShell per usare l'ID oggetto dell'applicazione Azure ADModify the PowerShell script's $myAzureADApplicationObjectId to use the Object ID of your Azure AD Application
1. Eseguire lo script modificato.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Configurare la sottoscrizione di eventi

Nel flusso di creazione per la sottoscrizione di eventi selezionare il tipo di endpoint 'Web Hook'. Dopo aver specificato l'URI dell'endpoint, fare clic sulla scheda Funzionalità aggiuntive nella parte superiore del pannello Crea sottoscrizioni eventi.

![Selezionare il tipo di endpoint webhook](./media/secure-webhook-delivery/select-webhook.png)

Nella scheda funzionalità aggiuntive, selezionare la casella 'Usa autenticazione AAD' e configurare l'ID tenant e l'ID applicazione:

* Copiare l'ID tenant di Azure AD dall'output dello script e immetterlo nel campo AAD Tenant ID.
* Copiare l'ID applicazione di Azure AD dall'output dello script e immetterlo nel campo ID applicazione AAD.

    ![Azione Secure Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
