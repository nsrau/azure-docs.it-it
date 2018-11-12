---
title: Estendere avvisi da Log Analytics ad Azure
description: In questo articolo vengono descritti gli strumenti e le API mediante i quali è possibile estendere avvisi da Log Analytics ad Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: ed6b2fafbb3329e20985b75f55d29b52dcc5da57
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415702"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Estendere avvisi da Log Analytics ad Avvisi di Azure
La funzionalità degli avvisi in Azure Log Analytics verrà sostituita da avvisi di Azure. Nel quadro della transizione, gli avvisi originariamente configurati in Log Analytics verranno estesi in Azure. Se non si desidera attendere che gli avvisi vengano spostati automaticamente in Azure, è possibile avviare il processo:

- Manualmente, dal portale di Operations Management Suite. 
- A livello di codice, tramite l'API AlertsVersion.  

> [!NOTE]
> Microsoft estenderà automaticamente gli avvisi creati nelle istanze del cloud pubblico in Log Analytics negli Avvisi di Azure a partire dal 14 maggio 2018, in serie ricorrenti fino al completamento. Se si riscontrano problemi con la creazione di [gruppi di azioni](monitoring-action-groups.md), adottare i [passaggi di correzione](monitoring-alerts-extend-tool.md#troubleshooting) seguenti per creare automaticamente i gruppi di azioni. È possibile applicare questi passaggi fino al 5 luglio 2018. *Non applicabile per gli utenti del cloud sovrano e di Azure per enti pubblici di Log Analytics*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opzione 1: procedere dal portale di Operations Management Suite
Nei passaggi seguenti viene descritto come estendere gli avvisi per l'area di lavoro dal portale di Operations Management Suite.  

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nel riquadro delle sottoscrizioni di Log Analytics selezionare un'area di lavoro e quindi selezionare il riquadro **Portale di OMS**.
![Schermata del riquadro di sottoscrizione di Log Analytics, con il riquadro del portale di OMS evidenziato](media/monitoring-alerts-extend-tool/azure-portal-01.png) 
3. Dopo il reindirizzamento al portale di Operations Management Suite, selezionare l'icona delle **Impostazioni**.
![Schermata del portale di Operations Management Suite, con l'icona Impostazioni evidenziata](media/monitoring-alerts-extend-tool/oms-portal-settings-option.png) 
4. Nella pagina **Impostazioni** selezionare **Avvisi**.  
5. Selezionare **Estendi in Azure**.
![Schermata della pagina Impostazioni sul portale di Operations Management Suite, con Estendi in Azure evidenziato](media/monitoring-alerts-extend-tool/ExtendInto.png)
6. Nel riquadro **Avvisi** verrà visualizzata una procedura guidata in tre passaggi. Leggere la panoramica e selezionare **Avanti**.
![Schermata del passaggio 1 della procedura guidata](media/monitoring-alerts-extend-tool/ExtendStep1.png)  
7. Nel secondo passaggio viene visualizzato un riepilogo delle modifiche proposte, che elenca i [gruppi di azioni](monitoring-action-groups.md) appropriati per gli avvisi. Se in più avvisi sono presenti azioni simili, la procedura guidata propone di associare a tutte un singolo gruppo di azioni.  La convenzione di denominazione è la seguente: *WorkspaceName_AG_#Number*. Per continuare, selezionare **Avanti**.
![Schermata del passaggio 2 della procedura guidata](media/monitoring-alerts-extend-tool/ExtendStep2.png)  
8. Nell'ultimo passaggio della procedura guidata, selezionare **Fine**e confermare quando viene richiesto di avviare il processo. Facoltativamente, è possibile fornire un indirizzo di posta elettronica, in modo da ricevere una notifica quando il processo è terminato e tutti gli avvisi sono stati spostati in Avvisi di Azure.
![Schermata del passaggio 3 della procedura guidata](media/monitoring-alerts-extend-tool/ExtendStep3.png)

Quando la procedura guidata è terminata, sulla pagina **Impostazioni avvisi** la possibilità di estendere gli avvisi in Azure viene rimossa. In background, gli avvisi vengono spostati in Azure. Tale operazione può richiedere alcuni minuti. Durante l'operazione, è possibile apportare modifiche agli avvisi dal portale di Operations Management Suite. È possibile visualizzare lo stato corrente dallo striscione nella parte superiore del portale. Se è stato specificato un indirizzo di posta elettronica in precedenza, una volta completato il processo verrà visualizzato un messaggio di posta elettronica.  


Gli avvisi continueranno a essere elencati nel portale di Operations Management Suite, anche dopo essere stati spostati correttamente in Azure.
![Schermata della pagina Impostazioni avvisi sul portale di Operations Management Suite](media/monitoring-alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opzione 2: usare l'API AlertsVersion
È possibile usare l'API AlertsVersion di Log Analytics per estendere avvisi da Log Analytics ad Avvisi di Azure da qualsiasi client in grado di chiamare un'API REST. È possibile accedere all'API da PowerShell tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source. È possibile visualizzare i risultati in JSON.  

Per usare l'API, è innanzitutto necessario creare una richiesta GET. Questa valuta e restituisce un riepilogo delle modifiche proposte, prima di tentare l'estensione effettiva in Azure tramite una richiesta POST. L'elenco risultati riporta gli avvisi e un elenco proposto di [gruppi di azioni](monitoring-action-groups.md), in formato JSON. Se in più avvisi sono presenti azioni simili, il servizio propone di associare a tutte un singolo gruppo di azioni. La convenzione di denominazione è la seguente: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se la richiesta GET ha esito positivo, viene restituito un codice di stato HTTP 200, assieme a un elenco di avvisi e gruppi di azioni proposti nei dati JSON. Di seguito è riportata una risposta di esempio:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Se l'area di lavoro specificata non dispone di regole di avviso definite, i dati JSON restituiscono quanto segue:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Se tutte le regole di avviso nell'area di lavoro specificata sono già state estese ad Azure, la risposta alla richiesta GET sarà:

```json
{
    "version": 2
}
```

Per avviare la migrazione degli avvisi in Azure, avviare una risposta POST. La risposta POST conferma la finalità e l'accettazione dell'estensione degli avvisi da Log Analytics ad Avvisi di Azure. L'attività è pianificata e gli avvisi vengono elaborati come indicato, in base ai risultati, quando la risposta GET è stata eseguita in precedenza. È possibile fornire facoltativamente un elenco di indirizzi di posta elettronica a cui Log Analytics invierà un report al termine del processo in background di migrazione degli avvisi. È possibile usare la richiesta di esempio seguente:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Il risultato della migrazione degli avvisi in Avvisi di Azure può variare a seconda del riepilogo fornito dalla risposta GET. Se pianificato, gli avvisi in Log Analytics sono temporaneamente disattivati per la modifica nel portale di Operations Management Suite. Tuttavia, è possibile creare nuovi avvisi. 

Se la richiesta POST ha esito positivo, restituisce uno stato HTTP 200 OK, assieme alla risposta seguente:

```json
{
    "version": 2
}
```

Questa risposta indica che gli avvisi sono stati estesi in Avvisi di Azure. Questa proprietà Version ha il solo scopo di verificare se gli avvisi sono stati estesi ad Azure e non ha effetto sull'[API di ricerca di Log Analytics](../log-analytics/log-analytics-api-alerts.md). Una volta estesi correttamente gli avvisi in Azure, viene inviato un report agli eventuali indirizzi di posta elettronica forniti con la richiesta POST. Se tutti gli avvisi nell'area di lavoro specificata sono già stati impostati per l'estensione, la risposta alla richiesta POST sarà di accesso negato (codice di stato 403). Per visualizzare eventuali messaggi di errore o capire se il processo è bloccato, è possibile inviare una richiesta GET. Se è presente un messaggio di errore, questo viene restituito assieme ai dati di riepilogo.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>Opzione 3: usare uno script PowerShell personalizzato
 Se Microsoft non è riuscita a estendere gli avvisi dal portale di Operations Management Suite in Azure, è possibile eseguire questa operazione manualmente fino al 5 luglio 2018. Le due opzioni per l'estensione manuale vengono trattate nelle due sezioni precedenti.

Dopo il 5 luglio 2018, tutti gli avvisi verranno estesi dal portale di Operations Management Suite in Azure. Gli avvisi degli utenti che non hanno eseguito la [procedura di correzione necessaria suggerita](#troubleshooting) saranno eseguiti senza la generazione di azioni o notifiche, a causa della mancanza di [gruppi di azioni](monitoring-action-groups.md) associati. 

Per creare manualmente [gruppi di azioni](monitoring-action-groups.md) per gli avvisi in Log Analytics, usare lo script di esempio seguente:
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>Informazioni sullo script PowerShell personalizzato 
Di seguito sono riportate importanti informazioni sull'uso dello script:
- Un prerequisito è rappresentato dall'installazione di [ARMclient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager.
- Per eseguire lo script, è necessario disporre di un ruolo di proprietario o collaboratore nella sottoscrizione di Azure.
- È necessario specificare i seguenti parametri:
    - $subscriptionId: ID sottoscrizione di Azure associato con l'area di lavoro di Log Analytics di Operations Management Suite.
    - $resourceGroup: gruppo di risorse di Azure per l'area di lavoro di Log Analytics di Operations Management Suite.
    - $workspaceName: nome dell'area di lavoro di Log Analytics di Operations Management Suite.

### <a name="output-of-the-custom-powershell-script"></a>Output dello script PowerShell personalizzato
Lo script è dettagliato e restituisce i passaggi durante l'esecuzione: 
- Visualizza il riepilogo, contenente informazioni sugli avvisi di Log Analytics di Operations Management Suite esistenti nell'area di lavoro. Il riepilogo contiene anche informazioni sui gruppi di azioni di Azure da creare per le azioni associate. 
- Viene chiesto di procedere con l'estensione o uscire dopo la visualizzazione del riepilogo.
- Se si procede con l'estensione, vengono creati nuovi gruppi di azioni di Azure e tutti gli avvisi esistenti sono associati ad essi. 
- Lo script viene chiuso visualizzando il messaggio "Estensione completata". In caso di eventuali errori intermedi, lo script visualizza gli errori successivi.

## <a name="troubleshooting"></a>Risoluzione dei problemi 
Durante il processo di estensione degli avvisi, alcuni problemi possono impedire al sistema di creare i [gruppi di azioni](monitoring-action-groups.md) necessari. In questi casi, viene visualizzato un messaggio di errore in uno striscione nella sezione **Avviso** del portale di Operations Management Suite o nella chiamata GET effettuata per l'API.

> [!IMPORTANT]
> Se gli utenti di Log Analyticsbase basato su cloud pubblico non eseguono la procedura di correzione seguente prima del 5 luglio 2018, gli avvisi verranno eseguito in Azure, ma non genereranno alcuna azione o notifica. Per ottenere le notifiche degli avvisi è necessario apportare una modifica manuale aggiungendo [gruppi di azioni](monitoring-action-groups.md), oppure usare il precedente [script PowerShell personalizzato](#option-3---using-custom-powershell-script).

Di seguito viene elencata la procedura di correzione per ogni errore:
- **Error: Scope Lock is present at subscription/resource group level for write operations** (Errore: a livello di sottoscrizione/gruppo di risorse è presente un blocco dell'ambito per le operazioni di scrittura): ![Schermata della pagina Impostazioni avvisi del portale di Operations Management Suite con messaggio di errore di blocco dell'ambito evidenziato](media/monitoring-alerts-extend-tool/ErrorScopeLock.png)

    Quando l'ambito del blocco è abilitato, la funzionalità limita qualsiasi nuova modifica alla sottoscrizione o al gruppo di risorse contenenti l'area di lavoro di Log Analytics (Operations Management Suite). Il sistema non è in grado di estendere gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    Per risolvere questo problema, eliminare il blocco *ReadOnly* nella sottoscrizione o nel gruppo di risorse contenenti l'area di lavoro. Questa operazione può essere eseguita nel portale di Azure oppure tramite PowerShell, l'interfaccia della riga di comando di Azure o l'API. Per altre informazioni, vedere l'[uso del Blocco risorsa](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Quando si risolve l'errore tramite la procedura illustrata nell'articolo, gli avvisi in Operations Management Suite vengono estesi ad Azure nel quadro dell'esecuzione pianificata per il giorno successivo. Non è necessario intraprendere alcun'altra azione né avviare alcuna operazione.

- **Error: Policy is present at subscription/resource group level** (Errore: il criterio è presente a livello di sottoscrizione/gruppo di risorse): ![Schermata della pagina Impostazioni avvisi del portale di Operations Management Suite con messaggio di errore di criterio evidenziato](media/monitoring-alerts-extend-tool/ErrorPolicy.png)

    Quando [Criteri di Azure](../azure-policy/azure-policy-introduction.md) è abilitato, la funzionalità limita qualsiasi nuova risorsa in una sottoscrizione o in un gruppo di risorse contenenti l'area di lavoro di Log Analytics (Operations Management Suite). Il sistema non è in grado di estendere gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    Per risolvere questo problema, modificare il criterio che causa l'errore *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*  che impedisce la creazione di nuove risorse nella sottoscrizione o nel gruppo di risorse che contiene l'area di lavoro. Questa operazione può essere eseguita nel portale di Azure oppure tramite PowerShell, l'interfaccia della riga di comando di Azure o l'API. È possibile controllare le azioni per trovare i criteri che causano l'errore. Per altre informazioni, vedere la [visualizzazione dei log attività per il controllo delle azioni](../azure-resource-manager/resource-group-audit.md). 
    
    Quando si risolve l'errore tramite la procedura illustrata nell'articolo, gli avvisi in Operations Management Suite vengono estesi ad Azure nel quadro dell'esecuzione pianificata per il giorno successivo. Non è necessario intraprendere alcun'altra azione né avviare alcuna operazione.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza degli avvisi di Azure](monitoring-overview-unified-alerts.md).
* Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
