---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6b3d1ff76d4f7611da8e08dd4ce42293c805978e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423857"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

You configure an action to notify a person by email or SMS, they receive a confirmation indicating they have been added to the action group.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Nome:** un identificatore univoco all'interno del gruppo di azione.  
* **Action type**: The action performed. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Details**: The corresponding details that vary by *action type*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. In the [Azure portal](https://portal.azure.com), search for and select **Monitor**. The **Monitor** pane consolidates all your monitoring settings and data in one view.

1. Selezionare **Avvisi** e quindi **Gestisci azioni**.

    ![Manage Actions button](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.png)
    
1. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-define.png)

1. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

1. Selezionare il **gruppo di risorse** in cui verrà salvato il gruppo di azione.

1. Define a list of actions. Provide the following for each action:

    1. **Nome**: immettere un identificatore univoco per questa azione.

    1. **Tipo di azione**: selezionare Massaggio di posta elettronica/SMS/Push/Voce, App per la logica, Webhook, ITSM o Runbook di Automazione.

    1. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    1. **Common alert schema**: You can choose to enable the [common alert schema](https://aka.ms/commonAlertSchemaDocs), which provides the advantage of having a single extensible and unified alert payload across all the alert services in Azure Monitor.

1. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

After you create an action group, it's visible in the **Action groups** section of the **Monitor** pane. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> See [Subscription Service Limits for Monitoring](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) for numeric limits on each of the items below.  

### <a name="automation-runbook"></a>Automation Runbook
Refer to the [Azure subscription service limits](../../azure-subscription-service-limits.md) for limits on Runbook payloads.

You may have a limited number of Runbook actions in an Action Group. 

### <a name="azure-app-push-notifications"></a>Azure app Push Notifications
You may have a limited number of Azure app actions in an Action Group.

### <a name="email"></a>Indirizzo di posta elettronica
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="email-azure-resource-manager-role"></a>Email Azure Resource Manager Role
Send email to the members of the subscription's role.

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="function"></a>Funzione
The function keys for Function Apps configured as actions are read through the Functions API, which currently requires v2 function apps to configure the app setting “AzureWebJobsSecretStorageType” to “files”. For more information, see [Changes to Key Management in Functions V2]( https://aka.ms/funcsecrets).

You may have a limited number of Function actions in an Action Group.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../../azure-monitor/platform/itsmc-overview.md).

You may have a limited number of ITSM actions in an Action Group. 

### <a name="logic-app"></a>App per la logica
You may have a limited number of Logic App actions in an Action Group.

### <a name="secure-webhook"></a>Secure Webhook
**The Secure Webhook functionality is currently in Preview.**

The Action Groups Webhook action enables you to take advantage of Azure Active Directory to secure the connection between your action group and your protected web API (webhook endpoint). The overall workflow for taking advantage of this functionality is described below. For an overview of Azure AD Applications and service principals, see [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Create an Azure AD Application for your protected web API. Vedi https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure your protected API to be called by a daemon app.
    
1. Enable Action Groups to use your Azure AD Application.

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
    - Modify the PowerShell script's Connect-AzureAD call to use your Azure AD Tenant ID.
    - Modify the PowerShell script's variable $myAzureADApplicationObjectId to use the Object ID of your Azure AD Application
    - Run the modified script.
    
1. Configure the Action Group Secure Webhook action.
    - Copy the value $myApp.ObjectId from the script and enter it in the Application Object ID field in the Webhook action definition.
    
    ![Secure Webhook action](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure Webhook PowerShell Script

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) and [SMS alert behavior](../../azure-monitor/platform/alerts-sms-behavior.md) for additional important information.

You may have a limited number of SMS actions in an Action Group.  

### <a name="voice"></a>Voce
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

You may have a limited number of Voice actions in an Action Group.

### <a name="webhook"></a>webhook
Webhooks are retried using the following rules. The webhook call is retried a maximum of 2 times when the following HTTP status codes are returned: 408, 429, 503, 504 or the HTTP endpoint does not respond. La prima ripetizione del tentativo avviene dopo 10 secondi. la seconda dopo 100 secondi. After two failures, no action group will call the endpoint for 30 minutes. 

Intervalli di indirizzi IP di origine
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

To receive updates about changes to these IP addresses, we recommend you configure a Service Health alert, which monitors for Informational notifications about the Action Groups service.

You may have a limited number of Webhook actions in an Action Group.



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](../../azure-monitor/platform/alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
