---
title: Ricerche salvate e avvisi nelle soluzioni di gestione | Microsoft Docs
description: Le soluzioni di gestione includono in genere ricerche salvate in Log Analytics per l'analisi dei dati raccolti dalla soluzione.  Potranno anche definire avvisi per la notifica all'utente o per eseguire automaticamente un'azione in risposta a un problema critico.  Questo articolo descrive come definire le ricerche salvate e gli avvisi di Log Analytics in un modello di Resource Manager in modo da consentirne l'inclusione nelle soluzioni di gestione.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/18/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ec5f1cef3f9ca82953093d2086b615087db1a7f
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024771"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Aggiunta di avvisi e di ricerche salvate di Log Analytics alla soluzione di gestione (anteprima)

> [!NOTE]
> Questo è un documento preliminare che illustra come creare soluzioni di gestione attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.   


Le [soluzioni di gestione](monitoring-solutions.md) includeranno in genere [ricerche salvate](../log-analytics/log-analytics-log-searches.md) di Log Analytics per l'analisi dei dati raccolti dalla soluzione.  Potranno anche definire [avvisi](../log-analytics/log-analytics-alerts.md) per la notifica all'utente o per eseguire automaticamente un'azione in risposta a un problema critico.  Questo articolo descrive come definire le ricerche salvate e gli avvisi di Log Analytics in un [modello di Resource Manager](../resource-manager-template-walkthrough.md) in modo da consentirne l'inclusione nelle [soluzioni di gestione](monitoring-solutions-creating.md).

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono richiesti o comuni nelle soluzioni di gestione e che sono descritti in [Progettare e creare una soluzione di gestione in Azure](monitoring-solutions-creating.md)  

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si abbia già familiarità con la [creazione di una soluzione di gestione](monitoring-solutions-creating.md) e la struttura di un [modello di Resource Manager](../resource-group-authoring-templates.md) e un file di soluzione.


## <a name="log-analytics-workspace"></a>Area di lavoro di Log Analytics
Tutte le risorse in Log Analytics sono contenute in un'[area di lavoro](../log-analytics/log-analytics-manage-access.md).  Come descritto in [Area di lavoro di Log Analytics e account di Automazione](monitoring-solutions.md#log-analytics-workspace-and-automation-account), l'area di lavoro non è inclusa nella soluzione di gestione, ma deve essere presente prima che la soluzione venga installata.  Se non è disponibile, l'installazione della soluzione non riesce.

Il nome dell'area di lavoro è il nome di ogni risorsa di Log Analytics.  A questo scopo, nella soluzione viene usato il parametro **workspace** come nell'esempio seguente di una risorsa SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versione API di Log Analytics
Tutte le risorse di Log Analytics definite in un modello di Resource Manager hanno una proprietà **apiVersion** che definisce la versione dell'API che la risorsa deve usare.   

La tabella seguente elenca la versione dell'API per la risorsa usata in questo esempio.

| Tipo di risorsa | Versione dell'API | Query |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Ricerche salvate
Includere [ricerche salvate](../log-analytics/log-analytics-log-searches.md) in una soluzione per consentire agli utenti di eseguire query sui dati raccolti dalla soluzione.  Le ricerche salvate vengono visualizzate in **Ricerche salvate** nel portale di Azure.  È necessaria una ricerca salvata anche per ogni avviso.   

