---
title: Panoramica degli avvisi in Log Analytics di OMS | Microsoft Docs
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all&quot;utente in modo proattivo o richiamare le azioni per tentare di correggerle.  Questo articolo descrive come creare una regola di avviso e include i dettagli relativi alle diverse azioni che possono attivare.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>Risolvere i problemi in Log Analytics usando gli avvisi

Gli avvisi in Log Analytics identificano informazioni importanti nel repository di Log Analytics.  Possono essere usati per essere prontamente informati di un problema o intraprendere azioni automatiche in risposta al problema.  Questo articolo offre una panoramica della creazione e dell'uso degli avvisi.  


>[!NOTE]
> Per informazioni sulle regole di avviso di misurazione delle metriche attualmente in anteprima pubblica, vedere [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/) (Nuovo tipo di regola di avviso di misurazione delle metriche in anteprima pubblica).

## <a name="alert-rules"></a>Regole di avviso

Gli avvisi vengono creati da regole di avviso che eseguono automaticamente ricerche log a intervalli regolari.  Se i risultati della ricerca log corrispondono a criteri specifici viene creato un record di avviso.  La regola può anche eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo.  

![Avvisi Log Analytics](media/log-analytics-alerts/overview.png)


Le regole di avviso vengono definite dai dettagli seguenti.

- **Ricerca log**.  Si tratta della query che verrà eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query verranno usati per determinare se viene creato un avviso.
- **Intervallo di tempo**.  Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , verranno restituiti solo i record creati tra 12.15 e le 13.15.
- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore dell'intervallo di tempo.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br>Si considerino ad esempio un intervallo di tempo di 30 minuti e una frequenza pari a 60 minuti.  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13:00 e 13:30 non verrà mai valutato.
- **Soglia**.  Se il numero di record restituito dalla ricerca log supera la soglia, viene creato un avviso.

## <a name="creating-alert-rules"></a>Creazione di regole di avviso
È possibile creare e modificare le regole di avviso in più modi.  Per istruzioni dettagliate, vedere gli articoli seguenti.  

- Creare regole di avviso tramite il [portale OMS](log-analytics-alerts-creating.md).
- Creare regole di avviso tramite il [modello di Resource Manager](log-analytics-template-workspace-configuration.md).
- Creare regole di avviso tramite l'[API REST](log-analytics-api-alerts.md).

## <a name="alert-actions"></a>Azioni di avviso

Oltre a creare un record di avviso, la regola di avviso può eseguire una o più azioni quando crea un avviso.  È possibile usare azioni per inviare un messaggio di posta elettronica in risposta all'avviso o avviare un processo che prova a eseguire azioni correttive.  

È anche possibile usare le azioni per espandere le funzionalità di Log Analytics con altri servizi.  Ad esempio, Log Analytics non supporta attualmente funzionalità per la notifica tramite SMS o telefono.  È tuttavia possibile usare un'azione webhook in una regola di avviso per chiamare un servizio, ad esempio [PagerDuty](https://www.pagerduty.com/), che offre queste funzionalità.  Per un esempio dettagliato di creazione di un webhook per l'invio di un messaggio tramite [Slack](https://slack.com/), vedere [Creare un'azione di avviso webhook in Log Analytics di OMS per inviare messaggi a Slack](log-analytics-alerts-webhooks.md).

La tabella seguente elenca le azioni disponibili.  Per informazioni su ognuna di queste azioni, vedere [Adding actions to alert rules in Log Analytics](log-analytics-alerts-actions.md) (Aggiunta di azioni a regole di avviso in Log Analytics). 

| Azione | Descrizione |
|:--|:--|
| Email  |     Inviare un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari. |
| webhook | Richiamare un processo esterno tramite una singola richiesta HTTP POST. |
| Runbook | Avviare un runbook in Automazione di Azure. |


## <a name="alerting-scenarios"></a>Scenari di avviso

### <a name="events"></a>Eventi
Per generare un avviso su un singolo evento, creare una regola di avviso con il numero di risultati **maggiore di 0** e la frequenza e l'intervallo di tempo impostati su **5 minuti**.  Queste impostazioni attivano l'esecuzione della query ogni 5 minuti e la ricerca dell'occorrenza di un singolo evento creato dall'ultima esecuzione della query.  Una frequenza maggiore può ritardare il tempo tra l'evento raccolto e la creazione dell'avviso.  Per specificare l'evento desiderato verrà usata una query simile alla seguente.

    Type=Event Source=MyApplication EventID=7019 

Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che in un intervallo di tempo specifico non vengano creati più eventi.  A tale scopo usare la stessa query, ma impostare la soglia su un numero più elevato.  Per generare ad esempio un avviso su 5 eventi in 30 minuti, impostare la frequenza su **5 minuti**, l'intervallo di tempo su **30 minuti** e il numero di risultati **maggiore di 4**.    

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non viene registrato uno di questi eventi in un intervallo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare un numero di risultati **minore di 1**.

### <a name="performance-alerts"></a>Avvisi di prestazioni
I [dati sulle prestazioni](log-analytics-data-sources-performance-counters.md) vengono archiviati come record nel repository di Log Analytics in modo analogo agli eventi.  Se si vuole generare un avviso quando un contatore delle prestazioni supera una determinata soglia, tale soglia deve essere inclusa nella query.

Se ad esempio si vuole generare un avviso quando il processore è in esecuzione oltre il 90%, usare una query come la seguente con il numero di risultati per la regola di avviso **maggiore di 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Se si vuole generare un avviso quando la media del processore è oltre il 90% per un determinato intervallo di tempo, usare una query con il [comando measure](log-analytics-search-reference.md#commands) come la seguente con la soglia per la regola di avviso **maggiore di 0**. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>Record di avvisi
I record degli avvisi creati dalle regole di avviso in Log Analytics hanno un **Tipo** impostato su **Avviso** e **SourceSystem** impostato su **OMS**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*Avviso* |
| SourceSystem |*OMS* |
| AlertName |Nome dell'avviso. |
| AlertSeverity |Livello di gravità dell'avviso. |
| LinkToSearchResults |Collegamento alla ricerca nei log di Log Analytics che restituisce i record della query che ha creato l'avviso. |
| Query |Testo della query eseguita. |
| QueryExecutionEndTime |Fine dell'intervallo di tempo per la query. |
| QueryExecutionStartTime |Inizio dell'intervallo di tempo per la query. |
| ThresholdOperator | Operatore usato dalla regola di avviso. |
| ThresholdValue | Valore usato dalla regola di avviso. |
| TimeGenerated |Data e ora in cui è stato creato l'avviso. |

Sono disponibili altri tipi di record di avviso creati dalla [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) e dalle [esportazioni di Power BI](log-analytics-powerbi.md).  Questi record hanno un **Tipo** impostato su **Avviso** ma vengono distinti da relativo **SourceSystem**.


## <a name="next-steps"></a>Passaggi successivi
* Creare una regola di avviso tramite il [portale OMS](log-analytics-alerts-creating.md).
* Installare la [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) per analizzare gli avvisi creati in Log Analytics con gli avvisi raccolti da System Center Operations Manager (SCOM).
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) che possono generare avvisi.
* Completare una procedura dettagliata per la [configurazione di un webhook](log-analytics-alerts-webhooks.md) con una regola di avviso.  
* Informazioni su come scrivere [runbook in Automazione di Azure](https://azure.microsoft.com/documentation/services/automation) per la risoluzione dei problemi identificati dagli avvisi.


