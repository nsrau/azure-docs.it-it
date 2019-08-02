---
title: Ricerche salvate nelle soluzioni di gestione | Microsoft Docs
description: Le soluzioni di gestione includono in genere ricerche salvate in Log Analytics per l'analisi dei dati raccolti dalla soluzione. Potranno anche definire avvisi per la notifica all'utente o per eseguire automaticamente un'azione in risposta a un problema critico. Questo articolo descrive come definire Log Analytics le ricerche salvate in un modello di Gestione risorse in modo che possano essere incluse nelle soluzioni di gestione.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2e32fb57a5ee34da8c342649cc1740d111723ec
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662897"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Aggiunta di avvisi e di ricerche salvate di Log Analytics alla soluzione di gestione (anteprima)

> [!IMPORTANT]
> Come [annunciato in precedenza](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), le aree di lavoro di log Analytics create dopo *il 1 ° giugno 2019* . saranno in grado di gestire le regole di avviso usando **solo** l' [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)di Azure scheduledQueryRules, il [modello di Azure Resource Manager](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). I clienti possono [passare facilmente alla gestione delle regole di avviso preferita per le](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) aree di lavoro precedenti per sfruttare le ScheduledQueryRules di monitoraggio di Azure come predefinite e ottenere molti [nuovi vantaggi](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) , ad esempio la possibilità di usare i cmdlet nativi di PowerShell, aumentando periodo di tempo lookback nelle regole, creazione di regole in un gruppo di risorse separato o in una sottoscrizione e molto altro ancora.

> [!NOTE]
> Questo è un documento preliminare che illustra come creare soluzioni di gestione attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.

Le [soluzioni di gestione](solutions.md) includeranno in genere [ricerche salvate](../../azure-monitor/log-query/log-query-overview.md) di Log Analytics per l'analisi dei dati raccolti dalla soluzione. Potranno anche definire [avvisi](../../azure-monitor/platform/alerts-overview.md) per la notifica all'utente o per eseguire automaticamente un'azione in risposta a un problema critico. Questo articolo descrive come definire le ricerche salvate e gli avvisi di Log Analytics in un [modello di Resource Manager](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) in modo da consentirne l'inclusione nelle [soluzioni di gestione](solutions-creating.md).

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono obbligatori o comuni nelle soluzioni di gestione e che sono descritti in [Progettare e creare una soluzione di gestione in Azure](solutions-creating.md).

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si abbia già familiarità con la [creazione di una soluzione di gestione](solutions-creating.md) e la struttura di un [modello di Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) e un file di soluzione.


## <a name="log-analytics-workspace"></a>area di lavoro Log Analytics
Tutte le risorse in Log Analytics sono contenute in un'[area di lavoro](../../azure-monitor/platform/manage-access.md). Come descritto in [area di lavoro Log Analytics e account di Automazione](solutions.md#log-analytics-workspace-and-automation-account), l'area di lavoro non è inclusa nella soluzione di gestione, ma deve essere presente prima che la soluzione venga installata. Se non è disponibile, l'installazione della soluzione non riesce.

Il nome dell'area di lavoro è il nome di ogni risorsa di Log Analytics. A questo scopo, nella soluzione viene usato il parametro **workspace** come nell'esempio seguente di una risorsa SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versione API di Log Analytics
Tutte le risorse di Log Analytics definite in un modello di Resource Manager hanno una proprietà **apiVersion** che definisce la versione dell'API che la risorsa deve usare.

La tabella seguente elenca la versione dell'API per la risorsa usata in questo esempio.

| Tipo di risorsa | Versione dell'API | Query |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Ricerche salvate
Includere [ricerche salvate](../../azure-monitor/log-query/log-query-overview.md) in una soluzione per consentire agli utenti di eseguire query sui dati raccolti dalla soluzione. Le ricerche salvate vengono visualizzate in **Ricerche salvate** nel portale di Azure. È necessaria una ricerca salvata anche per ogni avviso.

