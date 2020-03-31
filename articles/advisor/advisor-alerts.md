---
title: Creare avvisi di Azure Advisor per nuove raccomandazioniCreate Azure Advisor alerts for new recommendations
description: Creare avvisi di Azure Advisor per una nuova raccomandazioneCreate Azure Advisor alerts for new recommendation
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443163"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Creare avvisi di Azure Advisor in base alle nuove raccomandazioniCreate Azure Advisor alerts on new recommendations 

Questo articolo illustra come configurare un avviso per i nuovi consigli di Azure Advisor usando il portale di Azure e i modelli di Azure Resource Manager.This article shows you how to set up an alert for new recommendations from Azure Advisor using the Azure portal and Azure Resource Manager templates. 

Ogni volta che Azure Advisor rileva una nuova raccomandazione per una delle risorse, viene archiviato un evento nel log attività di Azure.Whenever Azure Advisor detects a new recommendation for one of your resources, an event is stored in [Azure Activity log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). È possibile configurare avvisi per questi eventi da Azure Advisor usando un'esperienza di creazione degli avvisi specifica per i suggerimenti. È possibile selezionare una sottoscrizione e, facoltativamente, un gruppo di risorse per specificare le risorse per le quali si desidera ricevere avvisi. 

È inoltre possibile determinare i tipi di consigli utilizzando queste proprietà:You can also determine the types of recommendations by using these properties:

* Category
* Livello di impatto
* Tipo di raccomandazione

È inoltre possibile configurare l'azione che verrà eseguita quando viene attivato un avviso da:You can also configure the action that will take place when an alert is triggered by:  

* Selezione di un gruppo di azioni esistente
* Creazione di un nuovo gruppo di azioni

Per ulteriori informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi](../azure-monitor/platform/action-groups.md)di azioni .

> [!NOTE] 
> Gli avvisi di Advisor sono attualmente disponibili solo per i consigli su disponibilità elevata, prestazioni e costi. Le raccomandazioni sulla sicurezza non sono supportate. 

## <a name="in-the-azure-portal"></a>Nel portale di Azure
1. Nel **portale**selezionare **Azure Advisor**.

    ![Azure Advisor nel portale](./media/advisor-alerts/create1.png)

2. Nella sezione **Monitoraggio** del menu a sinistra selezionare **Avvisi**. 

    ![Avvisi in Advisor](./media/advisor-alerts/create2.png)

3. Selezionare **Nuovo avviso advisor**.

    ![Avviso nuovo consulente](./media/advisor-alerts/create3.png)

4. Nella sezione **Ambito** selezionare la sottoscrizione e, facoltativamente, il gruppo di risorse per cui si vuole ricevere un avviso. 

    ![Ambito degli avvisi advisor](./media/advisor-alerts/create4.png)

5. Nella sezione **Condizione** selezionare il metodo che si desidera utilizzare per la configurazione dell'avviso. Se si desidera avvisare per tutti i suggerimenti per una determinata categoria e/o livello di impatto, selezionare **Categoria e livello di impatto**. Se si desidera ricevere un avviso per tutti i suggerimenti di un determinato tipo, selezionare **Tipo di raccomandazione**.

    ![Condizione di avviso di Azure AdvisorAzure Advisor alert condition](./media/advisor-alerts/create5.png)

6. A seconda dell'opzione Configura per selezionata, sarà possibile specificare i criteri. Se si desiderano tutte le raccomandazioni, è sufficiente lasciare vuoti i campi rimanenti. 

    ![Gruppo di azioni di avviso Advisor](./media/advisor-alerts/create6.png)

7. Nella sezione **Gruppi** di azioni selezionare **Aggiungi esistente** per utilizzare un gruppo di azioni già creato oppure **selezionare Crea nuovo** per impostare un nuovo gruppo di [azioni.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 

    ![Avviso advisor aggiungere esistente](./media/advisor-alerts/create7.png)

8. Nella sezione Dettagli avviso assegnare all'avviso un nome e una breve descrizione. Se si desidera che l'avviso sia abilitato, lasciare **l'opzione Abilita regola al momento** della creazione impostata su **Sì**. Selezionare quindi il gruppo di risorse in cui salvare l'avviso. Ciò non influirà sull'ambito di destinazione della raccomandazione. 

    ![Azure Advisor Banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Con un modello di Azure Resource Manager

Questo modello di Resource Manager crea un avviso di raccomandazione e un nuovo gruppo di azioni.

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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configurare gli avvisi di raccomandazione per l'utilizzo di un webhookConfigure recommendation alerts to use a webhook
Questa sezione illustra come configurare gli avvisi di Azure Advisor per l'invio di dati di raccomandazione tramite webhook ai sistemi esistenti. 

È possibile impostare avvisi per ricevere una notifica quando si dispone di un nuovo suggerimento di Advisor su una delle risorse. Questi avvisi possono inviare una notifica tramite posta elettronica o SMS, ma possono anche essere utilizzati per l'integrazione con i sistemi esistenti tramite un webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Utilizzo del payload dell'avviso di raccomandazione di AdvisorUsing the Advisor recommendation alert payload
Se si desidera integrare gli avvisi di Advisor nei propri sistemi usando un webhook, sarà necessario analizzare il payload JSON inviato dalla notifica. 

Quando si imposta il gruppo di azioni per questo avviso, si seleziona se si desidera utilizzare lo schema di avviso comune. Se si seleziona lo schema di avviso comune, il payload sarà simile al seguente:If you select the common alert schema, your payload will look like: 

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

Se non si utilizza lo schema comune, il payload è simile al seguente:If you do not use the common schema, your payload looks like the following: 

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

In entrambi gli schemi è possibile identificare gli `Recommendation` eventi di `Microsoft.Advisor/recommendations/available/action`raccomandazione di Advisor cercando **eventSource** e **operationName** è .

Alcuni degli altri campi importanti che è possibile utilizzare sono: 

* *alertTargetIDs* (nello schema comune) o *resourceId* (schema legacy)
* *raccomandazioneTipo*
* *raccomandazioneNome*
* *raccomandazioneCategoria*
* *raccomandazioneImpatto*
* *raccomandazioneResourceLink*


## <a name="manage-your-alerts"></a>Gestire gli avvisi 

In Azure Advisor è possibile modificare, eliminare o disabilitare e abilitare gli avvisi di avviso. 

1. Nel **portale**selezionare **Azure Advisor**.

    ![Azure Advisor Banner](./media/advisor-alerts/create1.png)

2. Nella sezione **Monitoraggio** del menu a sinistra selezionare **Avvisi**.

    ![Azure Advisor Banner](./media/advisor-alerts/create2.png)

3. Per modificare un avviso, fare clic sul nome dell'avviso per aprire l'avviso e modificare i campi da modificare.

4. Per eliminare, abilitare o disabilitare un avviso, fare clic sull'ellisse alla fine della riga e quindi selezionare l'azione che si desidera eseguire.
 

