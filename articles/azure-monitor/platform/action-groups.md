---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705264"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

Configurare un'azione per inviare una notifica tramite posta elettronica o SMS, una persona che ricevono un messaggio di conferma che indica che sono stati aggiunti al gruppo di azione.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Nome**: un identificatore univoco all'interno del gruppo di azioni.  
* **Tipo di azione**: L'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Dettagli**: I dettagli corrispondenti che variano in base al *tipo di azione*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Monitoraggio**. Il **Monitor** riquadro consolida tutte le impostazioni e i dati in un'unica visualizzazione monitoraggio.

    ![Servizio "Monitoraggio"](./media/action-groups/home-monitor.png)
    
1. Selezionare **Alerts** quindi selezionare **gestire azioni**.

    ![Pulsante di azioni di gestione](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.png)
    
1. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-define.png)

1. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

1. Selezionare il **gruppo di risorse** in cui verrà salvato il gruppo di azione.

1. Definire un elenco di azioni. Specificare le informazioni seguenti per ogni azione:

    1. **Nome**: immettere un identificatore univoco per questa azione.

    1. **Tipo di azione**: selezionare Messaggio di posta elettronica/SMS/Push/Voce, App per la logica, Webhook, ITSM o Runbook di Automazione.

    1. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    1. **Schema di avviso comune**: È possibile scegliere di abilitare la [common schema avviso](https://aka.ms/commonAlertSchemaDocs), che offre il vantaggio di una singola estensibile e di servizi di payload avviso unificato in tutto l'avviso in Monitoraggio di Azure.

1. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azioni, è visibile nel **gruppi di azioni** sezione il **monitoraggio** riquadro. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Visualizzare [limiti di servizio di sottoscrizione per il monitoraggio](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) per limiti numerici per ognuno dei seguenti elementi.  

### <a name="azure-app-push-notifications"></a>Notifiche Push dell'app di Azure
Potrebbe essere un numero limitato di azioni delle app di Azure in un gruppo di azioni.

### <a name="email"></a>Email
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Potrebbe essere un numero limitato di azioni di posta elettronica in un gruppo di azioni. Vedere le [informazioni sulla limitazione di frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) articolo.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../../azure-monitor/platform/itsmc-overview.md).

Potrebbe essere un numero limitato di azioni di gestione dei servizi IT in un gruppo di azioni. 

### <a name="logic-app"></a>App per la logica
Potrebbe essere un numero limitato di azioni delle App per la logica in un gruppo di azioni.

### <a name="function"></a>Funzione
I tasti funzione per le app di funzione configurate come azioni vengono lette attraverso l'API di funzioni, che attualmente richiede che le app di funzioni v2 per configurare l'impostazione "AzureWebJobsSecretStorageType" in "file" dell'app. Per altre informazioni, vedere [passa a gestione delle chiavi di funzioni V2]( https://aka.ms/funcsecrets).

Potrebbe essere un numero limitato di azioni di funzione in un gruppo di azioni.

### <a name="automation-runbook"></a>Runbook di automazione
Vedere le [limiti dei servizi di sottoscrizione di Azure](../../azure-subscription-service-limits.md) per i limiti nel payload di Runbook.

Potrebbe essere un numero limitato di azioni di Runbook in un gruppo di azioni. 

### <a name="sms"></a>sms
Vedere le [informazioni sulla limitazione di velocità](./../../azure-monitor/platform/alerts-rate-limiting.md) e [comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md) per ulteriori informazioni.

Potrebbe essere un numero limitato di azioni di SMS in un gruppo di azioni.  

### <a name="voice"></a>Chiamata vocale
Vedere le [informazioni sulla limitazione di frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) articolo.

Potrebbe essere un numero limitato di azioni vocale in un gruppo di azioni.

### <a name="webhook"></a>webhook
I Webhook vengono ritentati utilizzando le regole seguenti. La chiamata webhook viene ritentata un massimo di 2 volte quando i codici di stato HTTP seguenti vengono restituiti: 408, 429, 503, 504 o l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. la seconda dopo 100 secondi. Dopo due errori, nessun gruppo di azioni chiamerà l'endpoint per 30 minuti. 

Intervalli di indirizzi IP di origine
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Per ricevere aggiornamenti sulle modifiche apportate a questi indirizzi IP, è consigliabile che configurare un avviso di integrità del servizio, che consente di monitorare le notifiche informative sul servizio di gruppi di azioni.

Potrebbe essere un numero limitato di azioni Webhook in un gruppo di azioni.

#### <a name="secure-webhook"></a>Proteggere Webhook
**La funzionalità Secure Webhook è attualmente in anteprima.**

L'azione di Webhook di gruppi di azione consente di sfruttare i vantaggi di Azure Active Directory per proteggere la connessione tra il gruppo di azioni e protected web API (endpoint di webhook). Il flusso di lavoro generale per sfruttare questa funzionalità è descritta di seguito. Per una panoramica di Azure AD applicazioni ed entità servizio, vedere [Panoramica di Microsoft identity platform (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Creare un'applicazione Azure AD per l'API web protetta. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare le API protetta che verrà chiamata da un'app daemon.
    
1. Abilitare i gruppi di azione usare l'applicazione Azure AD.

    > [!NOTE]
    > È necessario essere un membro del [ruolo di amministratore dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) per eseguire lo script.
    
    - Modificare chiamata Connect-AzureAD dello script PowerShell per usare l'ID Tenant di Azure AD.
    - Modificare la variabile dello script PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione AD Azure
    - Eseguire lo script modificato.
    
1. Configurare l'azione di Webhook di gruppo di azione.
    - Copiare il valore $myApp.ObjectId dallo script e immetterla nel campo ID oggetto dell'applicazione nella definizione dell'azione di Webhook.
    
    ![Azione di Webhook protetto](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Proteggere lo Script di PowerShell di Webhook

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


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](../../azure-monitor/platform/alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