Le risorse [ricerca salvata di Log Analytics](../../azure-monitor/log-query/log-query-overview.md) sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches` e hanno la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri.

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

| Proprietà | descrizione |
|:--- |:--- |
| category | Categoria della ricerca salvata.  Tutte le ricerche salvate nella stessa soluzione condivideranno in genere una singola categoria in modo da essere raggruppate nella console. |
| displayname | Nome da visualizzare per la ricerca salvata nel portale. |
| query | Query da eseguire. |

> [!NOTE]
> Potrebbe essere necessario usare caratteri di escape, se la query include caratteri che potrebbero essere interpretati come JSON. La query **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** , ad esempio, dovrà essere scritta nel file di soluzione nel modo seguente: **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"** .

## <a name="alerts"></a>Avvisi
Gli [avvisi del log di Azure](../../azure-monitor/platform/alerts-unified-log.md) vengono creati dalle regole di avviso di Azure che eseguono le query di log specificate a intervalli regolari. Se i risultati della query corrispondono ai criteri specificati, viene creato un record di avviso e vengono eseguite una o più azioni usando i [gruppi di azioni](../../azure-monitor/platform/action-groups.md).

Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure è possibile recuperare o aggiungere azioni usando il [modello di Azure Resource Manager - gruppo di azioni](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Le regole di avviso nella soluzione di gestione legacy sono costituite dalle tre diverse risorse seguenti.

- **Ricerca salvata.** Definisce la ricerca log che viene eseguita. Una singola ricerca salvata può essere condivisa da più regole di avviso.
- **Pianificazione.** Definisce la frequenza di esecuzione della ricerca log. Ogni regola di avviso ha una sola pianificazione.
- **Azione di avviso.** Ogni regola di avviso dispone di una risorsa gruppo di azioni o risorsa azione (legacy) di tipo **Alert** che definisce i dettagli dell'avviso, come i criteri per la creazione di un record di avviso e la gravità dell'avviso. La risorsa [Gruppo di azioni](../../azure-monitor/platform/action-groups.md) può disporre di un elenco di azioni configurate da intraprendere quando viene generato l'avviso, come ad esempio chiamata vocale, SMS, messaggio di posta elettronica, webhook, strumento ITSM, runbook di automazione, app per la logica e così via.

Le risorse ricerca salvata sono illustrate sopra. Di seguito sono descritte le altre risorse.

### <a name="schedule-resource"></a>Risorsa pianificazione

Una ricerca salvata può avere una o più pianificazioni, ognuna delle quali rappresenta una regola di avviso separata. La pianificazione definisce la frequenza con cui viene eseguita la ricerca e l'intervallo di tempo per cui vengono recuperati i dati. Le risorse pianificazione sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e hanno la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri.

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

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| enabled       | Yes | Specifica se l'avviso viene abilitato al momento della creazione. |
| interval      | Yes | Frequenza, in minuti, con cui viene eseguita la query. |
| queryTimeSpan | Yes | Periodo di tempo, in minuti, per cui verranno valutati i risultati. |

La risorsa pianificazione dipenderà dalla ricerca salvata, che verrà quindi creata prima della pianificazione.
> [!NOTE]
> Il nome della pianificazione deve essere univoco per ogni area di lavoro specifica; due pianificazioni non possono avere lo stesso ID anche se sono associate a ricerche diverse. Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo.

### <a name="actions"></a>Azioni
Una pianificazione può avere più azioni. Un'azione può definire uno o più processi da eseguire, ad esempio l'invio di un messaggio di posta o l'avvio di un runbook o ancora può definire una soglia che determina quando i risultati di una ricerca corrispondono a certi criteri. Alcune azioni definiranno entrambi, in modo che i processi vengano eseguiti quando viene raggiunta la soglia.
Le azioni possono essere definite usando la risorsa [gruppo di azioni] o la risorsa azione.

La proprietà **Type** specifica due tipi di risorsa azione. Una pianificazione richiede un'azione **Alert** che definisce i dettagli della regola di avviso e le azioni da eseguire quando viene creato un avviso. Le risorse azione sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Le azioni di avviso hanno la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri.

```json
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

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| type | Yes | Tipo di azione.  Per le azioni di avviso, il tipo è **Alert**. |
| name | Yes | Nome visualizzato per l'avviso.  È il nome visualizzato nella console per la regola di avviso. |
| descrizione | No | Descrizione facoltativa dell'avviso. |
| severity | Yes | Gravità del record di avviso tra i valori seguenti:<br><br> **critical**<br>**warning**<br>**informational**


