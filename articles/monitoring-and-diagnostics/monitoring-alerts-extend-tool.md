---
title: Come estendere (copiare) avvisi dal portale di OMS ad Azure | Microsoft Docs
description: I clienti possono eseguire volontariamente l'estensione degli avvisi da OMS ad Avvisi di Azure tramite strumenti e API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166183"
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Come estendere (copiare) avvisi da OMS ad Azure
A partire dal **14 maggio 2018**, tutti gli avvisi configurati in [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) verranno estesi ad Azure. Gli avvisi estesi ad Azure hanno le stesse caratteristiche degli avvisi di OMS. Le funzionalità di monitoraggio rimangono inalterate. L'estensione degli avvisi creati in OMS ad Azure offre numerosi vantaggi. Per altre informazioni sui vantaggi e sul processo di estensione degli avvisi da OMS ad Azure, vedere [Estendere gli avvisi da OMS ad Azure](monitoring-alerts-extend.md).

> [!NOTE]
> A partire dal 14 maggio 2018 Microsoft inizierà il processo di estensione automatica degli avvisi in Azure. Non tutte le aree di lavoro e gli avvisi verranno estesi in quel giorno: Microsoft inizierà a estendere automaticamente gli avvisi in quote nelle prossime settimane. Di conseguenza gli avvisi nel portale di OMS non verranno estesi automaticamente in Azure il 14 maggio 2018 e gli utenti potranno ancora estendere manualmente gli avvisi usando le opzioni seguenti.

I clienti che vogliono spostare gli avvisi da OMS ad Azure immediatamente, possono usare una delle opzioni indicate di seguito.

## <a name="option-1---using-oms-portal"></a>Opzione 1: uso del portale OMS
Per avviare volontariamente l'estensione degli avvisi dal portale di OMS ad Azure, seguire questa procedura.

1. Nella pagina Panoramica del portale di OMS passare a Impostazioni e quindi alla sezione Avvisi. Fare clic sul pulsante "Extend into Azure" (Estendi ad Azure), come evidenziato nella figura seguente.

    ![Pagina Impostazioni avvisi del portale di OMS con l'opzione per l' estensione](./media/monitor-alerts-extend/ExtendInto.png)

