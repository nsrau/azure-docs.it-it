---
title: Creare avvisi di Azure Advisor per nuove raccomandazioni utilizzando portale di Azure
description: Creare avvisi di Azure Advisor per nuove raccomandazioni
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 837f12a12e532902ee76e345afa430f758f79c10
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973654"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Creare avvisi di Azure Advisor per nuove raccomandazioni usando il portale di Azure 

Questo articolo illustra come configurare un avviso per nuove raccomandazioni da Azure Advisor usando il portale di Azure. 

Ogni volta che Azure Advisor rileva una nuova raccomandazione per una delle risorse in uso, viene archiviato un evento nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md). È possibile configurare avvisi per questi eventi di Azure Advisor usando un'esperienza di creazione di avvisi specifici delle raccomandazioni. È possibile selezionare una sottoscrizione e, facoltativamente, un gruppo di risorse per specificare le risorse per le quali si vogliono ricevere gli avvisi. 

Si possono anche determinare i tipi di raccomandazioni usando queste proprietà:

* Categoria
* Livello di impatto
* Tipo di raccomandazione

È anche possibile configurare l'azione che verrà eseguita quando un avviso viene attivato in uno dei due modi seguenti:  

* Selezionando un gruppo di azioni esistente
* Creando un nuovo gruppo di azioni

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Gli avvisi di Azure Advisor sono attualmente disponibili solo per le raccomandazioni di tipo Disponibilità elevata, Prestazioni e Costo. Le raccomandazioni sulla sicurezza non sono supportate. 

## <a name="create-alert-rule"></a>Creare una regola di avviso
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

7. Nella sezione **gruppi di azione** selezionare **Aggiungi esistente** per usare un gruppo di azioni già creato oppure selezionare **Crea nuovo** per configurare un nuovo gruppo di [azioni](../azure-monitor/platform/action-groups.md). 

    ![Avviso di Advisor Aggiungi esistente](./media/advisor-alerts/create7.png)

8. Nella sezione Dettagli avviso assegnare un nome e una breve descrizione all'avviso. Se si desidera che l'avviso sia abilitato, lasciare **Attiva regola al momento** della selezione impostata su **Sì**. Quindi selezionare il gruppo di risorse in cui salvare l'avviso. Questa operazione non avrà alcun effetto sull'ambito di destinazione dell'indicazione. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="Screenshot della sezione Dettagli avviso.":::


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

In entrambi gli schemi è possibile identificare gli eventi di raccomandazione di Advisor cercando **EventSource** è `Recommendation` e **OperationName**  è `Microsoft.Advisor/recommendations/available/action` .

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

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Screenshot del menu portale di Azure che mostra Azure Advisor selezionato.":::

2. Nella sezione **monitoraggio** del menu a sinistra selezionare **avvisi**.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Screenshot del menu portale di Azure che Mostra gli avvisi selezionati.":::

3. Per modificare un avviso, fare clic sul nome dell'avviso per aprire l'avviso e modificare i campi che si desidera modificare.

4. Per eliminare, abilitare o disabilitare un avviso, fare clic sui puntini di sospensione alla fine della riga e quindi selezionare l'azione che si desidera eseguire.
 

## <a name="next-steps"></a>Passaggi successivi
- Leggere una [panoramica degli avvisi del log attività](../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).