Le risorse [ricerca salvata di Log Analytics](../log-analytics/log-analytics-log-searches.md) sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches` e hanno la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



Le singole proprietà di una ricerca salvata sono descritte nella tabella seguente. 

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| category | Categoria della ricerca salvata.  Tutte le ricerche salvate nella stessa soluzione condivideranno in genere una singola categoria in modo da essere raggruppate nella console. |
| displayname | Nome da visualizzare per la ricerca salvata nel portale. |
| query | Query da eseguire. |

> [!NOTE]
> Potrebbe essere necessario usare caratteri di escape, se la query include caratteri che potrebbero essere interpretati come JSON.  La query **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, ad esempio, dovrà essere scritta nel file di soluzione nel modo seguente: **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Avvisi
Gli [avvisi del log di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) vengono creati dalle regole di avviso di Azure che eseguono le query di log specificate a intervalli regolari.  Se i risultati della query corrispondono ai criteri specificati, viene creato un record di avviso e vengono eseguite una o più azioni usando i [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md).  

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno estesi ad Azure. Per altre informazioni, vedere [Estendere avvisi da Log Analytics ad Avvisi di Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure è possibile recuperare o aggiungere azioni usando il [modello di Azure Resource Manager - gruppo di azioni](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Le regole di avviso in una soluzione di gestione sono costituite dalle tre diverse risorse riportate di seguito.

- **Ricerca salvata.**  Definisce la ricerca log che viene eseguita.  Una singola ricerca salvata può essere condivisa da più regole di avviso.
- **Pianificazione.**  Definisce la frequenza di esecuzione della ricerca log.  Ogni regola di avviso ha una sola pianificazione.
- **Azione di avviso.**  Ogni regola di avviso dispone di una risorsa gruppo di azioni o risorsa azione (legacy) di tipo **Alert** che definisce i dettagli dell'avviso, come i criteri per la creazione di un record di avviso e la gravità dell'avviso. La risorsa [Gruppo di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) può disporre di un elenco di azioni configurate da intraprendere quando viene generato l'avviso, come ad esempio chiamata vocale, SMS, messaggio di posta elettronica, webhook, strumento ITSM, runbook di automazione, app per la logica e così via.
 
La risorsa azione (legacy) definirà facoltativamente una risposta tramite posta elettronica e runbook.
- **Azione webhook (legacy)**  Se la regola di avviso chiama un webhook, è necessaria una risorsa azione aggiuntiva di tipo **Webhook**.    

Le risorse ricerca salvata sono illustrate sopra.  Di seguito sono descritte le altre risorse.


### <a name="schedule-resource"></a>Risorsa pianificazione

Una ricerca salvata può avere una o più pianificazioni, ognuna delle quali rappresenta una regola di avviso separata. La pianificazione definisce la frequenza con cui viene eseguita la ricerca e l'intervallo di tempo per cui vengono recuperati i dati.  Le risorse pianificazione sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e hanno la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



Le proprietà delle risorse pianificazione sono descritte nella tabella seguente.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| Enabled       | Yes | Specifica se l'avviso viene abilitato al momento della creazione. |
| interval      | Yes | Frequenza, in minuti, con cui viene eseguita la query. |
| queryTimeSpan | Yes | Periodo di tempo, in minuti, per cui verranno valutati i risultati. |

La risorsa pianificazione dipenderà dalla ricerca salvata, che verrà quindi creata prima della pianificazione.

> [!NOTE]
> Il nome della pianificazione deve essere univoco per ogni area di lavoro specifica; due pianificazioni non possono avere lo stesso ID anche se sono associate a ricerche diverse. Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo.


### <a name="actions"></a>Azioni
Una pianificazione può avere più azioni. Un'azione può definire uno o più processi da eseguire, ad esempio l'invio di un messaggio di posta o l'avvio di un runbook o ancora può definire una soglia che determina quando i risultati di una ricerca corrispondono a certi criteri.  Alcune azioni definiranno entrambi, in modo che i processi vengano eseguiti quando viene raggiunta la soglia.

Le azioni possono essere definite usando la risorsa [gruppo di azioni] o la risorsa azione.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. Per altre informazioni, vedere [Estendere avvisi da Log Analytics ad Avvisi di Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure è possibile recuperare o aggiungere azioni usando il [modello di Azure Resource Manager - gruppo di azioni](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


La proprietà **Type** specifica due tipi di risorsa azione.  Una pianificazione richiede un'azione **Alert** che definisce i dettagli della regola di avviso e le azioni da eseguire quando viene creato un avviso. Le risorse azione sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Le azioni di avviso hanno la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 


```
    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                  },
              },
      "AzNsNotification": {
        "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
        "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
        "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
        }
    }
