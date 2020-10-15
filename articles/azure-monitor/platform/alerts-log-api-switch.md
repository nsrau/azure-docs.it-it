---
title: Eseguire l'aggiornamento all'API corrente dei log di monitoraggio di Azure
description: Informazioni su come passare all'API ScheduledQueryRules per gli avvisi di log
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294513"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Eseguire l'aggiornamento all'API per gli avvisi del log corrente dall'API Log Analytics Alert legacy

> [!NOTE]
> Questo articolo è pertinente solo per Azure Public (**non** per Azure Government o Azure China cloud).

> [!NOTE]
> Quando un utente sceglie di modificare la preferenza per l' [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) corrente, non è possibile ripristinare l' [API legacy log Analytics Alert](api-alerts.md)precedente.

In passato, gli utenti usavano l' [API log Analytics Alert legacy](api-alerts.md) per gestire le regole di avviso del log. Le aree di lavoro correnti usano l' [API ScheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Questo articolo descrive i vantaggi e il processo di trasferimento dall'API legacy all'API corrente.

## <a name="benefits"></a>Vantaggi

- Modello singolo per la creazione di regole di avviso (in precedenza erano necessari tre modelli distinti).
- API singola per le aree di lavoro Log Analytics o Application Insights risorse.
- [Supporto](alerts-log.md#managing-log-alerts-using-powershell)per i cmdlet di PowerShell.
- Allineamento dei livelli di gravità con tutti gli altri tipi di avviso.
- Possibilità di creare un [Avviso del log tra aree di lavoro](../log-query/cross-workspace-query.md) che si estende su più risorse esterne, ad esempio log Analytics aree di lavoro o risorse Application Insights.
- Gli utenti possono specificare le dimensioni per suddividere gli avvisi utilizzando il parametro ' aggregate on '.
- Gli avvisi del log hanno un periodo di tempo prolungato di un massimo di due giorni di dati (in precedenza limitato a un giorno).

## <a name="impact"></a>Impatto

- Tutte le nuove regole devono essere create o modificate con l'API corrente. Vedere l' [esempio di utilizzo tramite il modello di risorsa di Azure](alerts-log-create-templates.md) e l'uso di [esempio tramite PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Poiché le regole diventano Azure Resource Manager risorse rilevate nell'API corrente e devono essere univoche, l'ID risorsa regole verrà modificato in questa struttura: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . I nomi visualizzati della regola di avviso rimarranno invariati.

## <a name="process"></a>Processo

Il processo di cambio non è interattivo e non richiede passaggi manuali, nella maggior parte dei casi. Le regole di avviso non vengono arrestate o bloccate, durante o dopo l'opzione.
Eseguire questa chiamata per cambiare tutte le regole di avviso associate all'area di lavoro Log Analytics specifica:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Con il corpo della richiesta contenente il codice JSON seguente:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Di seguito è riportato un esempio di uso di [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source, che semplifica il richiamo della chiamata API precedente:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se l'opzione ha esito positivo, la risposta è:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Controllare lo stato di spostamento dell'area di lavoro

È anche possibile usare questa chiamata API per controllare lo stato dell'opzione:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

È anche possibile usare lo strumento [ARMClient](https://github.com/projectkudu/ARMClient) :

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se l'area di lavoro Log Analytics è stata impostata sull' [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules), la risposta è:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Se l'area di lavoro Log Analytics non è stata cambiata, la risposta è:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli [Avvisi del log - Monitoraggio di Azure](alerts-unified-log.md).
- Informazioni su come [gestire gli avvisi del log usando l'API](alerts-log-create-templates.md).
- Informazioni su come [gestire gli avvisi del log tramite PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Altre informazioni sull'[esperienza di Avvisi di Azure](./alerts-overview.md).
