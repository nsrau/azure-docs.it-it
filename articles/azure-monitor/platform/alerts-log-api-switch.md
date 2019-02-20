---
title: Passare dalla API degli avvisi di Log Analytics a nuova API degli avvisi di Azure
description: Panoramica di ritiro di savedSearch legacy basata su API di avviso di Log Analytics e processo per passare dalle regole di avviso alla nuova API ScheduledQueryRules, con dettagli in riferimento alle preoccupazioni dei clienti.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 879a91d7007057e577631e157dae71f1566acab6
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118225"
---
# <a name="switch-api-preference-for-log-alerts"></a>Modificare la preferenza della API degli avvisi di Log Alerts

> [!NOTE]
> Contenuto indicato **non** applicabile agli utenti di Azure GovCloud e solo per gli utenti del cloud pubblico di Azure.  

Fino a poco tempo fa le regole degli avvisi venivano gestite nel portale Microsoft Operations Management Suite (OMS). La nuova esperienza avvisi è stata integrata con diversi servizi in Microsoft Azure, tra cui Log Analytics e abbiamo chiesto di [estendere le regole di avviso dal portale OMS ad Azure](alerts-extend.md). Ma per assicurare un'interruzione minima per i clienti, il processo non ha modificato l'interfaccia programmatica per l'uso- [API degli avvisi di Log Analytics](api-alerts.md) basata su SavedSearch.

Ma ora si annuncia una vera alternativa programmatica di Azure per gli utenti degli avvisi di Log Analytics, [Monitoraggio di Azure - API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), anch'esso riportato nella [fatturazione di Azure - per gli avvisi del log](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Per altre informazioni su come gestire gli avvisi di log tramite API, vedere [Gestione degli avvisi del log tramite il modello di risorsa di Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [Gestione degli avvisi del log tramite PowerShell, CLI o API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Vantaggi del passaggio alla nuova API di Azure

Vi sono numerosi vantaggi relativi alla creazione e gestione degli avvisi tramite [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) rispetto a [API legacy degli avvisi di Log Analytics](api-alerts.md); tra i principali:

- Possibilità di [ricercare log tra le aree di lavoro](../log-query/cross-workspace-query.md) nelle regole di avviso e di raggiungere le risorse esterne come ad esempio le aree di lavoro di Log Analytics o anche le app di Application Insights
- Quando vengono usati più campi per creare raggruppamenti nelle query, attraverso [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) l'utente può specificare il campo da aggregare nel portale di Azure
- Gli avvisi di log creati tramite l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) possono avere come periodo definito un massimo di 48 ore e recuperare i dati per un periodo più lungo rispetto al passato
- Creare regole di avviso in un unico passaggio come un'unica risorsa senza la necessità di creare tre livelli di risorse come con [legacy API degli avvisi di Log Analytics](api-alerts.md)
- Unica interfaccia programmatica per tutte le varianti degli avvisi di log basati su query in Azure - la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) può essere usata per gestire le regole per Log Analytics, oltre che per Application Insights
- Tutte le nuove funzionalità di avviso e il futuro sviluppo dell'avviso del log saranno disponibili solo tramite la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo di commutazione dall'API legacy degli avvisi relativi ai log

Il processo di estensione degli avvisi da [API legacy degli avvisi di Log Analytics](api-alerts.md) non comporta la modifica della definizione, della query o della configurazione degli avvisi. Gli utenti sono liberi di usare [API legacy degli avvisi di Log Analytics](api-alerts.md) o la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Le regole di avviso create per ciascuna API saranno *rese gestibili solo dalla stessa API*, oltre che dal portale di Azure. Per impostazione predefinita, il Monitoraggio di Azure continuerà a usare [API legacy degli avvisi di Log Analytics](api-alerts.md) per la creazione di qualsiasi nuova regola di avviso dal portale di Azure.

Gli effetti della commutazione delle preferenze API scheduledQueryRules sono i seguenti:

- Tutte le interazioni eseguite per la gestione degli avvisi relativi ai log tramite interfacce programmatiche ora devono essere create tramite [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per altre informazioni, vedere [l'uso di esempio di Modello di risorsa di Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [l'uso di esempio tramite PowerShell o interfaccia della riga di comando di Azure](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Le nuove regole degli avvisi relativi ai log create nel portale di Azure verranno create solo tramite [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e consentiranno agli utenti di usare [funzionalità aggiuntive della nuova API](#Benefits-of-switching-to-new-Azure-API) anche tramite il portale di Azure

Qualsiasi cliente che desideri passare volontariamente al nuovo [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloccare l'uso dalla [API legacy degli avvisi relativi a Log Analytics](api-alerts.md) può farlo effettuando una chiamata PUT sull'API per cambiare tutte le regole relative agli avvisi associate all'area di lavoro specifica di Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

In caso di un corpo della richiesta contenente il codice JSON seguente.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

È possibile accedere all'API anche dalla riga di comando di PowerShell tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source che semplifica la chiamata all'API di Azure Resource Manager. Come mostrato di seguito, nella chiamata PUT di esempio viene usato lo strumento ARMclient per cambiare tutte le regole relative agli avvisi associate all'area di lavoro specifica di Log Analytics.

```PowerShell
$switchJSON = {'scheduledQueryRulesEnabled': 'true'}
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se la commutazione di tutte le regole di avviso nell'area di lavoro di Log Analytics per usare i nuovi [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) viene completata correttamente, verrà fornita la risposta seguente.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Gli utenti possono anche controllare lo stato corrente dell'area di lavoro di Log Analytics e vedere se è stata o no modificata per il solo uso di [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per controllare, gli utenti possono eseguire una chiamata GET all'API seguente.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Per eseguire il codice precedente usando la riga di comando di PowerShell con lo strumento [ARMClient](https://github.com/projectkudu/ARMClient), vedere l'esempio seguente.

```PowerShell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se l'area di lavoro di Log Analytics specificata è stata modificata solo per l'uso di [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), allora la risposta JSON sarà come la seguente.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Altrimenti, se l'area di lavoro di Log Analytics specificata non è ancora stata modificata per il solo uso di [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), allora la risposta JSON sarà come la seguente.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli [Avvisi del log - Monitoraggio di Azure](alerts-unified-log.md).
- Informazioni su come creare [avvisi del log in Avvisi di Azure](alerts-log.md).
- Altre informazioni sull'[esperienza di Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
