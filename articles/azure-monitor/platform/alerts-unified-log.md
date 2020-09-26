---
title: Avvisi del log in Monitoraggio di Azure
description: Attivare messaggi di posta elettronica, notifiche, chiamare URL di siti Web (webhook) o automazione quando viene soddisfatta la condizione di query di log specificata
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 8081c60833c3c02d55ae66ca695ba106dba01450
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294139"
---
# <a name="log-alerts-in-azure-monitor"></a>Avvisi del log in Monitoraggio di Azure

## <a name="overview"></a>Panoramica

Gli avvisi del log sono uno dei tipi di avviso supportati negli [avvisi di Azure](./alerts-overview.md). Gli avvisi del log consentono agli utenti di usare una query [log Analytics](../log-query/get-started-portal.md) per valutare le risorse registra ogni frequenza impostata e generare un avviso in base ai risultati. Le regole possono attivare una o più azioni utilizzando i [gruppi di azioni](./action-groups.md).

> [!NOTE]
> I dati di log da un' [area di lavoro log Analytics](../log-query/get-started-portal.md) possono essere inviati all'archivio delle metriche di monitoraggio di Azure. Gli avvisi di metrica hanno un [comportamento diverso](alerts-metric-overview.md), che può essere più utile a seconda dei dati in uso. Per informazioni su cosa e come è possibile indirizzare i log alle metriche, vedere [avviso di metrica per i log](alerts-metric-logs.md).

> [!NOTE]
> Non sono attualmente previsti addebiti aggiuntivi per la versione dell'API `2020-05-01-preview` e gli avvisi del log incentrato sulle risorse.  I prezzi per le funzionalità in anteprima verranno annunciati in futuro e un avviso fornito prima di iniziare la fatturazione. Se si sceglie di continuare a usare la nuova versione dell'API e gli avvisi del log incentrato sulle risorse dopo il periodo di preavviso, l'addebito sarà addebitato alla tariffa applicabile.

## <a name="prerequisites"></a>Prerequisiti

Gli avvisi del log eseguono query sui dati Log Analytics. Innanzitutto è necessario iniziare a [raccogliere i dati di log](resource-logs.md) ed eseguire query sui dati di log per individuare eventuali problemi. È possibile usare l' [argomento degli esempi di query di avviso](../log-query/saved-queries.md) in log Analytics per comprendere cosa è possibile individuare o iniziare [a scrivere una query personalizzata](../log-query/get-started-portal.md).

Il [collaboratore di monitoraggio di Azure](./roles-permissions-security.md) è un ruolo comune necessario per la creazione, la modifica e l'aggiornamento degli avvisi del log. Sono necessari anche i diritti di esecuzione delle query & di accesso per i log delle risorse. L'accesso parziale ai log delle risorse può non riuscire a eseguire query o restituire risultati parziali. [Altre informazioni sulla configurazione degli avvisi di log in Azure](./alerts-log.md).