#### <a name="threshold"></a>Soglia
Questa sezione è obbligatoria e definisce le proprietà della soglia dell'avviso.

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| Operator | Yes | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Value | Yes | Valore per il confronto dei risultati. |

##### <a name="metricstrigger"></a>MetricsTrigger
Questa sezione è facoltativa. Includere la sezione per un avviso di misurazione delle metriche.

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| TriggerCondition | Yes | Specifica se la soglia riguarda il numero totale di violazioni o le violazioni consecutive, con i valori seguenti:<br><br>**Total<br>Consecutive** |
| Operator | Yes | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Value | Yes | Numero di volte in cui i criteri devono essere soddisfatti per attivare l'avviso. |


#### <a name="throttling"></a>Limitazione
Questa sezione è facoltativa. Includere la sezione se si vogliono eliminare gli avvisi generati dalla stessa regola per un determinato intervallo di tempo dopo la creazione di un avviso.

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| DurationInMinutes | Sì, se è incluso l'elemento Throttling | Numero di minuti in cui verranno eliminati gli avvisi dopo che ne è stato creato uno dalla stessa regola di avviso. |

#### <a name="azure-action-group"></a>Gruppo di azioni di Azure
Tutti gli avvisi in Azure usano un gruppo di azioni come meccanismo predefinito per la gestione delle azioni. Con un gruppo di azioni, è possibile specificare le azioni una volta e quindi associare il gruppo di azioni a più avvisi in Azure. Tutto questo senza la necessità di dichiarare ripetutamente le stesse azioni più volte. I gruppi di azioni supportano più azioni, tra cui posta elettronica, SMS, chiamate vocali, connessioni di Gestione dei servizi IT, runbook di Automazione, URI di webhook e altro ancora.

Per gli utenti che hanno esteso gli avvisi in Azure, per una pianificazione devono ora essere passati i dettagli del gruppo di azioni insieme alla soglia per poter creare un avviso. I dettagli di posta elettronica, gli URL di webhook, i dettagli relativi all'automazione runbook e altre azioni devono essere definiti all'interno di un gruppo di azioni prima di creare un avviso. È possibile creare un [gruppo di azioni da Monitoraggio di Azure](../../azure-monitor/platform/action-groups.md) nel portale o usare il [modello Gruppo di azioni - Risorsa](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nome dell'elemento | Obbligatorio | descrizione |
|:--|:--|:--|
| AzNsNotification | Yes | ID risorsa del gruppo di azioni di Azure da associare a un avviso per l'esecuzione di azioni necessarie quando viene soddisfatto il criterio di avviso. |
| CustomEmailSubject | No | Riga dell'oggetto personalizzata del messaggio inviato a tutti gli indirizzi specificati nel gruppo di azioni associato. |
| CustomWebhookPayload | No | Payload personalizzato da inviare a tutti gli endpoint webhook definiti nel gruppo di azioni associato. Il formato dipende da ciò che è previsto dal webhook e deve essere un oggetto JSON serializzato valido. |

## <a name="sample"></a>Esempio

Di seguito è riportato un esempio di soluzione che include le risorse seguenti:

- Ricerca salvata
- Pianificazione
- Gruppo di azioni

L'esempio usa variabili dei [parametri di soluzione standard]( solutions-solution-file.md#parameters) comunemente usate in una soluzione, anziché impostare i valori come hardcoded nelle definizioni delle risorse.

```json
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
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
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
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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

```json
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
* [Aggiungere viste](solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook di automazione e altre risorse](solutions-resources-automation.md) alla soluzione di gestione.
