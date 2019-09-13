---
title: Creare avvisi Azure Advisor per le nuove raccomandazioni | Microsoft Docs
description: Crea avvisi Azure Advisor per la nuova raccomandazione
services: advisor
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2019
ms.author: sagupt
ms.openlocfilehash: a67034752b4c43533a5735b857186ee83934717a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935638"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Crea avvisi Azure Advisor per le nuove raccomandazioni 

Questo articolo illustra come configurare un avviso per nuove raccomandazioni da Azure Advisor usando i modelli portale di Azure e Azure Resource Manager. 

Ogni volta che Azure Advisor rileva una nuova raccomandazione per una delle risorse, un evento viene archiviato nel [log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). È possibile configurare avvisi per questi eventi da Azure Advisor usando un'esperienza di creazione di avvisi specifica della raccomandazione. È possibile selezionare una sottoscrizione e, facoltativamente, un gruppo di risorse per specificare le risorse su cui si desidera ricevere gli avvisi. 

È anche possibile determinare i tipi di raccomandazioni usando queste proprietà:

* Category
* Livello di effetto
* Tipo di raccomandazione

È anche possibile configurare l'azione che verrà eseguita quando un avviso viene attivato da:  

* Selezione di un gruppo di azioni esistente
* Creazione di un nuovo gruppo di azioni

Per ulteriori informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azioni] (.. /azure-monitor/platform/action-groups.md.

> [!NOTE] 
> Gli avvisi di Advisor sono attualmente disponibili solo per i consigli relativi a disponibilità elevata, prestazioni e costi. Le raccomandazioni sulla sicurezza non sono supportate. 

## <a name="in-the-azure-portal"></a>Nel portale di Azure
1. Nel **portale**selezionare **Azure Advisor**.

    ![Azure Advisor nel portale](./media/advisor-alerts/create1.png)

2. Nella sezione **monitoraggio** del menu a sinistra selezionare **avvisi**. 

    ![Avvisi in Advisor](./media/advisor-alerts/create2.png)

3. Selezionare **nuovo avviso di Advisor**.

    ![Avviso nuovo Advisor](./media/advisor-alerts/create3.png)

4. Nella sezione **ambito** selezionare la sottoscrizione e, facoltativamente, il gruppo di risorse in cui si desidera ricevere un avviso. 

    ![Ambito avvisi Advisor](./media/advisor-alerts/create4.png)

5. Nella sezione **condizione** selezionare il metodo che si vuole usare per la configurazione dell'avviso. Se si desidera ricevere un avviso per tutte le raccomandazioni per una determinata categoria e/o livello di effetto, selezionare **categoria e livello di**interesse. Se si desidera ricevere un avviso per tutte le raccomandazioni di un determinato tipo, selezionare **tipo di raccomandazione**.

    ![Azure Advisor condizione di avviso](./media/advisor-alerts/create5.png)

6. A seconda dell'opzione Configura per selezionata, sarà possibile specificare i criteri. Se si desiderano tutte le raccomandazioni, lasciare vuoti i campi rimanenti. 

    ![Gruppo di azione avvisi di Advisor](./media/advisor-alerts/create6.png)

7. Nella sezione **gruppi di azione** selezionare **Aggiungi esistente** per usare un gruppo di azioni già creato oppure selezionare **Crea nuovo** per configurare un nuovo gruppo di [azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Avviso di Advisor Aggiungi esistente](./media/advisor-alerts/create7.png)

8. Nella sezione Dettagli avviso assegnare un nome e una breve descrizione all'avviso. Se si desidera che l'avviso sia abilitato, lasciare **Attiva regola al momento** della selezione impostata su **Sì**. Quindi selezionare il gruppo di risorse in cui salvare l'avviso. Questa operazione non avrà alcun effetto sull'ambito di destinazione dell'indicazione. 

    ![Banner Azure Advisor](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Con un modello di Azure Resource Manager

Questo modello di Gestione risorse crea un avviso di raccomandazione e un nuovo gruppo di azioni.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configurare gli avvisi di raccomandazione per l'uso di un webhook
Questa sezione illustra come configurare Azure Advisor avvisi per inviare i dati di raccomandazione tramite webhook ai sistemi esistenti. 

È possibile configurare avvisi per ricevere notifiche quando si dispone di una nuova raccomandazione di Advisor su una delle risorse. Questi avvisi possono ricevere notifiche tramite posta elettronica o SMS, ma possono anche essere usati per l'integrazione con i sistemi esistenti tramite un webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Utilizzo del payload degli avvisi di consigli di Advisor
Se si vuole integrare gli avvisi di Advisor nei propri sistemi usando un webhook, è necessario analizzare il payload JSON inviato dalla notifica. 

Quando si configura il gruppo di azioni per questo avviso, è necessario selezionare se si desidera utilizzare lo schema di avviso comune. Se si seleziona lo schema di avviso comune, il payload sarà simile al seguente: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Se non si usa lo schema comune, il payload ha un aspetto simile al seguente: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

In entrambi gli schemi è possibile identificare gli eventi di raccomandazione di Advisor cercando EventSource `Recommendation` è e **OperationName** è `Microsoft.Advisor/recommendations/available/action`.

Di seguito sono riportate alcune delle altre importanti campi che è possibile usare: 

* *alertTargetIDs* (nello schema comune) o *resourceId* (schema legacy)
* *recommendationType*
* *recommendname*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Gestire gli avvisi 

Da Azure Advisor è possibile modificare, eliminare o disabilitare e abilitare gli avvisi di raccomandazioni. 

1. Nel **portale**selezionare **Azure Advisor**.

    ![Banner Azure Advisor](./media/advisor-alerts/create1.png)

2. Nella sezione **monitoraggio** del menu a sinistra selezionare **avvisi**.

    ![Banner Azure Advisor](./media/advisor-alerts/create2.png)

3. Per modificare un avviso, fare clic sul nome dell'avviso per aprire l'avviso e modificare i campi che si desidera modificare.

4. Per eliminare, abilitare o disabilitare un avviso, fare clic sui puntini di sospensione alla fine della riga e quindi selezionare l'azione che si desidera eseguire.
 

