---
title: Creare un avviso del log con un modello di Resource Manager
description: Informazioni su come creare un avviso del log usando un modello di Azure Resource Manager e un'API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6ec07d02e61d50aa588d75ba7337eb9237e11207
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402898"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Creare un avviso del log con un modello di Resource Manager
Questo articolo illustra come poter gestire in Azure gli [avvisi del log](monitor-alerts-unified-log.md) su larga scala a livello di codice usando un [modello di Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) tramite [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) e l'[interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). Al momento, Avvisi di Azure supporta gli avvisi del log per le query di [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) e [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Gestione dell'avviso del log in Log Analytics
L'avviso del log di [Log Analytics di Azure](../log-analytics/log-analytics-tutorial-viewdata.md) è integrato nei [nuovi avvisi di Azure](monitoring-overview-unified-alerts.md). Continua a usare le API di Log Analytics, restando compatibile con lo schema usato in precedenza.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'area di lavoro verranno automaticamente estesi ad Azure. Per altre informazioni, vedere [Estendere avvisi da Log Analytics ad Avvisi di Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Gli avvisi del log di Log Analytics vengono creati da regole di avviso che eseguono una ricerca salvata a intervalli regolari. Se i risultati della query corrispondono ai criteri specificati, viene creato un record di avviso e vengono eseguite una o più azioni. 

Il modello di Resource Manager per la ricerca salvata di Log Analytics e gli avvisi di Log Analytics sono disponibili nella sezione Log Analytics della documentazione. Per altre informazioni, vedere [Aggiunta di avvisi e di ricerche salvate di Log Analytics](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md) che include esempi illustrativi e dettagli sullo schema.

### <a name="using-resource-template-via-apipowershell"></a>Uso del modello di Resource Manager tramite API/Powershell
L'API REST degli avvisi di Log Analytics è RESTful ed è accessibile tramite l'API REST di Azure Resource Manager. L'API è quindi accessibile da una riga di comando di PowerShell. Offre i risultati della ricerca in formato JSON per poter essere usati in molti modi diversi a livello di codice.

In [Creare e gestire regole di avviso in Log Analytics con l'API REST](../log-analytics/log-analytics-api-alerts.md) sono disponibili esempi per accedere all'API da Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Gestione dell'avviso del log in Application Insights
Gli avvisi del registro sono stati introdotti in Azure Application Insights come parte dei nuovi avvisi di Azure e sono disponibili in Monitoraggio di Azure. L'esecuzione avviene quindi nell'API di Monitoraggio di Azure come gruppo operazioni REST per le [regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

### <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
L'avviso del log per le risorse di Application Insights ha il tipo `Microsoft.Insights/scheduledQueryRules/`. Per altre informazioni su questo tipo di risorsa, vedere il [riferimento all'API per regole di query pianificate in Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

La struttura riportata di seguito per la [creazione di regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) si basa su modello di risorse e un set di dati di esempio come variabili.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Il campo di tag con il collegamento nascosto alla risorsa di destinazione è obbligatorio se si usa la chiamata API per le [regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) o il modello di risorse. 

Per usare il JSON di esempio in questa procedura dettagliata, è possibile salvarlo con il nome sampleScheduledQueryRule.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Uso del modello di Resource Manager tramite l'interfaccia della riga di comando/Powershell
L'API delle regole di query pianificate in Monitoraggio di Azure è una API REST completamente compatibile con l'API REST di Azure Resource Manager. Può pertanto essere usata con il cmdlet di Resource Manager tramite Powershell e l'interfaccia della riga di comando di Azure.

Di seguito viene illustrato l'utilizzo tramite il cmdlet di Azure Resource Manager in PowerShell per l'esempio di Resource Manager riportato sopra (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Di seguito viene illustrato l'utilizzo tramite il comando di Azure Resource Manager nell'interfaccia della riga di comando di Azure per l'esempio di Resource Manager riportato sopra (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Se l'operazione è stata eseguita correttamente, sarà restituito il valore 201 a indicare la creazione della nuova regola di avviso. Se invece è stata modificata una regola di avviso esistente, sarà restituito il valore 200.


## <a name="next-steps"></a>Passaggi successivi
* Comprendere le [azioni webhook per gli avvisi del log](monitor-alerts-unified-log-webhook.md)
* Leggere altre informazioni sui nuovi [Avvisi di Azure ](monitoring-overview-unified-alerts.md)
* Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md)
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md).   
