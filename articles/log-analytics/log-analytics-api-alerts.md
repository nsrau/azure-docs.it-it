---
title: Uso dell'API REST degli avvisi di Log Analytics
description: L'API REST degli avvisi di Log Analytics consente di creare e gestire avvisi in Log Analytics, come funzionalità di Log Analytics.  In questo articolo vengono forniti i dettagli dell'API e alcuni esempi per l'esecuzione di diverse operazioni.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 85cf55b4117208266e247316b1050e3988a2ce23
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409153"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Creare e gestire regole di avviso in Log Analytics con l'API REST
L'API REST degli avvisi di Log Analytics consente di creare e gestire avvisi in Log Analytics.  In questo articolo vengono forniti i dettagli dell'API e alcuni esempi per l'esecuzione di diverse operazioni.

L'API REST di ricerca di Log Analytics è RESTful ed è accessibile tramite l'API REST Azure Resource Manager. In questo documento vengono forniti alcuni esempi in cui si accede all'API dalla riga di comando di PowerShell tramite [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento da riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager. L'uso di ARMClient e PowerShell è una delle numerose opzioni di accesso all'API di ricerca di Log Analytics. Con questi strumenti è possibile usare l'API RESTful di Azure Resource Manager per effettuare chiamate alle aree di lavoro di Log Analytics ed eseguire i comandi di ricerca al loro interno. L'API fornirà risultati della ricerca per l'utente in formato JSON, consentendo di usare i risultati della ricerca in molti modi diversi a livello di codice.

## <a name="prerequisites"></a>Prerequisiti
Attualmente, gli avvisi possono essere creati solo con una ricerca salvata in Log Analytics.  Per ulteriori informazioni, fare riferimento all’ [API REST di ricerca log](log-analytics-log-search-api.md) .

## <a name="schedules"></a>Pianificazioni
Una ricerca salvata può avere una o più pianificazioni. La pianificazione definisce la frequenza con cui viene eseguita la ricerca e l'intervallo di tempo in cui vengono identificati i criteri.
Le pianificazioni includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Interval |La frequenza con cui viene eseguita la ricerca. Il valore è espresso in minuti. |
| QueryTimeSpan |L'intervallo di tempo durante il quale vengono valutati i criteri. Deve essere maggiore o uguale a Interval. Il valore è espresso in minuti. |
| Version |La versione API utilizzata.  Attualmente, deve sempre essere impostata su 1. |

Si consideri ad esempio una query eventi con Interval pari a 15 minuti e Timespan pari a 30 minuti. In questo caso, la query viene eseguita ogni 15 minuti e viene attivato un avviso se i criteri continuano a restituire true in un intervallo di 30 minuti.

### <a name="retrieving-schedules"></a>Recupero delle pianificazioni
Utilizzare il metodo Get per recuperare tutte le pianificazioni per una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Usare il metodo Get con l'ID pianificazione per recuperare una determinata pianificazione per una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Di seguito è riportata una risposta di esempio per una pianificazione.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Creazione di una pianificazione
Usare il metodo Put con un ID pianificazione univoco per creare una nuova pianificazione.  Si noti che due pianificazioni non possono avere lo stesso ID anche se sono associate a ricerche diverse.  Quando si crea una pianificazione nella console di Log Analytics, viene creato un GUID per l'ID pianificazione.

> [!NOTE]
> Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Modifica di una pianificazione
Usare il metodo Put con un ID pianificazione esistente per la stessa ricerca salvata per modificare la pianificazione.  Il corpo della richiesta deve includere il valore ETag della pianificazione.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminazione delle pianificazioni
Per eliminare una pianificazione, utilizzare il metodo Delete con un ID pianificazione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Azioni
Una pianificazione può avere più azioni. Un'azione può definire uno o più processi da eseguire, ad esempio l'invio di un messaggio di posta o l'avvio di un runbook o ancora può definire una soglia che determina quando i risultati di una ricerca corrispondono a certi criteri.  Alcune azioni definiranno entrambi, in modo che i processi vengano eseguiti quando viene raggiunta la soglia.

Tutte le azioni includono le proprietà elencate nella tabella seguente.  I vari tipi di avvisi hanno proprietà aggiuntive diverse, che vengono descritte di seguito.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| type |Tipo di azione.  Attualmente, i valori possibili sono Alert e Webhook. |
| NOME |Nome visualizzato per l'avviso. |
| Version |La versione API utilizzata.  Attualmente, deve sempre essere impostata su 1. |

