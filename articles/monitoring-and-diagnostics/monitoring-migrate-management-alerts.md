---
title: "Eseguire la migrazione di avvisi di Azure su eventi di gestione in avvisi del log attività | Microsoft Docs"
description: "A partire dal 1° ottobre verranno rimossi gli avvisi relativi agli eventi di gestione. È necessario quindi prepararsi e migrare gli avvisi esistenti."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Eseguire la migrazione di avvisi di Azure su eventi di gestione in avvisi del log attività


> [!WARNING]
> A partire dal 1° ottobre verranno disattivati gli avvisi relativi agli eventi di gestione. Usare le istruzioni seguenti per capire se si hanno questi avvisi ed eventualmente eseguirne la migrazione.
>
> 

## <a name="what-is-changing"></a>Cosa cambierà

Monitoraggio di Azure (in precedenza Azure Insights) offriva una funzionalità per la creazione di un avviso che attivasse eventi di gestione e generasse notifiche all'URL di un webhook o a un indirizzo di posta elettronica. È possibile aver creato uno di questi avvisi in uno dei modi seguenti:
* Nel portale di Azure per determinati tipi di risorsa, in Monitoraggio -> Avvisi -> Aggiungi avviso, impostando "Avviso per" su "Eventi"
* Eseguendo il cmdlet di PowerShell Add-AzureRmLogAlertRule
* Usando direttamente l'[API REST per gli avvisi](http://docs.microsoft.com/rest/api/monitor/alertrules) con odata.type = "ManagementEventRuleCondition" e dataSource.odata.type = "RuleManagementEventDataSource"
 
Lo script di PowerShell seguente restituisce un elenco di tutti gli avvisi relativi ad eventi di gestione presenti nella sottoscrizione personale, oltre alle condizioni impostate su ogni avviso.

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
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

Questa funzionalità è stata integrata negli [avvisi del log attività di Monitoraggio di Azure](monitoring-activity-log-alerts.md). Questi nuovi avvisi consentono di impostare una condizione sugli eventi del log attività e di ricevere una notifica nel momento in cui un nuovo evento soddisfa la condizione definita. Gli avvisi sugli eventi di gestione presentano anche una serie di miglioramenti:
* È possibile riusare il gruppo di destinatari di una notifica ("azioni") anche in altri avvisi tramite [Gruppi di azioni](monitoring-action-groups.md), in modo da ridurre la necessità di definire ogni volta gli utenti che devono ricevere un avviso.
* È possibile ricevere una notifica direttamente sul telefono tramite SMS con la funzionalità Gruppi di azioni.
* È possibile [creare avvisi del log attività con i modelli di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* È possibile creare condizioni più flessibili e complesse per soddisfare esigenze specifiche.
* Le notifiche vengono recapitate più rapidamente.
 
## <a name="how-to-migrate"></a>Come eseguire la migrazione
 
Per creare un nuovo avviso del log attività, è possibile:
* Seguire le [istruzioni su come creare un avviso nel portale di Azure](monitoring-activity-log-alerts.md)
* Imparare a [creare un avviso usando un modello di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Gli avvisi di eventi di gestione creati in precedenza non verranno automaticamente migrati in avvisi del log attività. Usando lo script di PowerShell precedente, è necessario invece elencare gli avvisi di eventi di gestione attualmente configurati e ricrearli manualmente come avvisi del log attività. Questa operazione deve essere eseguita entro il 1° ottobre. A partire da quella data, infatti, gli avvisi di eventi di gestione non saranno più visibili nella sottoscrizione di Azure. Altri tipi di avvisi di Azure, tra cui gli avvisi metrica di Monitoraggio di Azure, gli avvisi di Application Insights e gli avvisi di Log Analytics, non sono interessati da questa modifica. Per eventuali domande, aggiungerle ai commenti al termine dell'articolo.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [log attività](monitoring-overview-activity-logs.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](monitoring-activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
* Altre informazioni sulle [notifiche del servizio](monitoring-service-notifications.md)
* Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)