2. Dopo aver selezionato il pulsante, viene visualizzata una procedura guidata in 3 passaggi. Il primo passaggio fornisce i dettagli del processo. Premere Avanti per continuare.

    ![Estendere avvisi dal portale di OMS ad Azure - Passaggio 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. Nel secondo passaggio il sistema visualizza un riepilogo della modifica proposta, elencando i [Gruppi di azioni](monitoring-action-groups.md) appropriati, per gli avvisi presenti nel portale di OMS. Se in più avvisi sono presenti azioni simili, il sistema propone di associare a tutte un singolo gruppo di azioni.  Il gruppo di azioni proposto segue la convenzione di denominazione: *NomeAreaDiLavoro_GA_#Numero*. Per procedere, fare clic su Avanti.
Di seguito, una schermata di esempio.

    ![Estendere avvisi dal portale di OMS ad Azure - Passaggio 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. Nell'ultimo passaggio della procedura guidata è possibile chiedere al portale di OMS di pianificare l'estensione di tutti gli avvisi ad Azure, creando nuovi gruppi di azioni e associandoli agli avvisi, come illustrato nella schermata precedente. Per continuare, scegliere Fine e confermare al prompt per avviare il processo. Facoltativamente, i clienti possono anche specificare gli indirizzi di posta elettronica a cui il portale di OMS deve inviare un report al termine dell'elaborazione.

    ![Estendere avvisi dal portale di OMS ad Azure - Passaggio 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Al termine della procedura guidata si viene reindirizzati alla pagina Impostazioni avvisi e l'opzione "Extend into Azure" (Estendi ad Azure) viene rimossa. In background, il portale di OMS pianificherà l'estensione degli avvisi da Log Analytics ad Azure. L'operazione può richiedere alcuni minuti e, una volta avviata, per un breve periodo non sarà possibile apportare modifiche agli avvisi nel portale di OMS. Verrà visualizzato lo stato corrente tramite banner e, se al passaggio 4 sono stati forniti gli indirizzi di posta elettronica, si riceverà una notifica quando il processo in background estende correttamente tutti gli avvisi ad Azure. 

6. Gli avvisi continueranno a essere elencati nel portale di OMS, anche al termine dell'estensione ad Azure.

    ![Dopo l'estensione degli avvisi dal portale di OMS ad Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Opzione 2: uso dell'API
Per i clienti che vogliono automatizzare o controllare a livello di codice il processo di estensione degli avvisi dal portale di OMS ad Azure, Microsoft ha rilasciato una nuova API AlertsVersion in Log Analytics.

L'API AlertsVersion di Log Analytics è RESTful ed è accessibile tramite l'API REST di Azure Resource Manager. In questo documento sono disponibili esempi in cui si accede all'API da una riga di comando di PowerShell tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento della riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager. L'uso di ARMClient e PowerShell è una delle numerose opzioni di accesso all'API. L'API restituisce i risultati in formato JSON, consentendone l'utilizzo in diversi modi a livello di codice.

Usando GET sull'API, è possibile ottenere il riepilogo della modifica proposta come elenco di [Gruppi di azioni](monitoring-action-groups.md) appropriati per gli avvisi nel portale di OMS, in formato JSON. Se in più avvisi sono presenti azioni simili, il sistema propone di associare a tutte un singolo gruppo di azioni.  Il gruppo di azioni proposto segue la convenzione di denominazione: *NomeAreaDiLavoro_GA_#Numero*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> La chiamata GET all'API non comporterà l'estensione degli avvisi dal portale di OMS ad Azure. Si limiterà a fornire come risposta il riepilogo delle modifiche proposte. Per confermare l'esecuzione delle modifiche in modo da estendere gli avvisi ad Azure, è necessario eseguire una chiamata POST all'API.

Se la chiamata GET all'API ha esito positivo, insieme alla risposta 200 OK, verrà fornito un elenco JSON di avvisi insieme ai gruppi di azioni proposti. Di seguito, una risposta di esempio:

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
In caso non siano presenti avvisi nell'area di lavoro specificata, insieme alla risposta 200 OK per l'operazione GET il formato JSON sarà:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Se tutti gli avvisi nell'area di lavoro specificata, sono già stati estesi ad Azure, la risposta alla chiamata GET sarà:
```json
{
    "version": 2
}
```

Per avviare la pianificazione dell'estensione degli avvisi dal portale di OMS ad Azure, eseguire una chiamata POST all'API. L'esecuzione di tale chiamata/comando conferma le finalità dell'utente e l'accettazione dell'estensione degli avvisi dal portale di OMS ad Azure ed esegue le modifiche, come indicato nella risposta della chiamata GET all'API. L'utente può facoltativamente fornire un elenco di indirizzi di posta elettronica a cui il portale di OMS invierà un report al termine del processo in background di estensione degli avvisi dal portale di OMS ad Azure pianificato.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Il risultato dell'estensione degli avvisi dal portale di OMS ad Azure può variare rispetto al riepilogo fornito da GET, in base a qualsiasi modifica apportata nel sistema. Dopo aver pianificato l'operazione non sarà più possibile apportare modifiche agli avvisi nel portale di OMS, sebbene sia possibile crearne di nuovi. 

Se la chiamata POST ha esito positivo, restituisce una risposta 200 OK insieme a:
```json
{
    "version": 2
}
```
Tale risposta indica che gli avvisi sono stati estesi ad Azure, come indicato dalla versione 2. Questa versione ha il solo scopo di verificare se gli avvisi sono stati estesi ad Azure e non hanno effetto sull'utilizzo con l'[API di ricerca di Log Analytics](../log-analytics/log-analytics-api-alerts.md). Dopo che gli avvisi sono stati correttamente estesi ad Azure, a tutti indirizzi di posta elettronica forniti durante le operazioni GET verrà inviato un report con i dettagli delle modifiche apportate.

Infine, se tutti gli avvisi nell'area di lavoro specificata sono già stati impostati per l'estensione ad Azure, la risposta a POST sarà 403 Accesso negato. Per visualizzare eventuali messaggi di errore o capire se il processo di estensione è bloccato, l'utente può eseguire una chiamata GET e l'eventuale messaggio di errore verrà restituito insieme al riepilogo.

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

## <a name="troubleshooting"></a>risoluzione dei problemi 
Durante il processo di estensione degli avvisi da OMS in Azure possono verificarsi problemi occasionali che impediscono al sistema di creare i [Gruppi di azioni](monitoring-action-groups.md) necessari. In questi casi verrà visualizzato un messaggio di errore nel portale di OMS sotto forma di un banner nella sezione degli avvisi e in una chiamata GET all'API.

Di seguito sono elencate le procedure di correzione per ogni errore:
1. **Error: The subscription is not registered to use the namespace 'microsoft.insights'** (Errore: la sottoscrizione non è registrata per l'uso dello spazio dei nomi 'microsoft.insights'): ![Pagina Impostazioni avvisi del portale di OMS con messaggio di errore di registrazione](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. La sottoscrizione associata all'area di lavoro di OMS non è stata registrata per l'uso della funzionalità Monitoraggio di Azure (microsoft.insights). Per questo motivo, OMS non può estendere gli avvisi in Monitoraggio di Azure e Avvisi.
    
    b. Per risolvere il problema, registrare l'uso di microsoft.insights (Monitoraggio di Azure e Avvisi) nella sottoscrizione tramite PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure. Per altre informazioni, vedere l'articolo sulla [risoluzione degli errori durante la registrazione del provider di risorse](../azure-resource-manager/resource-manager-register-provider-errors.md)
    
    c. Una volta risolto l'errore in base alla procedura illustrata nell'articolo, OMS estenderà gli avvisi in Azure durante l'esecuzione pianificata del giorno successivo senza bisogno di alcun intervento.
2. **Error: Scope Lock is present at subscription/resource group level for write operations** (Errore: a livello di sottoscrizione/gruppo di risorse è presente un blocco dell'ambito per le operazioni di scrittura): ![Pagina Impostazioni avvisi del portale di OMS con messaggio di errore di blocco dell'ambito](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Quando è abilitato il blocco dell'ambito, che limita le nuove modifiche nella sottoscrizione o nel gruppo di risorse contenente l'area di lavoro di Log Analytics (OMS), il sistema non è in grado di estendere (copiare) gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    b. Per risolvere il problema, eliminare il blocco *ReadOnly* sulla sottoscrizione o il gruppo di risorse che contiene l'area di lavoro usando PowerShell, l'interfaccia della riga di comando di Azure, il portale di Azure o l'API. Per altre informazioni, vedere l'articolo sull'[utilizzo del blocco delle risorse](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Una volta risolto l'errore in base alla procedura illustrata nell'articolo, OMS estenderà gli avvisi in Azure durante l'esecuzione pianificata del giorno successivo senza bisogno di alcun intervento.

3. **Error: Policy is present at subscription/resource group level** (Errore: a livello di sottoscrizione/gruppo di risorse è presente un criterio): ![Pagina Impostazioni avvisi del portale di OMS con messaggio di errore di criterio](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Quando è applicato il [criterio di Azure](../azure-policy/azure-policy-introduction.md), che limita le nuove risorse nella sottoscrizione o nel gruppo di risorse contenente l'area di lavoro di Log Analytics (OMS), il sistema non è in grado di estendere (copiare) gli avvisi in Azure e creare i gruppi di azioni necessari.
    
    b. Per risolvere, modificare il criterio che causa l'errore *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*  che impedisce la creazione di nuove risorse nella sottoscrizione o nel gruppo di risorse che contiene l'area di lavoro. Usando il portale di Azure, Powershell, l'interfaccia della riga di comando di Azure o l'API è possibile controllare le azioni per trovare il criterio che provoca l'errore. Per altre informazioni, visualizzare l'articolo sulla [visualizzazione dei log attività per il controllo delle azioni](../azure-resource-manager/resource-group-audit.md). 
    
    c. Una volta risolto l'errore in base alla procedura illustrata nell'articolo, OMS estenderà gli avvisi in Azure durante l'esecuzione pianificata del giorno successivo senza bisogno di alcun intervento.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza degli avvisi di Azure](monitoring-overview-unified-alerts.md)
* Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
