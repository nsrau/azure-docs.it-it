---
title: Eseguire la migrazione di avvisi di Azure su eventi di gestione in avvisi del log attività
description: A partire dal 1° ottobre verranno rimossi gli avvisi relativi agli eventi di gestione. È necessario quindi prepararsi e migrare gli avvisi esistenti.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: fb54e11c9da6bec2a1e0354317df6343140cbf09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60255917"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Eseguire la migrazione di avvisi di Azure su eventi di gestione in avvisi del log attività

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> A partire dal 1° ottobre verranno disattivati gli avvisi relativi agli eventi di gestione. Usare le istruzioni seguenti per capire se si hanno questi avvisi ed eventualmente eseguirne la migrazione.

## <a name="what-is-changing"></a>Cosa cambierà

Monitoraggio di Azure (in precedenza Azure Insights) offriva una funzionalità per la creazione di un avviso che attivasse eventi di gestione e generasse notifiche all'URL di un webhook o a un indirizzo di posta elettronica. È possibile aver creato uno di questi avvisi in uno dei modi seguenti:
* Nel portale di Azure per determinati tipi di risorsa, in Monitoraggio -> Avvisi -> Aggiungi avviso, impostando "Avviso per" su "Eventi"
* Eseguendo il cmdlet di PowerShell Add-AzLogAlertRule
* Usando direttamente l'[API REST per gli avvisi](https://docs.microsoft.com/rest/api/monitor/alertrules) con odata.type = "ManagementEventRuleCondition" e dataSource.odata.type = "RuleManagementEventDataSource"
 
Lo script di PowerShell seguente restituisce un elenco di tutti gli avvisi relativi ad eventi di gestione presenti nella sottoscrizione personale, oltre alle condizioni impostate su ogni avviso.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Se non sono presenti avvisi relativi ad eventi di gestione, il cmdlet di PowerShell restituirà una serie di messaggi di avviso simili al seguente:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Questi messaggi di avviso possono essere ignorati. Se invece sono presenti avvisi relativi ad eventi di gestione, l'output del cmdlet di PowerShell sarà simile al seguente:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Gli avvisi sono separati tra loro da una linea tratteggiata e includono una serie di dettagli, tra cui l'ID risorsa dell'avviso e la regola specifica da monitorare.

Questa funzionalità è stata integrata negli [avvisi del log attività di Monitoraggio di Azure](../../azure-monitor/platform/activity-log-alerts.md). Questi nuovi avvisi consentono di impostare una condizione sugli eventi del log attività e di ricevere una notifica nel momento in cui un nuovo evento soddisfa la condizione definita. Gli avvisi sugli eventi di gestione presentano anche una serie di miglioramenti:
* È possibile riusare il gruppo di destinatari di una notifica ("azioni") anche in altri avvisi tramite [Gruppi di azioni](../../azure-monitor/platform/action-groups.md), in modo da ridurre la necessità di definire ogni volta gli utenti che devono ricevere un avviso.
* È possibile ricevere una notifica direttamente sul telefono tramite SMS con la funzionalità Gruppi di azioni.
* È possibile [creare avvisi del log attività con i modelli di Resource Manager](../../azure-monitor/platform/alerts-activity-log.md).
* È possibile creare condizioni più flessibili e complesse per soddisfare esigenze specifiche.
* Le notifiche vengono recapitate più rapidamente.
 
## <a name="how-to-migrate"></a>Come eseguire la migrazione
 
Per creare un nuovo avviso del log attività, è possibile:
* Seguire le [istruzioni su come creare un avviso nel portale di Azure](../../azure-monitor/platform/activity-log-alerts.md)
* Imparare a [creare un avviso usando un modello di Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
 
Gli avvisi di eventi di gestione creati in precedenza non verranno automaticamente migrati in avvisi del log attività. Usando lo script di PowerShell precedente, è necessario invece elencare gli avvisi di eventi di gestione attualmente configurati e ricrearli manualmente come avvisi del log attività. Questa operazione deve essere eseguita entro il 1° ottobre. A partire da quella data, infatti, gli avvisi di eventi di gestione non saranno più visibili nella sottoscrizione di Azure. Altri tipi di avvisi di Azure, inclusi gli avvisi delle metriche di monitoraggio di Azure, gli avvisi di Application Insights e avvisi di ricerca nei Log sono interessati da questa modifica. Per eventuali domande, aggiungerle ai commenti al termine dell'articolo.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [log attività](../../azure-monitor/platform/activity-logs-overview.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](../../azure-monitor/platform/activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
* Esaminare lo [schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Altre informazioni sulle [notifiche del servizio](../../azure-monitor/platform/service-notifications.md)
* Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md)