### <a name="retrieving-actions"></a>Recupero delle azioni

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics). Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure, è possibile recuperare o aggiungere azioni usando l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Utilizzare il metodo Get per recuperare tutte le azioni per una pianificazione.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilizzare il metodo Get con l'ID azione per recuperare una determinata azione per una pianificazione.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Creazione o modifica di azioni
Usare il metodo Put con un ID azione univoco per la pianificazione per creare una nuova azione.  Quando si crea un'azione nella console di Log Analytics, un GUID è destinato all'ID azione.

> [!NOTE]
> Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo.

Usare il metodo Put con un ID azione esistente per la stessa ricerca salvata per modificare la pianificazione.  Il corpo della richiesta deve includere il valore ETag della pianificazione.

Il formato della richiesta per la creazione di una nuova azione varia in base al tipo di attività e alcuni esempi sono disponibili nelle sezioni seguenti.

### <a name="deleting-actions"></a>Eliminazione delle azioni

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics). Per gli utenti che scelgono di estendere gli avvisi ad Azure, le azioni vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure, è possibile recuperare o aggiungere azioni usando l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Utilizzare il metodo Delete con l'ID azione per eliminare un’azione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Azioni di avviso
Una pianificazione deve avere una sola azione di avviso.  Le azioni di avviso includono una o più delle sezioni elencate nella tabella seguente.  Ciascuna è descritta in dettaglio di seguito.

| Sezione | DESCRIZIONE | Uso |
|:--- |:--- |:--- |
| Soglia |Criteri di esecuzione dell'azione.| Obbligatoria per ogni avviso, prima o dopo l'estensione ad Azure. |
| Gravità |Etichetta usata per classificare l'avviso quando viene attivato.| Obbligatoria per ogni avviso, prima o dopo l'estensione ad Azure. |
| Elimina |Opzione per arrestare le notifiche dall'avviso. | Facoltativa per ogni avviso, prima o dopo l'estensione ad Azure. |
| Gruppi di azioni |ID del gruppo di azioni di Azure, in cui sono specificate le azioni necessarie, come posta elettronica, SMS, chiamate vocali, webhook, runbook di Automazione, connettori di Gestione dei servizi IT e così via.| Obbligatoria dopo che gli avvisi sono stati estesi ad Azure|
| Customize Actions|Permette di modificare l'output standard per azioni selezionate dal gruppo di azioni| Facoltativa per ogni avviso, può essere usata dopo l'estensione degli avvisi ad Azure. |
| EmailNotification |Inviare messaggi a più destinatari. | Non obbligatoria, se gli avvisi sono stati estesi ad Azure|
| Correzione |Avviare un runbook in Automazione di Azure per tentare di risolvere il problema identificato. |Non obbligatoria, se gli avvisi sono stati estesi ad Azure|
| Azioni webhook | Permette di eseguire il push di dati dagli avvisi al servizio desiderato come JSON |Non obbligatoria, se gli avvisi sono stati estesi ad Azure|

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics).

#### <a name="thresholds"></a>Soglie
Un’azione di avviso deve avere una sola soglia.  Quando i risultati di una ricerca salvata corrispondano alla soglia di un'azione associata a tale ricerca, vengono eseguiti tutti gli altri processi in tale azione.  Inoltre, un'azione può contenere solo una soglia, in modo da poter essere utilizzata con azioni di altri tipi che non contengono soglie.

Le soglie includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Operator |Operatore di confronto soglie. <br> gt = Maggiore di <br> lt = minore di |
| Value |Valore per la soglia. |

Si consideri ad esempio una query eventi con Interval pari a 15 minuti, Timespan pari a 30 minuti e Threshold maggiore di 10. In questo caso, la query viene eseguita ogni 15 minuti e viene attivato un avviso se restituisce 10 eventi creati in un intervallo di 30 minuti.

Di seguito è riportata una risposta di esempio per un'azione con una sola soglia.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di soglia per una pianificazione.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di soglia per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Gravità
Log Analytics permette di classificare gli avvisi in categorie, per semplificare la gestione e la valutazione. La gravità definita per gli avvisi è: Informativo, Avviso e Critico. Queste definizioni corrispondono alla scala di gravità normale degli avvisi di Azure in questo modo:

|Livello di gravità di Log Analytics  |Livello di gravità degli avvisi di Azure  |
|---------|---------|
|Critico |Gravità 0|
|Avviso |Gravità 1|
|Informativo | Gravità 2|