```

Le proprietà delle risorse azione di avviso sono descritte nella tabella seguente.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| type | Yes | Tipo di azione.  Per le azioni di avviso, il tipo è **Alert**. |
| NOME | Yes | Nome visualizzato per l'avviso.  È il nome visualizzato nella console per la regola di avviso. |
| DESCRIZIONE | No  | Descrizione facoltativa dell'avviso. |
| Severity | Yes | Gravità del record di avviso tra i valori seguenti:<br><br> **critical**<br>**warning**<br>**informational**


#### <a name="threshold"></a>Soglia
Questa sezione è obbligatoria  e definisce le proprietà della soglia dell'avviso.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| Operatore | Yes | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Valore | Yes | Valore per il confronto dei risultati. |

##### <a name="metricstrigger"></a>MetricsTrigger
Questa sezione è facoltativa.  Includere la sezione per un avviso di misurazione delle metriche.

> [!NOTE]
> Gli avvisi di misurazione delle metriche sono attualmente disponibili in anteprima pubblica. 

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| TriggerCondition | Yes | Specifica se la soglia riguarda il numero totale di violazioni o le violazioni consecutive, con i valori seguenti:<br><br>**Total<br>Consecutive** |
| Operatore | Yes | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Valore | Yes | Numero di volte in cui i criteri devono essere soddisfatti per attivare l'avviso. |


#### <a name="throttling"></a>Limitazione
Questa sezione è facoltativa.  Includere la sezione se si vogliono eliminare gli avvisi generati dalla stessa regola per un determinato intervallo di tempo dopo la creazione di un avviso.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| DurationInMinutes | Sì, se è incluso l'elemento Throttling | Numero di minuti in cui verranno eliminati gli avvisi dopo che ne è stato creato uno dalla stessa regola di avviso. |


#### <a name="azure-action-group"></a>Gruppo di azioni di Azure
Tutti gli avvisi in Azure usano un gruppo di azioni come meccanismo predefinito per la gestione delle azioni. Con un gruppo di azioni, è possibile specificare le azioni una volta e quindi associare il gruppo di azioni a più avvisi in Azure. Tutto questo senza la necessità di dichiarare ripetutamente le stesse azioni più volte. I gruppi di azioni supportano più azioni, tra cui posta elettronica, SMS, chiamate vocali, connessioni di Gestione dei servizi IT, runbook di Automazione, URI di webhook e altro ancora. 

Per gli utenti che hanno esteso gli avvisi in Azure, per una pianificazione devono ora essere passati i dettagli del gruppo di azioni insieme alla soglia per poter creare un avviso. I dettagli di posta elettronica, gli URL di webhook, i dettagli relativi all'automazione runbook e altre azioni devono essere definiti all'interno di un gruppo di azioni prima di creare un avviso. È possibile creare un [gruppo di azioni da Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) nel portale o usare il [modello Gruppo di azioni - Risorsa](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| AzNsNotification | Yes | ID risorsa del gruppo di azioni di Azure da associare a un avviso per l'esecuzione di azioni necessarie quando viene soddisfatto il criterio di avviso. |
| CustomEmailSubject | No  | Riga dell'oggetto personalizzata del messaggio inviato a tutti gli indirizzi specificati nel gruppo di azioni associato. |
| CustomWebhookPayload | No  | Payload personalizzato da inviare a tutti gli endpoint webhook definiti nel gruppo di azioni associato. Il formato dipende da ciò che è previsto dal webhook e deve essere un oggetto JSON serializzato valido. |


#### <a name="actions-for-oms-legacy"></a>Azioni per OMS (legacy)

Ogni pianificazione ha un'azione **Alert**.  che definisce i dettagli dell'avviso e, facoltativamente, le azioni di notifica e correzione.  Una notifica invia un messaggio di posta elettronica a uno o più indirizzi.  Una correzione avvia un runbook in Automazione di Azure per provare a risolvere il problema rilevato.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. Per altre informazioni, vedere [Estendere avvisi da Log Analytics ad Avvisi di Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure è possibile recuperare o aggiungere azioni usando il [modello di Azure Resource Manager - gruppo di azioni](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Questa sezione è facoltativa. Includere la sezione se si vuole inviare un messaggio di posta elettronica a uno o più destinatari.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| Destinatari | Yes | Elenco delimitato da virgole di indirizzi di posta elettronica a cui inviare una notifica quando viene creato un avviso, come nell'esempio seguente.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Oggetto | Yes | Riga dell'oggetto del messaggio di posta elettronica. |
| Attachment | No  | Gli allegati non sono attualmente supportati.  Se questo elemento è incluso, il valore dovrà essere **None**. |


##### <a name="remediation"></a>Correzione
Questa sezione è facoltativa. Includere la sezione se si vuole avviare un runbook in risposta all'avviso. |

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| RunbookName | Yes | Nome del runbook da avviare. |
| WebhookUri | Yes | URI del webhook per il runbook. |
| Expiry | No  | Data e ora di scadenza della correzione. |

##### <a name="webhook-actions"></a>Azioni webhook

Le azioni webhook avviano un processo chiamando un URL e, facoltativamente, fornendo un payload da inviare. Simili alle azioni correttive, sono destinate a webhook che possono richiamare processi diversi dai runbook di Automazione di Azure. Hanno inoltre l'opzione aggiuntiva di fornire un payload da recapitare al processo remoto.

Se l'avviso chiamerà un webhook, sarà necessaria una risorsa azione di tipo **Webhook** in aggiunta alla risorsa azione **Alert**.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

Le proprietà delle risorse azione webhook sono descritte nella tabella seguente.

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--|:--|:--|
| type | Yes | Tipo di azione.  Per le azioni webhook, il tipo è **Webhook**. |
| name | Yes | Nome visualizzato per l'azione.  Non viene visualizzato nella console. |
| wehookUri | Yes | URI del webhook. |
| customPayload | No  | Payload personalizzato da inviare al webhook. Il formato dipende da ciò che il webhook si aspetta. |


## <a name="sample"></a>Esempio

Di seguito è riportato un esempio di soluzione che include le risorse seguenti:

- Ricerca salvata
- Pianificazione
- Gruppo di azioni

L'esempio usa variabili dei [parametri di soluzione standard]( monitoring-solutions-solution-file.md#parameters) comunemente usate in una soluzione, anziché impostare i valori come hardcoded nelle definizioni delle risorse.

```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "actiongroup": {
            "type": "string",
            "metadata": {
              "Description": "List of action groups for alert actions separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
              "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",

          "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
              "GroupIds": [
                "[parameters('actiongroup')]"
              ],
              "CustomEmailSubject": "Sample alert"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
            "AzNsNotification": {
              "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
              "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
            }             
            }
          }
        ]
    }
```

Il file di parametri seguente offre valori di esempio per la soluzione.
```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "actiongroup": {
                "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
            }
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere viste](monitoring-solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook di automazione e altre risorse](monitoring-solutions-resources-automation.md) alla soluzione di gestione.