> [!NOTE]
> Avvisi di log per Log Analytics utilizzati per la gestione tramite l' [API log Analytics Alert](api-alerts.md)legacy. [Altre informazioni sul trasferimento all'API ScheduledQueryRules corrente](alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definizione di valutazione della query

La definizione della condizione delle regole di ricerca log inizia da:

- Quale query eseguire?
- Come si usano i risultati?

Le sezioni seguenti descrivono i diversi parametri che è possibile usare per impostare la logica precedente.

### <a name="log-query"></a>Query di log
Query [log Analytics](../log-query/get-started-portal.md) utilizzata per valutare la regola. I risultati restituiti da questa query vengono usati per determinare se è necessario attivare un avviso. È possibile definire l'ambito della query per:

- Una risorsa specifica, ad esempio una macchina virtuale.
- Una risorsa su scala, ad esempio una sottoscrizione o un gruppo di risorse.
- Più risorse che usano [query tra risorse](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Le query di avviso presentano vincoli per garantire prestazioni ottimali e la pertinenza dei risultati. [Altre informazioni](./alerts-log-query.md)sono disponibili qui.

> [!IMPORTANT]
> Le query incentrate sulle risorse e [tra risorse](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) sono supportate solo usando l'API scheduledQueryRules corrente. Se si usa l' [API log Analytics Alert](api-alerts.md)legacy, sarà necessario passare a. [Altre informazioni sul cambio](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Intervallo di tempo query

L'intervallo di tempo è impostato nella definizione della condizione della regola. Nelle aree di lavoro e in Application Insights, viene chiamato **period**. In tutti gli altri tipi di risorse, viene chiamato **override intervallo di tempo query**.

Analogamente a log Analytics, l'intervallo di tempo limita i dati delle query all'intervallo specificato. Anche se nella query viene usato il comando **fa** , verrà applicato l'intervallo di tempo.

Una query, ad esempio, analizza 60 minuti, quando l'intervallo di tempo è di 60 minuti, anche se il testo contiene **ago (1D)**. È necessario che l'intervallo di tempo e il filtro del tempo di query corrispondano. Nel caso di esempio, la modifica **Period**dell'  /  **intervallo di tempo della query di sostituzione** del periodo su un giorno, funziona come previsto.

### <a name="measure"></a>Measure

Gli avvisi del log consentono di accedere a valori numerici che possono essere valutati. È possibile misurare due elementi diversi:

#### <a name="count-of-the-results-table-rows"></a>Conteggio delle righe della tabella dei risultati

Il conteggio dei risultati è la misura predefinita. Ideale per l'utilizzo di eventi quali i registri eventi di Windows, syslog, le eccezioni delle applicazioni. Viene attivato quando i record del log si verificano o non vengono effettuati nell'intervallo di tempo valutato.

Gli avvisi del log funzionano meglio quando si tenta di rilevare i dati nel log. Funziona meno bene quando si tenta di rilevare la mancanza di dati nei log. Ad esempio, avvisi sull'heartbeat della macchina virtuale.

Per le aree di lavoro e Application Insights, viene chiamato **in base** al **numero di risultati della**selezione. In tutti gli altri tipi di risorse, viene chiamato **Measure** con le **righe della tabella**di selezione.

> [!NOTE]
> Poiché i log sono dati semi-strutturati, sono intrinsecamente più latenti della metrica, è possibile che si verifichino problemi durante il tentativo di rilevare la mancanza di dati nei log ed è consigliabile usare gli [avvisi delle metriche](alerts-metric-overview.md). È possibile inviare i dati all'archivio delle metriche dai log usando gli [avvisi delle metriche per i log](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Esempio di caso di utilizzo conteggio righe tabella risultati

Si vuole essere a conoscenza del momento in cui l'applicazione ha risposto con il codice di errore 500 (errore interno del server). Si deve creare una regola di avviso con i dettagli seguenti:

- **Query:** 

```Kusto
requests
| where resultCode == "500"
```

- **Periodo di tempo:** 15 minuti
- **Frequenza di avviso:** 15 minuti
- **Valore di soglia:** maggiore di 0

Le regole di avviso monitorano quindi le richieste che terminano con il codice di errore 500. La query viene eseguita ogni 15 minuti negli ultimi 15 minuti. Se viene trovato anche un record, viene attivato l'avviso e vengono attivate le azioni configurate.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Calcolo della misura in base a una colonna numerica (ad esempio valore del contatore CPU)

Per le aree di lavoro e Application Insights, viene chiamato **in base** alla **misurazione della metrica**di selezione. In tutti gli altri tipi di risorse, viene chiamato **Measure** con la selezione di un nome di colonna numerico.

### <a name="aggregation-type"></a>Tipo di aggregazione

Calcolo eseguito su più record per aggregarli a un valore numerico. Ad esempio:
- **Count** restituisce il numero di record nella query
- **Average** restituisce la media della [**granularità di aggregazione**](#aggregation-granularity) delle colonne di misura definita.

Nelle aree di lavoro e Application Insights è supportato solo nel tipo di misura **metrica misurazione** . Il risultato della query deve contenere una colonna denominata AggregatedValue che fornisce un valore numerico dopo un'aggregazione definita dall'utente. In tutti gli altri tipi di risorse, il **tipo di aggregazione** è selezionato dal campo di tale nome.

### <a name="aggregation-granularity"></a>Granularità di aggregazione

Determina l'intervallo utilizzato per aggregare più record a un valore numerico. Se, ad esempio, sono stati specificati **5 minuti**, i record vengono raggruppati per intervalli di 5 minuti utilizzando il **tipo di aggregazione** specificato.

Nelle aree di lavoro e Application Insights è supportato solo nel tipo di misura **metrica misurazione** . Il risultato della query deve contenere [bin ()](/azure/kusto/query/binfunction) che imposta l'intervallo nei risultati della query. In tutti gli altri tipi di risorse, il campo che controlla questa impostazione viene definito **granularità di aggregazione**.

> [!NOTE]
> Poiché [bin ()](/azure/kusto/query/binfunction) può produrre intervalli di tempo non uniformi, il servizio avvisi convertirà automaticamente la funzione [bin ()](/azure/kusto/query/binfunction) in [bin_at funzione ()](/azure/kusto/query/binatfunction) con il tempo appropriato in fase di esecuzione, per garantire i risultati con un punto fisso.

### <a name="split-by-alert-dimensions"></a>Dividi per dimensioni avviso

Suddividere gli avvisi per numero o colonne stringa in avvisi distinti raggruppando in combinazioni univoche. Quando si creano avvisi incentrati sulle risorse su larga scala (ambito di sottoscrizione o di gruppo di risorse), è possibile suddividerli in base alla colonna ID risorsa di Azure. La suddivisione nella colonna ID risorsa di Azure consente di modificare la destinazione dell'avviso sulla risorsa specificata.

Nelle aree di lavoro e Application Insights è supportato solo nel tipo di misura **metrica misurazione** . Il campo è denominato **Aggregate in**. È limitato a tre colonne. La presenza di più di tre gruppi per colonne nella query può causare risultati imprevisti. In tutti gli altri tipi di risorse, è configurato nella sezione **Split by Dimensions** della condizione (limitata a sei divisioni).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Esempio di suddivisione in base alle dimensioni di avviso

Si desidera, ad esempio, monitorare gli errori per più macchine virtuali che eseguono il sito Web o l'app in un gruppo di risorse specifico. Questa operazione può essere eseguita usando una regola di avviso del log, come indicato di seguito:

- **Query:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Quando si usano le aree di lavoro e Application Insights con la logica di avviso di **misurazione delle metriche** , è necessario aggiungere questa riga al testo della query:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Colonna ID risorsa:** _ResourceId (la suddivisione in base alla colonna ID risorsa nelle regole di avviso è disponibile solo per le sottoscrizioni e i gruppi di risorse attualmente)
- **Dimensioni/aggregazione in:**
  - Computer = VM1, VM2 (i valori di filtro nella definizione delle regole di avviso non sono attualmente disponibili per le aree di lavoro e Application Insights. Filtrare nel testo della query.)
- **Periodo di tempo:** 15 minuti
- **Frequenza di avviso:** 15 minuti
- **Valore di soglia:** maggiore di 0

Questa regola monitora se una macchina virtuale presenta eventi di errore negli ultimi 15 minuti. Ogni macchina virtuale viene monitorata separatamente e attiverà azioni singolarmente.

> [!NOTE]
> La divisione in base alle dimensioni di avviso è disponibile solo per l'API scheduledQueryRules corrente. Se si usa l' [API log Analytics Alert](api-alerts.md)legacy, sarà necessario passare a. [Altre informazioni sul cambio](./alerts-log-api-switch.md). Gli avvisi incentrati sulle risorse alla scala sono supportati solo nella versione dell'API `2020-05-01-preview` e versioni successive.

## <a name="alert-logic-definition"></a>Definizione della logica di avviso

Una volta definita la query da eseguire e la valutazione dei risultati, è necessario definire la logica di avviso e quando attivare le azioni. Le sezioni seguenti descrivono i diversi parametri che è possibile usare:

### <a name="threshold-and-operator"></a>Threshold e operator

I risultati della query vengono trasformati in un numero che viene confrontato con la soglia e l'operatore.

### <a name="frequency"></a>Frequenza

Intervallo di esecuzione della query. Il set può essere compreso tra 5 minuti e un giorno. Deve essere minore o uguale all'intervallo di [tempo della query](#query-time-range) per non perdere i record del log.

Ad esempio, se si imposta il periodo di tempo su 30 minuti e la frequenza su 1 ora.  Se la query viene eseguita a 00:00, restituisce record compresi tra 23:30 e 00:00. Alla successiva esecuzione della query è 01:00 che restituirà i record compresi tra 00:30 e 01:00. Tutti i record creati tra 00:00 e 00:30 non verranno mai valutati.

### <a name="number-of-violations-to-trigger-alert"></a>Numero di violazioni da attivare per l'avviso

È possibile specificare il periodo di valutazione dell'avviso e il numero di errori necessari per attivare un avviso. Consentendo di definire in modo più efficace un tempo di inefficacia per l'attivazione di un avviso. 

Se, ad esempio, la [**granularità di aggregazione**](#aggregation-granularity) della regola è definita come "5 minuti", è possibile attivare un avviso solo se si verificano tre errori (15 minuti) dell'ultima ora. Questa impostazione è definita dai criteri di business dell'applicazione.

## <a name="state-and-resolving-alerts"></a>Stato e risoluzione degli avvisi

Gli avvisi del log sono senza stato. Gli avvisi vengono attivati ogni volta che viene soddisfatta la condizione, anche se attivata in precedenza. Gli avvisi generati non vengono risolti. È possibile [contrassegnare l'avviso come chiuso](alerts-managing-alert-states.md). È anche possibile disattivare le azioni per impedirne l'attivazione per un periodo dopo il quale è stata attivata una regola di avviso.

Nelle aree di lavoro e in Application Insights, viene chiamato non **visualizzare avvisi**. In tutti gli altri tipi di risorse, viene chiamato **azioni mute**. 

Vedere questo esempio di valutazione degli avvisi:

| Tempo    | Valutazione della condizione del log | Risultato 
| ------- | ----------| ----------| ------- 
| 00:05 | false | L'avviso non viene attivato. Non è stata chiamata alcuna azione.
| 00:10 | TRUE  | Avvisi attivati e gruppi di azioni chiamati. Nuovo stato di avviso attivo.
| 00:15 | TRUE  | Avvisi attivati e gruppi di azioni chiamati. Nuovo stato di avviso attivo.
| 00:20 | false | L'avviso non viene attivato. Non è stata chiamata alcuna azione. Lo stato degli avvisi di precedente rimane attivo.

## <a name="pricing-and-billing-of-log-alerts"></a>Prezzi e fatturazione per gli avvisi del log

Le informazioni sui prezzi sono disponibili nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Gli avvisi del log sono elencati sotto provider `microsoft.insights/scheduledqueryrules` di risorse con:

- Registrare gli avvisi in Application Insights visualizzati con il nome esatto della risorsa insieme alle proprietà di avviso e del gruppo di risorse.
- Avvisi di log su Log Analytics visualizzati con il nome esatto della risorsa insieme alle proprietà di avviso e del gruppo di risorse. Quando viene creato usando l' [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules).
- Gli avvisi del log creati dall' [API legacy log Analytics](./api-alerts.md) non vengono rilevate [le risorse di Azure](../../azure-resource-manager/management/overview.md) e non hanno imposto nomi di risorse univoci. Questi avvisi vengono ancora creati `microsoft.insights/scheduledqueryrules` come risorse nascoste, che hanno questa struttura di denominazione delle risorse `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Gli avvisi del log nell'API legacy vengono visualizzati con il nome di risorsa nascosto sopra, insieme alle proprietà di avviso e del gruppo di risorse.

> [!NOTE]
> I caratteri di risorse non supportati, ad esempio, `<, >, %, &, \, ?, /` vengono sostituiti con `_` nei nomi delle risorse nascoste, che verranno inoltre riportate nelle informazioni sulla fatturazione.

> [!NOTE]
> Avvisi di log per Log Analytics usati per essere gestiti tramite l' [API log Analytics Alert](api-alerts.md) legacy e i modelli legacy di [log Analytics le ricerche salvate e gli avvisi](../insights/solutions.md). [Altre informazioni sul trasferimento all'API ScheduledQueryRules corrente](alerts-log-api-switch.md). Qualsiasi gestione delle regole di avviso deve essere eseguita usando l' [API log Analytics legacy](api-alerts.md) fino a quando non si decide di cambiare e non è possibile usare le risorse nascoste.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [creazione di avvisi dei log in Azure](./alerts-log.md).
* Acquisire familiarità con i [webhook negli avvisi dei log in Azure](alerts-log-webhook.md).
* Informazioni su [Avvisi di Azure](./alerts-overview.md).
* Altre informazioni su [log Analytics](../log-query/log-query-overview.md).