Di seguito è riportata una risposta di esempio per un'azione con solo una soglia e la gravità. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione per una pianificazione con gravità.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di gravità per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Elimina
Log Analytics basato sugli avvisi di query genererà gli avvisi ogni volta che la soglia del tempo viene raggiunta o superata. In base alla logica inclusa nella query, ciò potrebbe comportare un avviso di recupero attivato per una serie di intervalli e di conseguenza l'invio continuativo delle notifiche. Per evitare questo scenario, un utente può impostare l'opzione Elimina che indica a Log Analytics di attendere un intervallo stabilito prima che la notifica venga generata la seconda volta per la regola di avviso. Quindi se l'eliminazione è impostata per 30 minuti, l'avviso verrà generato la prima volta e verrà configurato l'invio di notifiche. Ma attende quindi 30 minuti prima che venga usata di nuovo la notifica della regola di avviso. Nel periodo intermedio la regola di avviso continua l'esecuzione. Log Analytics sopprime la notifica solo per il tempo specificato, indipendentemente da quante volte viene attivata la regola di avviso in questo periodo.

La proprietà di eliminazione della regola di avviso di Log Analytics viene specificata con il valore *Limitazione* usando il valore *DurationInMinutes*.

Di seguito è riportata una risposta di esempio per un'azione con solo una soglia, la gravità e la proprietà di eliminazione

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione per una pianificazione con gravità.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di gravità per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Gruppi di azioni
Tutti gli avvisi in Azure usano un gruppo di azioni come meccanismo predefinito per la gestione delle azioni. Con un gruppo di azioni, è possibile specificare le azioni una volta e quindi associare il gruppo di azioni a più avvisi in Azure. Tutto questo senza la necessità di dichiarare ripetutamente le stesse azioni più volte. I gruppi di azioni supportano più azioni, tra cui posta elettronica, SMS, chiamate vocali, connessioni di Gestione dei servizi IT, runbook di Automazione, URI di webhook e altro ancora. 

Per gli utenti che hanno esteso gli avvisi in Azure, per una pianificazione devono ora essere passati i dettagli del gruppo di azioni insieme alla soglia per poter creare un avviso. I dettagli di posta elettronica, gli URL di webhook, i dettagli relativi all'automazione runbook e altre azioni devono essere definiti all'interno di un gruppo di azioni prima di creare un avviso. È possibile creare un [gruppo di azioni da Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) nel portale o usare l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Per aggiungere un'associazione di un gruppo di azioni a un avviso, specificare l'ID Azure Resource Manager univoco del gruppo di azioni nella definizione dell'avviso. Di seguito viene fornito un esempio:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Usare il metodo Put con un ID azione univoco per associare un gruppo di azioni già esistente per una pianificazione.  Di seguito viene fornito un esempio di utilizzo.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Usare il metodo Put con un ID azione esistente per modificare un gruppo di azioni associato per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizzare le azioni
Per impostazione predefinita, le azioni seguono il modello e il formato standard per le notifiche. Gli utenti possono tuttavia personalizzare alcune azioni, anche se sono controllate da gruppi di azioni. Attualmente, la personalizzazione è possibile per l'oggetto del messaggio di posta elettronica e il payload del webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizzare l'oggetto del messaggio di posta elettronica per il gruppo di azioni
Per impostazione predefinita, l'oggetto del messaggio di posta elettronica per gli avvisi è: Alert Notification <AlertName> for <WorkspaceName>. L'oggetto può essere personalizzato per poter usare parole o tag specifici in modo da impiegare facilmente regole di filtro nella cartella della posta in arrivo. I dettagli dell'intestazione dei messaggi di posta elettronica personalizzati devono essere inviati insieme ai dettagli relativi al gruppo di azioni, come nell'esempio seguente.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Usare il metodo Put con un ID azione univoco per associare un gruppo di azioni già esistente a una personalizzazione per una pianificazione.  Di seguito viene fornito un esempio di utilizzo.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Usare il metodo Put con un ID azione esistente per modificare un gruppo di azioni associato per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizzare il payload del webhook per il gruppo di azioni
Per impostazione predefinita, il webhook inviato tramite il gruppo di azioni per Log Analytics ha una struttura fissa. È tuttavia possibile personalizzare il payload JSON usando variabili specifiche supportate, in modo da soddisfare i requisiti dell'endpoint del webhook. Per altre informazioni, vedere [Azioni webhook per le regole di avviso relative ai log](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

