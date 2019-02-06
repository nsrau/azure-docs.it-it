---
title: Estendere avvisi da Log Analytics al cloud di Azure per enti pubblici
description: In questo articolo vengono descritti gli strumenti e le API mediante i quali è possibile estendere avvisi da Log Analytics ad Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103378"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Estendere avvisi da Log Analytics ad Avvisi di Azure
La funzionalità degli avvisi nel portale di OMS verrà sostituita Avvisi di Azure nel cloud di Azure per enti pubblici. Nel quadro della transizione, gli avvisi originariamente configurati in Log Analytics verranno estesi in Azure. Se non si desidera attendere che gli avvisi vengano spostati automaticamente in Azure, è possibile avviare il processo:

- Manualmente, dal portale di Operations Management Suite. 
- A livello di codice, tramite l'API AlertsVersion.  

> [!NOTE]
> Microsoft estenderà automaticamente gli avvisi creati nelle istanze di Log Analytics del portale di OMS per Azure per enti pubblici ad Avvisi di Azure a partire dall'1 marzo 2019, in modo sistematico. Se si riscontrano problemi con la creazione di [gruppi di azioni](../../azure-monitor/platform/action-groups.md), adottare i [passaggi di correzione](alerts-extend-tool.md#troubleshooting) seguenti per creare automaticamente i gruppi di azioni. È possibile usare questa procedura fino al 15 marzo 2019 nel portale di OMS per Azure per enti pubblici.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opzione 1: procedere dal portale di Operations Management Suite
Nei passaggi seguenti viene descritto come estendere gli avvisi per l'area di lavoro dal portale di Operations Management Suite per il cloud di Azure per enti pubblici.  

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nel riquadro delle sottoscrizioni di Log Analytics selezionare un'area di lavoro e quindi selezionare il riquadro **Portale di OMS**.
![Schermata del riquadro di sottoscrizione di Log Analytics, con il riquadro del portale di OMS evidenziato](media/alerts-extend-tool/azure-portal-01.png) 
3. Dopo il reindirizzamento al portale di Operations Management Suite, selezionare l'icona delle **Impostazioni**.
![Schermata del portale di Operations Management Suite, con l'icona Impostazioni evidenziata](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Nella pagina **Impostazioni** selezionare **Avvisi**.  
5. Selezionare **Estendi in Azure**.
![Schermata della pagina Impostazioni sul portale di Operations Management Suite, con Estendi in Azure evidenziato](media/alerts-extend-tool/ExtendInto.png)
6. Nel riquadro **Avvisi** verrà visualizzata una procedura guidata in tre passaggi. Leggere la panoramica e selezionare **Avanti**.
![Schermata del passaggio 1 della procedura guidata](media/alerts-extend-tool/ExtendStep1.png)  
7. Nel secondo passaggio viene visualizzato un riepilogo delle modifiche proposte, che elenca i [gruppi di azioni](../../azure-monitor/platform/action-groups.md) appropriati per gli avvisi. Se in più avvisi sono presenti azioni simili, la procedura guidata propone di associare a tutte un singolo gruppo di azioni.  La convenzione di denominazione è la seguente: *WorkspaceName_AG_#Number*. Per continuare, selezionare **Avanti**.
![Schermata del passaggio 2 della procedura guidata](media/alerts-extend-tool/ExtendStep2.png)  
8. Nell'ultimo passaggio della procedura guidata, selezionare **Fine**e confermare quando viene richiesto di avviare il processo. Facoltativamente, è possibile fornire un indirizzo di posta elettronica, in modo da ricevere una notifica quando il processo è terminato e tutti gli avvisi sono stati spostati in Avvisi di Azure.
![Schermata del passaggio 3 della procedura guidata](media/alerts-extend-tool/ExtendStep3.png)

Quando la procedura guidata è terminata, sulla pagina **Impostazioni avvisi** la possibilità di estendere gli avvisi in Azure viene rimossa. In background, gli avvisi vengono spostati in Azure. Tale operazione può richiedere alcuni minuti. Durante l'operazione, è possibile apportare modifiche agli avvisi dal portale di Operations Management Suite. È possibile visualizzare lo stato corrente dallo striscione nella parte superiore del portale. Se è stato specificato un indirizzo di posta elettronica in precedenza, una volta completato il processo verrà visualizzato un messaggio di posta elettronica.  


Gli avvisi continueranno a essere elencati nel portale di Operations Management Suite, anche dopo essere stati spostati correttamente in Azure.
![Schermata della pagina Impostazioni avvisi sul portale di Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opzione 2: usare l'API AlertsVersion
È possibile usare l'API AlertsVersion di Log Analytics per estendere avvisi da Log Analytics ad Avvisi di Azure da qualsiasi client in grado di chiamare un'API REST. È possibile accedere all'API da PowerShell tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source. È possibile visualizzare i risultati in JSON.  

Per usare l'API, è innanzitutto necessario creare una richiesta GET. Questa valuta e restituisce un riepilogo delle modifiche proposte, prima di tentare l'estensione effettiva in Azure tramite una richiesta POST. L'elenco risultati riporta gli avvisi e un elenco proposto di [gruppi di azioni](../../azure-monitor/platform/action-groups.md), in formato JSON. Se in più avvisi sono presenti azioni simili, il servizio propone di associare a tutte un singolo gruppo di azioni. La convenzione di denominazione è la seguente: *WorkspaceName_AG_#Number*.

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
                            "serviceUri": "https://test.com"
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

Questa risposta indica che gli avvisi sono stati estesi in Avvisi di Azure. Questa proprietà Version ha il solo scopo di verificare se gli avvisi sono stati estesi ad Azure e non ha effetto sull'[API di ricerca di Log Analytics](../../azure-monitor/platform/api-alerts.md). Una volta estesi correttamente gli avvisi in Azure, viene inviato un report agli eventuali indirizzi di posta elettronica forniti con la richiesta POST. Se tutti gli avvisi nell'area di lavoro specificata sono già stati impostati per l'estensione, la risposta alla richiesta POST sarà di accesso negato (codice di stato 403). Per visualizzare eventuali messaggi di errore o capire se il processo è bloccato, è possibile inviare una richiesta GET. Se è presente un messaggio di errore, questo viene restituito assieme ai dati di riepilogo.

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
                            "serviceUri": "https://test.com"
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

## <a name="troubleshooting"></a>risoluzione dei problemi 
Durante il processo di estensione degli avvisi, alcuni problemi possono impedire al sistema di creare i [gruppi di azioni](../../azure-monitor/platform/action-groups.md) necessari. In questi casi, viene visualizzato un messaggio di errore in uno striscione nella sezione **Avviso** del portale di Operations Management Suite o nella chiamata GET effettuata per l'API.

> [!IMPORTANT]
> Se gli utenti del portale di OMS basato sul cloud di Azure per enti pubblici non eseguono la procedura di correzione seguente prima del 15 marzo 2019, gli avvisi verranno eseguiti in Azure, ma non genereranno alcuna azione o notifica. Per ottenere le notifiche degli avvisi, è necessario modificare manualmente le regole di avviso in Azure e aggiungere i [gruppi di azioni](../../azure-monitor/platform/action-groups.md)

Di seguito viene elencata la procedura di correzione per ogni errore:
- **Errore: Scope Lock is present at subscription/resource group level for write operations** (a livello di sottoscrizione/gruppo di risorse è presente un blocco dell'ambito per le operazioni di scrittura):   ![Schermata della pagina Impostazioni avvisi del portale di Operations Management Suite, con messaggio di errore di blocco dell'ambito evidenziato](media/alerts-extend-tool/ErrorScopeLock.png)

    Quando l'ambito del blocco è abilitato, la funzionalità limita qualsiasi nuova modifica alla sottoscrizione o al gruppo di risorse contenenti l'area di lavoro di Log Analytics (Operations Management Suite). Il sistema non è in grado di estendere gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    Per risolvere questo problema, eliminare il blocco *ReadOnly* nella sottoscrizione o nel gruppo di risorse contenenti l'area di lavoro. Questa operazione può essere eseguita nel portale di Azure oppure tramite PowerShell, l'interfaccia della riga di comando di Azure o l'API. Per altre informazioni, vedere l'[uso del Blocco risorsa](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Quando si risolve l'errore tramite la procedura illustrata nell'articolo, gli avvisi in Operations Management Suite vengono estesi ad Azure nel quadro dell'esecuzione pianificata per il giorno successivo. Non è necessario intraprendere alcun'altra azione né avviare alcuna operazione.

- **Errore: Policy is present at subscription/resource group level** (a livello di sottoscrizione/gruppo di risorse è presente un criterio):   ![Schermata della pagina Impostazioni avvisi del portale di Operations Management Suite, con messaggio di errore di criterio evidenziato](media/alerts-extend-tool/ErrorPolicy.png)

    Quando [Criteri di Azure](../../governance/policy/overview.md) è abilitato, la funzionalità limita qualsiasi nuova risorsa in una sottoscrizione o in un gruppo di risorse contenenti l'area di lavoro di Log Analytics (Operations Management Suite). Il sistema non è in grado di estendere gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    Per risolvere questo problema, modificare il criterio che causa l'errore *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*  che impedisce la creazione di nuove risorse nella sottoscrizione o nel gruppo di risorse che contiene l'area di lavoro. Questa operazione può essere eseguita nel portale di Azure oppure tramite PowerShell, l'interfaccia della riga di comando di Azure o l'API. È possibile controllare le azioni per trovare i criteri che causano l'errore. Per altre informazioni, vedere la [visualizzazione dei log attività per il controllo delle azioni](../../azure-resource-manager/resource-group-audit.md). 
    
    Quando si risolve l'errore tramite la procedura illustrata nell'articolo, gli avvisi in Operations Management Suite vengono estesi ad Azure nel quadro dell'esecuzione pianificata per il giorno successivo. Non è necessario intraprendere alcun'altra azione né avviare alcuna operazione.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza degli avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
* Informazioni sugli [avvisi del log in Avvisi di Azure](alerts-unified-log.md)

