---
title: Passare dalla API degli avvisi di Log Analytics a nuova API degli avvisi di Azure
description: Panoramica dell'API e del processo di avviso di Log Analytics basata su Log Analytics legacy basata su Ricerca per passare le regole di avviso alla nuova API ScheduledQueryRules, con dettagli che affrontano i problemi comuni dei clienti.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249438"
---
# <a name="switch-api-preference-for-log-alerts"></a>Modificare la preferenza della API degli avvisi di Log Alerts

> [!NOTE]
> Contenuto dichiarato applicabile solo al cloud pubblico di Azure e non per Azure per enti pubblici o il cloud di Azure per la Cina.Content stated applicable to users Azure public cloud only and **not** for Azure Government or Azure China cloud.  

> [!NOTE]
> Quando un utente sceglie di passare alla nuova [API scheduledQueryRules,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) non è possibile ripristinare l'utilizzo dell'API di [avviso di Log Analytics legacy](api-alerts.md)precedente.

Fino a poco tempo fa le regole degli avvisi venivano gestite nel portale Microsoft Operations Management Suite (OMS). La nuova esperienza avvisi è stata integrata con diversi servizi in Microsoft Azure, tra cui Log Analytics e abbiamo chiesto di [estendere le regole di avviso dal portale OMS ad Azure](alerts-extend.md). Ma per assicurare un'interruzione minima per i clienti, il processo non ha modificato l'interfaccia programmatica per l'uso- [API degli avvisi di Log Analytics](api-alerts.md) basata su SavedSearch.

Ma ora si annuncia una vera alternativa programmatica di Azure per gli utenti degli avvisi di Log Analytics, [Monitoraggio di Azure - API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), anch'esso riportato nella [fatturazione di Azure - per gli avvisi del log](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Per altre informazioni su come gestire gli avvisi del log usando l'API, vedere [Gestione degli avvisi di log tramite Il modello](alerts-log.md#managing-log-alerts-using-azure-resource-template) di risorse di Azure e Gestione degli avvisi di log [tramite PowerShell.To](alerts-log.md#managing-log-alerts-using-powershell)learn more on how to manage your log alerts using the API, see Managing log alerts using Azure Resource Template e Managing log alerts using PowerShell .

## <a name="benefits-of-switching-to-new-azure-api"></a>Vantaggi del passaggio alla nuova API di Azure

Vi sono numerosi vantaggi relativi alla creazione e gestione degli avvisi tramite [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) rispetto a [API legacy degli avvisi di Log Analytics](api-alerts.md); tra i principali:

- Possibilità di [ricercare log tra le aree di lavoro](../log-query/cross-workspace-query.md) nelle regole di avviso e di raggiungere le risorse esterne come ad esempio le aree di lavoro di Log Analytics o anche le app di Application Insights
- Quando vengono usati più campi per creare raggruppamenti nelle query, attraverso [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) l'utente può specificare il campo da aggregare nel portale di Azure
- Gli avvisi di log creati tramite l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) possono avere come periodo definito un massimo di 48 ore e recuperare i dati per un periodo più lungo rispetto al passato
- Creare regole di avviso in un unico passaggio come un'unica risorsa senza la necessità di creare tre livelli di risorse come con [legacy API degli avvisi di Log Analytics](api-alerts.md)
- Unica interfaccia programmatica per tutte le varianti degli avvisi di log basati su query in Azure - la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) può essere usata per gestire le regole per Log Analytics, oltre che per Application Insights
- Gestire gli avvisi di log utilizzando i cmdlet di PowershellManage your log alerts using [Powershell cmdlets](alerts-log.md#managing-log-alerts-using-powershell)
- Tutte le nuove funzionalità di avviso e il futuro sviluppo dell'avviso del log saranno disponibili solo tramite la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo di commutazione dall'API legacy degli avvisi relativi ai log

Gli utenti sono liberi di usare [API legacy degli avvisi di Log Analytics](api-alerts.md) o la nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Le regole di avviso create per ciascuna API saranno *rese gestibili solo dalla stessa API*, oltre che dal portale di Azure. Per impostazione predefinita, Monitoraggio di Azure continuerà a usare l'API di avviso di Log Analytics legacy per la creazione di qualsiasi nuova regola di avviso dal portale di Azure per le aree di lavoro esistenti di Log Analytics.By default, Azure Monitor will continue to use [legacy Log Analytics Alert API](api-alerts.md) for creating any new alert rule from Azure portal for existing workspaces of Log Analytics. Come annunciato nuovo area di lavoro Log creato il 1 giugno 2019 o dopo tale data, utilizzerà automaticamente la nuova API scheduledQueryRules per impostazione predefinita, inclusa nel portale di Azure.As announced new [Log workspace created on or after June 1, 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) - will automatically use new [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) by default including in Azure portal.

Gli effetti della commutazione delle preferenze API scheduledQueryRules sono i seguenti:

- Tutte le interazioni eseguite per la gestione degli avvisi relativi ai log tramite interfacce programmatiche ora devono essere create tramite [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per altre informazioni, vedere Uso [di esempio tramite Modello di risorse](alerts-log.md#managing-log-alerts-using-azure-resource-template) di Azure e uso di [esempio tramite PowerShellFor](alerts-log.md#managing-log-alerts-using-powershell) more information, see, sample use via Azure Resource Template and sample use via PowerShell
- Le nuove regole degli avvisi relativi ai log create nel portale di Azure verranno create solo tramite [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e consentiranno agli utenti di usare [funzionalità aggiuntive della nuova API](#benefits-of-switching-to-new-azure-api) anche tramite il portale di Azure
- La gravità per le regole di avviso del log verrà spostata da: *Critico, Avviso & informativo*, *ai valori di gravità 0, 1 & 2*. Insieme all'opzione per creare/aggiornare le regole di avviso con gravità 3 e 4.

Il processo di estensione degli avvisi da [API legacy degli avvisi di Log Analytics](api-alerts.md) non comporta la modifica della definizione, della query o della configurazione degli avvisi. Le regole di avviso e il monitoraggio non sono interessati e gli avvisi non si fermeranno o verranno bloccati, durante o dopo il passaggio. Le uniche modifiche sono:

- Modifica delle preferenze dell'API e dell'accesso alle regole tramite una nuova API.
- URI di risorsa della regola di avviso modificato contenente gli ID utilizzati nell'API `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`di avviso di Log Analytics [legacy](api-alerts.md) anziché il nome della regola di avviso in questa struttura. Il nome visualizzato della regola di avviso rimarrà invariato.

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

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se la commutazione di tutte le regole di avviso nell'area di lavoro Log Analytics per usare i nuovi [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) viene completata correttamente, verrà fornita la risposta seguente.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Gli utenti possono anche controllare lo stato corrente dell'area di lavoro Log Analytics e vedere se è stata o no modificata per il solo uso di [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per controllare, gli utenti possono eseguire una chiamata GET all'API seguente.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Per eseguire il codice precedente usando la riga di comando di PowerShell con lo strumento [ARMClient](https://github.com/projectkudu/ARMClient), vedere l'esempio seguente.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se l'area di lavoro Log Analytics specificata è stata modificata solo per l'uso di [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), allora la risposta JSON sarà come la seguente.

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
