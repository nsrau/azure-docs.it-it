---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 8075574556375b7c07de2abd6c5aff792880b497
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738819"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

Quando viene configurata un'azione per l'invio di una notifica a un utente tramite posta elettronica o SMS, l'utente riceverà un messaggio di conferma che indica che è stato aggiunto al gruppo di azioni.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Name**: un identificatore univoco all'interno del gruppo di azioni.  
* **Tipo di azione**: L'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Dettagli**: i corrispondenti dettagli che variano in base al *tipo di azione*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.

1. Selezionare **Avvisi** e quindi **Gestisci azioni**.

    ![Pulsante Gestisci azioni](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.png)
    
1. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-define.png)

1. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

1. Selezionare il **gruppo di risorse** in cui verrà salvato il gruppo di azione.

1. Definire un elenco di azioni. Per ogni azione, specificare quanto segue:

    1. **Name**: immettere un identificatore univoco per questa azione.

    1. **Tipo di azione**: selezionare Runbook di automazione, Funzione di Azure, Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager, Posta elettronica/SMS/Push/Voce, Gestione dei servizi IT, App per la logica, Webhook protetto, Webhook.

    1. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    1. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](https://aka.ms/commonAlertSchemaDocs), che fornisce il vantaggio di avere un singolo payload degli avvisi estendibile e unificato per tutti i servizi di avviso in Monitoraggio di Azure.

1. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azioni, è possibile visualizzare **Gruppi di azioni** selezionando **Gestisci azioni** dalla pagina di destinazione **Avvisi** nel riquadro **Monitoraggio**. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Vedere [Limiti del servizio di sottoscrizione per il monitoraggio](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) per i limiti numerici per ognuno degli elementi seguenti.  

### <a name="automation-runbook"></a>Runbook di Automazione
Vedere i [Limiti del servizio per la sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sui payload di Runbook.

Un Gruppo di azioni può contenere un numero limitato di azioni di tipo Runbook. 

### <a name="azure-app-push-notifications"></a>Notifiche push dell'app Azure
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app Azure.

### <a name="email"></a>Email
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager
Inviare il messaggio di posta elettronica ai membri del ruolo della sottoscrizione. Il messaggio di posta elettronica verrà inviato solo ai membri **utente di Azure AD** del ruolo. Il messaggio di posta elettronica non verrà inviato a gruppi di Azure AD o entità servizio.

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md).

### <a name="function"></a>Funzione
Chiama un endpoint di trigger HTTP esistente in [Funzioni di Azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Un Gruppo di azioni può contenere un numero limitato di funzioni.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../../azure-monitor/platform/itsmc-overview.md).

Un Gruppo di azioni può contenere un numero limitato di azioni di gestione dei servizi IT. 

### <a name="logic-app"></a>App per la logica
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app per la logica.

### <a name="secure-webhook"></a>Webhook protetto
L'azione Webhook dei gruppi di azione consente di sfruttare Azure Active Directory per proteggere la connessione tra il gruppo di azioni e l'API Web protetta (endpoint webhook). Di seguito è descritto il flusso di lavoro generale per sfruttare questa funzionalità. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Creare un'applicazione Azure AD per l'API Web protetta. Vedere https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configurare l'API protetta affinché venga chiamata da un'app daemon.
    
1. Abilitare i gruppi di azioni per l'uso dell'applicazione Azure AD.

    > [!NOTE]
    > Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles).
    
    - Modificare la chiamata a Connect-AzureAD dello script di PowerShell per usare l'ID tenant di Azure AD.
    - Modificare la variabile dello script di PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione Azure AD
    - Eseguire lo script modificato.
    
1. Configurare l'azione del Webhook protetto del Gruppo di azioni.
    - Copiare il valore $myApp.ObjectId dallo script e immetterlo nel campo ID oggetto applicazione nella definizione dell'azione Webhook.
    
    ![Usare l'azione Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script di PowerShell per il Webhook protetto

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
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md) e sul [comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md). 

Un Gruppo di azioni può contenere un numero limitato di azioni SMS.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, il servizio SMS non è supportato per il paese/regione.  Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di SMS di terze parti con supporto per il paese/regione.  

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Chiamata vocale
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./../../azure-monitor/platform/alerts-rate-limiting.md).

Un Gruppo di azioni può contenere un numero limitato di azioni Voce.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, le chiamate vocali non sono supportate per il paese/regione. Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di chiamate vocali di terze parti con supporto per il paese/regione.  

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook
I webhook vengono ripetuti usando le regole seguenti. Viene eseguito un massimo di 2 nuovi tentativi di chiamata webhook quando vengono restituiti i codici di stato HTTP seguenti: 408, 429, 503, 504 o l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. la seconda dopo 100 secondi. Dopo due errori, nessun gruppo di azioni chiamerà l'endpoint per 30 minuti. 

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

Per ricevere aggiornamenti sulle modifiche apportate a questi indirizzi IP, è consigliabile configurare un avviso di Integrità dei servizi che consente di monitorare l'eventuale presenza di notifiche informative sul servizio Gruppi di azioni.

Un Gruppo di azioni può contenere un numero limitato di azioni di tipo Webhook.



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](../../azure-monitor/platform/alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
