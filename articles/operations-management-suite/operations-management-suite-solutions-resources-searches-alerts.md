---
title: Ricerche salvate e avvisi nelle soluzioni OMS | Microsoft Docs
description: Le soluzioni in OMS includeranno in genere ricerche salvate di Log Analytics per l&quot;analisi dei dati raccolti dalla soluzione.  Potranno anche definire avvisi per la notifica all&quot;utente o per eseguire automaticamente un&quot;azione in risposta a un problema critico.  Questo articolo descrive come definire le ricerche salvate e gli avvisi di Log Analytics in un modello di Azure Resource Manager in modo da consentirne l&quot;inclusione nelle soluzioni di gestione.
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 21c42a747a08c5386c65d10190baf0054a7adef8
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Aggiunta di avvisi e di ricerche salvate di Log Analytics alla soluzione di gestione in OMS (anteprima)

> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.   


Le [soluzioni di gestione in OMS](operations-management-suite-solutions.md) includeranno in genere [ricerche salvate](../log-analytics/log-analytics-log-searches.md) di Log Analytics per l'analisi dei dati raccolti dalla soluzione.  Potranno anche definire [avvisi](../log-analytics/log-analytics-alerts.md) per la notifica all'utente o per eseguire automaticamente un'azione in risposta a un problema critico.  Questo articolo descrive come definire le ricerche salvate e gli avvisi di Log Analytics in un [modello di Resource Manager](../resource-manager-template-walkthrough.md) in modo da consentirne l'inclusione nelle [soluzioni di gestione](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono richiesti o comuni nelle soluzioni di gestione e che sono descritti in [Creazione di soluzioni di gestione in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si abbia già familiarità con la [creazione di una soluzione di gestione](operations-management-suite-solutions-creating.md) e la struttura di un [modello di Azure Resource Manager](../resource-group-authoring-templates.md) e un file di soluzione.


## <a name="log-analytics-workspace"></a>Area di lavoro di Log Analytics
Tutte le risorse in Log Analytics sono contenute in un'[area di lavoro](../log-analytics/log-analytics-manage-access.md).  Come descritto in [Area di lavoro OMS e account di Automazione](operations-management-suite-solutions.md#oms-workspace-and-automation-account), l'area di lavoro non è inclusa nella soluzione di gestione, ma deve essere presente prima che la soluzione venga installata.  Se non è disponibile, l'installazione della soluzione non riuscirà.

Il nome dell'area di lavoro è il nome di ogni risorsa di Log Analytics.  A questo scopo, nella soluzione viene usato il parametro **workspace** come nell'esempio seguente di una risorsa savedsearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"


## <a name="saved-searches"></a>Ricerche salvate
Includere [ricerche salvate](../log-analytics/log-analytics-log-searches.md) in una soluzione per consentire agli utenti di eseguire query sui dati raccolti dalla soluzione.  Le ricerche salvate verranno visualizzate in **Preferiti** nel portale di OMS e in **Ricerche salvate** nel portale di Azure.  È necessaria una ricerca salvata anche per ogni avviso.   

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

| Proprietà | Descrizione |
|:--- |:--- |
| category | Categoria della ricerca salvata.  Tutte le ricerche salvate nella stessa soluzione condivideranno in genere una singola categoria in modo da essere raggruppate nella console. |
| displayname | Nome da visualizzare per la ricerca salvata nel portale. |
| query | Query da eseguire. |

> [!NOTE]
> Potrebbe essere necessario usare caratteri di escape, se la query include caratteri che potrebbero essere interpretati come JSON.  La query **Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, ad esempio, dovrà essere scritta nel file di soluzione nel modo seguente: **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Avvisi
Gli [avvisi di Log Analytics](../log-analytics/log-analytics-alerts.md) vengono creati da regole di avviso che eseguono una ricerca salvata a intervalli regolari.  Se i risultati della query corrispondono ai criteri specificati, viene creato un record di avviso e vengono eseguite una o più azioni.  

Le regole di avviso in una soluzione di gestione sono costituite dalle tre diverse risorse riportate di seguito.

- **Ricerca salvata.**  Definisce la ricerca nei log che verrà eseguita.  Una singola ricerca salvata può essere condivisa da più regole di avviso.
- **Pianificazione.**  Definisce la frequenza con cui verrà eseguita la ricerca nei log.  Ogni regola di avviso avrà una sola pianificazione.
- **Azione di avviso.**  Ogni regola di avviso avrà una risorsa azione di tipo **Alert** che definisce dettagli dell'avviso come i criteri per la creazione di un record di avviso e la gravità.  La risorsa azione definirà facoltativamente una risposta tramite posta elettronica e runbook.
- **Azione webhook (facoltativa).**  Se la regola di avviso chiamerà un webhook, è necessaria una risorsa azione aggiuntiva di tipo **Webhook**.    

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

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| Enabled       | Sì | Specifica se l'avviso viene abilitato al momento della creazione. |
| interval      | Sì | Frequenza, in minuti, con cui viene eseguita la query. |
| queryTimeSpan | Sì | Periodo di tempo, in minuti, per cui verranno valutati i risultati. |

La risorsa pianificazione dipenderà dalla ricerca salvata, che verrà quindi creata prima della pianificazione.


### <a name="actions"></a>Azioni
La proprietà **Type** specifica due tipi di risorsa azione.  Una pianificazione richiede un'azione **Alert** che definisce i dettagli della regola di avviso e le azioni da eseguire quando viene creato un avviso.  Può includere anche un'azione **Webhook**, se dall'avviso verrà chiamato un webhook.  

Le risorse azione sono di tipo `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Azioni di avviso

Ogni pianificazione avrà un'azione **Alert**,  che definisce i dettagli dell'avviso e, facoltativamente, le azioni di notifica e correzione.  Una notifica invia un messaggio di posta elettronica a uno o più indirizzi.  Una correzione avvia un runbook in Automazione di Azure per provare a risolvere il problema rilevato.

Le azioni di avviso hanno la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 



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
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

Le proprietà delle risorse azione di avviso sono descritte nella tabella seguente.

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| Type | Sì | Tipo di azione.  Per le azioni di avviso, sarà **Alert**. |
| Nome | Sì | Nome visualizzato per l'avviso.  È il nome visualizzato nella console per la regola di avviso. |
| Descrizione | No | Descrizione facoltativa dell'avviso. |
| Severity | Sì | Gravità del record di avviso tra i valori seguenti:<br><br> **Critical**<br>**Warning**<br>**Informational** |


##### <a name="threshold"></a>Soglia
Questa sezione è obbligatoria  e definisce le proprietà della soglia dell'avviso.

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| Operatore | Sì | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Valore | Sì | Valore per il confronto dei risultati. |


##### <a name="metricstrigger"></a>MetricsTrigger
Questa sezione è facoltativa.  Includere la sezione per un avviso di misurazione delle metriche.

> [!NOTE]
> Gli avvisi di misurazione delle metriche sono attualmente disponibili in anteprima pubblica. 

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| TriggerCondition | Sì | Specifica se la soglia riguarda il numero totale di violazioni o le violazioni consecutive, con i valori seguenti:<br><br>**Total<br>Consecutive** |
| Operatore | Sì | Operatore di confronto tra i valori seguenti:<br><br>**gt = maggiore di<br>lt = minore di** |
| Valore | Sì | Numero di volte in cui i criteri devono essere soddisfatti per attivare l'avviso. |

##### <a name="throttling"></a>Limitazione
Questa sezione è facoltativa.  Includere la sezione se si vogliono eliminare gli avvisi generati dalla stessa regola per un determinato intervallo di tempo dopo la creazione di un avviso.

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| DurationInMinutes | Sì, se è incluso l'elemento Throttling | Numero di minuti in cui verranno eliminati gli avvisi dopo che ne è stato creato uno dalla stessa regola di avviso. |

##### <a name="emailnotification"></a>EmailNotification
 Questa sezione è facoltativa. Includere la sezione se si vuole inviare un messaggio di posta elettronica a uno o più destinatari.

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| Destinatari | Sì | Elenco delimitato da virgole di indirizzi di posta elettronica a cui inviare una notifica quando viene creato un avviso, come nell'esempio seguente.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Oggetto | Sì | Riga dell'oggetto del messaggio di posta elettronica. |
| Attachment | No | Gli allegati non sono attualmente supportati.  Se questo elemento è incluso, il valore dovrà essere **None**. |


##### <a name="remediation"></a>Correzione
Questa sezione è facoltativa. Includere la sezione se si vuole avviare un runbook in risposta all'avviso. |

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| RunbookName | Sì | Nome del runbook da avviare. |
| WebhookUri | Sì | URI del webhook per il runbook. |
| Expiry | No | Data e ora di scadenza della correzione. |

#### <a name="webhook-actions"></a>Azioni webhook

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

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--|:--|:--|
| type | Sì | Tipo di azione.  Per le azioni webhook, sarà **Webhook**. |
| name | Sì | Nome visualizzato per l'azione.  Non viene visualizzato nella console. |
| wehookUri | Sì | URI del webhook. |
| customPayload | No | Payload personalizzato da inviare al webhook. Il formato dipenderà dalle previsioni del webhook. |




## <a name="sample"></a>Esempio

Di seguito è riportato un esempio di soluzione che include le risorse seguenti:

- Ricerca salvata
- Pianificazione
- Azione di avviso
- Azione webhook

L'esempio usa variabili dei [parametri di soluzione standard](operations-management-suite-solutions-solution-file.md#parameters) comunemente usate in una soluzione, anziché impostare i valori come hardcoded nelle definizioni delle risorse.

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
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
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
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


Il file di parametri seguente offre valori di esempio per la soluzione.

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
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere viste](operations-management-suite-solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook di automazione e altre risorse](operations-management-suite-solutions-resources-automation.md) alla soluzione di gestione.