I dettagli di personalizzazione del webhook devono essere inviati insieme a quelli relativi al gruppo di azioni e verranno applicati a tutti gli URI del webhook specificati all'interno del gruppo di azioni, come nell'esempio seguente.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Usare il metodo Put con un ID azione univoco per associare un gruppo di azioni già esistente a una personalizzazione per una pianificazione.  Di seguito viene fornito un esempio di utilizzo.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Usare il metodo Put con un ID azione esistente per modificare un gruppo di azioni associato per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Notifica tramite posta elettronica
Le notifiche tramite posta elettronica inviano i messaggi a uno o più destinatari.  Includono le proprietà elencate nella tabella seguente.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics). Per gli utenti che scelgono di estendere gli avvisi ad Azure, azioni come le notifiche tramite posta elettronica vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure, è possibile recuperare o aggiungere azioni usando l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Recipients |Elenco di indirizzi di posta elettronica. |
| Oggetto |L’oggetto del messaggio. |
| Attachment |Poiché gli allegati non sono attualmente supportati, il valore sarà sempre "None". |

Di seguito è riportata una risposta di esempio per un'azione di notifica di posta elettronica con una determinata soglia.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di posta elettronica per una pianificazione.  Nell'esempio seguente viene creata una notifica tramite posta elettronica con una determinata soglia, per cui il messaggio viene inviato quando i risultati della ricerca salvata superano la soglia.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di posta elettronica per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Azioni correttive
Le correzioni avviano un runbook in Automazione di Azure che tenta di risolvere il problema identificato dall'avviso.  È necessario creare un webhook per il runbook utilizzato in un'azione correttiva e quindi specificare l'URI nella proprietà WebhookUri.  Quando si crea questa azione usando il portale di Azure, viene automaticamente creato un nuovo webhook per il runbook.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics). Per gli utenti che scelgono di estendere gli avvisi ad Azure, azioni come la correzione tramite un runbook vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure, è possibile recuperare o aggiungere azioni usando l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Le correzioni includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| RunbookName |Nome del runbook. Deve corrispondere a un runbook pubblicato nell'account di automazione configurato nella soluzione di automazione nell'area di lavoro di Log Analytics. |
| WebhookUri |URL del webhook. |
| Expiry |La data e l’ora di scadenza del webhook.  Se il webhook non ha una scadenza, questo può essere una data futura valida. |

Di seguito è riportata una risposta di esempio per un'azione correttiva con una determinata soglia.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Usare il metodo Put con un ID azione univoco per creare una nuova azione di correzione per una pianificazione.  Nell'esempio seguente viene creata una correzione con una determinata soglia, per cui il runbook viene avviato quando i risultati della ricerca salvata superano la soglia.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Usare il metodo Put con un ID azione esistente per modificare un'azione di correzione per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Esempio
Di seguito è riportato un esempio completo per creare un nuovo avviso di posta elettronica.  Verrà creata una nuova pianificazione con un'azione contenente una soglia e un messaggio di posta elettronica.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Azioni webhook
Le azioni webhook avviano un processo chiamando un URL e, facoltativamente, fornendo un payload da inviare.  Simili alle azioni correttive, sono destinate a webhook che possono richiamare processi diversi dai runbook di Automazione di Azure.  Hanno inoltre l'opzione aggiuntiva di fornire un payload da recapitare al processo remoto.

> [!NOTE]
> A partire dal 14 maggio 2018, tutti gli avvisi in un'istanza su cloud pubblico dell'area di lavoro di Log Analytics verranno automaticamente estesi ad Azure. L'utente può volontariamente iniziare a estendere gli avvisi ad Azure prima del 14 maggio 2018. Per altre informazioni, vedere [Extend Alerts into Azure from Log Analytics](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Estendere gli avvisi in Azure da Log Analytics). Per gli utenti che scelgono di estendere gli avvisi ad Azure, azioni come quelle relative ai webhook vengono ora controllate nei gruppi di azioni di Azure. Quando un'area di lavoro e i suoi avvisi vengono estesi ad Azure, è possibile recuperare o aggiungere azioni usando l'[API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Le azioni webhook non hanno una soglia, ma devono invece essere aggiunte a una pianificazione che includa un'azione di avviso con una determinata soglia.  

Di seguito è riportato un esempio di risposta per un’azione webhook e un'azione di avviso associata con una determinata soglia.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Creazione o modifica di un’azione webhook
Usare il metodo Put con un ID azione univoco per creare una nuova azione di webhook per una pianificazione.  Nell'esempio seguente viene creata un'azione webhook e un’azione di avviso con una determinata soglia, in modo che il webhook verrà attivato quando i risultati della ricerca salvata supereranno tale soglia.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Usare il metodo Put con un ID azione esistente per modificare un'azione di webhook per una pianificazione.  Il corpo della richiesta deve includere il valore ETag dell'azione.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Passaggi successivi
* Utilizzare l’ [API REST per eseguire ricerche nei log](log-analytics-log-search-api.md) in Log Analytics.
* Per altre informazioni, fare riferimento agli [avvisi di log in Avvisi di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

