---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6ba48f3c40e45afa02e03a7589e968cca723118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249516"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

Configurare un'azione per inviare una notifica a una persona tramite posta elettronica o SMS, che riceve una conferma che indica che sono stati aggiunti al gruppo di azioni.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Nome**: Un identificatore univoco all'interno del gruppo di azioni.  
* **Tipo di azione**: L'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Dettagli**: I dettagli corrispondenti che variano in base al *tipo di azione*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Monitor .** Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in un'unica visualizzazione.

1. Selezionare **Avvisi** e quindi **Gestisci azioni**.

    ![Pulsante Gestisci azioni](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.png)
    
1. Immettere un nome nella casella **Nome gruppo di** azioni e un nome nella casella Nome **breve.** Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-define.png)

1. La casella **Abbonamento** viene riempita automaticamente con l'abbonamento corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

1. Selezionare il **gruppo di risorse** in cui viene salvato il gruppo di azioni.

1. Definire un elenco di azioni. Fornire quanto segue per ogni azione:Provide the following for each action:

    1. **Nome**: immettere un identificatore univoco per questa azione.

    1. **Tipo di azione**: selezionare Massaggio di posta elettronica/SMS/Push/Voce, App per la logica, Webhook, ITSM o Runbook di Automazione.

    1. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    1. **Schema di avviso comune:** è possibile scegliere di abilitare lo schema di [avviso comune,](https://aka.ms/commonAlertSchemaDocs)che offre il vantaggio di disporre di un unico payload di avviso estensibile e unificato in tutti i servizi di avviso in Monitoraggio di Azure.Common alert schema : You can choose to enable the common alert schema , which provides the advantage of having a single extensible and unified alert payload across all the alert services in Azure Monitor.

1. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azioni, è possibile visualizzare i gruppi di azioni selezionando Gestisci azioni nella pagina di destinazione Avvisi nel riquadro Monitoraggio.After you create an action **group,** you can view Action groups by selecting **Manage actions** from the **Alerts** landing page in **Monitor** pane. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Vedere [Limiti del servizio](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) di sottoscrizione per il monitoraggio per i limiti numerici in ognuno degli elementi seguenti.  

### <a name="automation-runbook"></a>Runbook di automazione
Fare riferimento [ai limiti](../../azure-resource-manager/management/azure-subscription-service-limits.md) del servizio di sottoscrizione di Azure per i limiti per i payload di Runbook.Refer to the Azure subscription service limits for limits on Runbook payloads.

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni Runbook. 

### <a name="azure-app-push-notifications"></a>Notifiche push dell'app AzureAzure app Push Notifications
È possibile avere un numero limitato di azioni dell'app Azure in un gruppo di azioni.

### <a name="email"></a>Email
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni di posta elettronica. Vedere l'articolo Informazioni [sulla limitazione delle tariffe.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Ruolo di Azure Resource Manager tramite posta elettronica
Inviare un messaggio di posta elettronica ai membri del ruolo della sottoscrizione. La posta elettronica verrà inviata solo ai membri utente di **Azure AD** del ruolo. La posta elettronica non verrà inviata a gruppi o entità servizio di Azure AD.

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni di posta elettronica. Vedere l'articolo Informazioni [sulla limitazione delle tariffe.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Funzione
Chiama un endpoint trigger HTTP esistente in Funzioni di [Azure.](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni Funzione.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../../azure-monitor/platform/itsmc-overview.md).

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni ITSM. 

### <a name="logic-app"></a>App per la logica
Si può avere un numero limitato di azioni App per la logica in un gruppo di azioni.

### <a name="secure-webhook"></a>Webhook sicuro
L'azione Webhook Gruppi di azioni consente di sfruttare Azure Active Directory per proteggere la connessione tra il gruppo di azioni e l'API Web protetta (endpoint webhook). Il flusso di lavoro complessivo per sfruttare questa funzionalità è descritto di seguito. Per una panoramica delle applicazioni di Azure AD e delle entità servizio, vedere Panoramica della piattaforma di [identità Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Creare un'applicazione Azure AD per l'API Web protetta. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare l'API protetta in modo che venga chiamata da un'app daemon.
    
1. Abilitare i gruppi di azioni per usare l'applicazione Azure AD.

    > [!NOTE]
    > Per eseguire questo script, è necessario essere membri del [ruolo Amministratore applicazioni di Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)
    
    - Modificare la chiamata Connect-AzureAD dello script di PowerShell per usare l'ID tenant di Azure AD.
    - Modificare la variabile dello script di PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione Azure ADModify the PowerShell script's script's variable $myAzureADApplicationObjectId to use the Object ID of your Azure AD Application
    - Eseguire lo script modificato.
    
1. Configurare l'azione Secure Webhook del gruppo di azioni.
    - Copiare il valore $myApp.ObjectId dallo script e immetterlo nel campo ID oggetto applicazione nella definizione dell'azione Webhook.
    
    ![Azione Secure Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script di PowerShell Webhook sicuro

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

### <a name="sms"></a>sms
Per ulteriori informazioni importanti, vedere le informazioni sulla [limitazione](./../../azure-monitor/platform/alerts-rate-limiting.md) della frequenza e il comportamento degli [avvisi SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni SMS.  

### <a name="voice"></a>Chiamata vocale
Vedere l'articolo Informazioni [sulla limitazione delle tariffe.](./../../azure-monitor/platform/alerts-rate-limiting.md)

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni vocali.

### <a name="webhook"></a>webhook
I webhook vengono ritentati utilizzando le regole seguenti. La chiamata webhook viene ritentata al massimo 2 volte quando vengono restituiti i seguenti codici di stato HTTP: 408, 429, 503, 504 o l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. la seconda dopo 100 secondi. Dopo due errori, nessun gruppo di azioni chiamerà l'endpoint per 30 minuti. 

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

Per ricevere aggiornamenti sulle modifiche apportate a questi indirizzi IP, è consigliabile configurare un avviso di integrità del servizio, che consente di monitorare le notifiche informative sul servizio Gruppi di azioni.

È possibile che in un gruppo di azioni sia presente un numero limitato di azioni Webhook.



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](../../azure-monitor/platform/alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
