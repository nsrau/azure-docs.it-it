---
title: Monitorare l'integrità dell'area di lavoro Log Analytics in monitoraggio di Azure
description: Viene descritto come monitorare l'integrità dell'area di lavoro di Log Analytics utilizzando i dati nella tabella delle operazioni.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 07d9ae0d7cdf8e823bb59cb376d40cdf846bb2cb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092756"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorare l'integrità dell'area di lavoro Log Analytics in monitoraggio di Azure
Per mantenere le prestazioni e la disponibilità dell'area di lavoro Log Analytics in monitoraggio di Azure, è necessario essere in grado di rilevare in modo proattivo tutti i problemi che si verificano. Questo articolo descrive come monitorare l'integrità dell'area di lavoro di Log Analytics usando i dati nella tabella delle [operazioni](https://docs.microsoft.com/azure/azure-monitor/reference/tables/operation) . Questa tabella è inclusa in ogni area di lavoro Log Analytics e contiene gli errori e gli avvisi che si verificano nell'area di lavoro. È consigliabile esaminare periodicamente questi dati e creare avvisi per ricevere notifiche proattive in caso di eventi imprevisti importanti nell'area di lavoro.

## <a name="_logoperation-function"></a>Funzione _LogOperation

I log di monitoraggio di Azure inviano informazioni dettagliate sui problemi alla tabella delle [operazioni](https://docs.microsoft.com/azure/azure-monitor/reference/tables/operation) nell'area di lavoro in cui si è verificato il problema. La funzione di sistema **_LogOperation** è basata sulla tabella **Operation** e fornisce un set di informazioni semplificato per l'analisi e l'invio di avvisi.

## <a name="columns"></a>Colonne

La funzione **_LogOperation** restituisce le colonne nella tabella seguente.

| Colonna | Descrizione |
|:---|:---|
| TimeGenerated | Ora in cui si è verificata l'evento imprevisto. |
| Category  | Gruppo di categorie Operation. Può essere usato per filtrare i tipi di operazioni e consentire la creazione di avvisi e controllo del sistema più precisi. Per un elenco di categorie, vedere la sezione seguente. |
| Operazione  | Descrizione del tipo di operazione. Questo può indicare uno dei limiti Log Analytics, il tipo di operazione o parte di un processo. |
| Level | Livello di gravità del problema:<br>-Info: non è necessario prestare particolare attenzione.<br>-Warning: il processo non è stato completato come previsto e l'attenzione è necessaria.<br>-Errore: il processo non è riuscito ed è necessaria un'attenzione urgente. 
| Dettaglio | Una descrizione dettagliata dell'operazione include un messaggio di errore specifico, se esistente. |
| _ResourceId | ID risorsa della risorsa di Azure correlata all'operazione.  |
| Computer | Nome del computer se l'operazione è correlata a un agente di monitoraggio di Azure. |
| CorrelationId | Utilizzato per raggruppare le operazioni correlate consecutive. |


## <a name="categories"></a>Categorie

Nella tabella seguente vengono descritte le categorie della funzione _LogOperation. 

| Category | Descrizione |
|:---|:---|
| Ingestion           | Operazioni che fanno parte del processo di inserimento dei dati. Vedere di seguito per altri dettagli. |
| Agente               | Indica un problema relativo all'installazione dell'agente. |
| Raccolta dati     | Operazioni correlate ai processi di raccolte dati. |
| Targeting della soluzione  | L'operazione di tipo *ConfigurationScope* è stata elaborata. |
| Soluzione di valutazione | È stato eseguito un processo di valutazione. |


### <a name="ingestion"></a>Ingestion
Le operazioni di inserimento sono problemi che si sono verificati durante l'inserimento dei dati, incluse Log Analytics le notifiche relative al raggiungimento dei limiti di area Le condizioni di errore in questa categoria potrebbero indicare una perdita di dati, pertanto sono particolarmente importanti da monitorare. Nella tabella seguente vengono fornite informazioni dettagliate su queste operazioni. Vedere [limiti del servizio di monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) per i limiti del servizio per log Analytics aree di lavoro.


| Operazione | Level | Dettaglio | Articolo correlato |
|:---|:---|:---|:---|
| Log personalizzato | Errore   | È stato raggiunto il limite di colonne per i campi personalizzati. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Log personalizzato | Errore   | Inserimento dei log personalizzati non riuscito. | |
| Metadata. | Errore | Errore di configurazione rilevato. | |
| Raccolta dati | Errore   | I dati sono stati eliminati perché la richiesta è stata creata prima del numero di giorni impostati. | [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](manage-cost-storage.md#alert-when-daily-cap-reached)
| Raccolta dati | Info    | È stata rilevata la configurazione del computer della raccolta.| |
| Raccolta dati | Info    | Raccolta dati avviata a causa di un nuovo giorno. | [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](/azure/azure-monitor/platform/manage-cost-storage#alert-when-daily-cap-reached) |
| Raccolta dati | Avviso | Raccolta dati arrestata a causa del raggiungimento del limite giornaliero.| [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](/azure/azure-monitor/platform/manage-cost-storage#alert-when-daily-cap-reached) |
| Elaborazione dati | Errore   | Formato JSON non valido. | [Inviare dati di log a Monitoraggio di Azure con l'API di raccolta dati HTTP (anteprima pubblica)](data-collector-api.md#request-body) | 
| Elaborazione dati | Avviso | Il valore è stato tagliato fino alla dimensione massima consentita. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Elaborazione dati | Avviso | Il valore del campo è stato tagliato come limite dimensioni raggiunto. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) | 
| Velocità di inserimento | Info | Limite di velocità di inserimento prossimo al 70%. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Velocità di inserimento | Avviso | Limite di velocità di inserimento prossimo al limite. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Velocità di inserimento | Errore   | Limite di velocità raggiunto. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Archiviazione | Errore   | Non è possibile accedere all'account di archiviazione perché le credenziali utilizzate non sono valide.  |



   

## <a name="alert-rules"></a>Regole di avviso
Usare gli [avvisi di query di log](../platform/alerts-log-query.md) in monitoraggio di Azure per ricevere notifiche proattive quando viene rilevato un problema nell'area di lavoro log Analytics. È consigliabile usare una strategia che consenta di rispondere in modo tempestivo ai problemi, riducendo al minimo i costi. La sottoscrizione viene addebitata per ogni regola di avviso con un costo a seconda della frequenza con cui viene valutata.

Una strategia consigliata consiste nell'iniziare con due regole di avviso in base al livello del problema. Utilizzare una frequenza breve, ad esempio ogni 5 minuti, per gli errori e una frequenza più lunga, ad esempio 24 ore, per gli avvisi. Poiché gli errori indicano una potenziale perdita di dati, è opportuno rispondere rapidamente per ridurre al minimo eventuali perdite. Gli avvisi indicano in genere un problema che non richiede attenzione immediata, quindi è possibile esaminarli quotidianamente.

Usare il processo in [creare, visualizzare e gestire gli avvisi del log usando monitoraggio di Azure](../platform/alerts-log.md) per creare le regole di avviso del log. Nelle sezioni seguenti vengono descritti i dettagli di ogni regola.


| Query | Valore soglia | Periodo | Frequenza |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Queste regole di avviso rispondono allo stesso modo a tutte le operazioni con errore o avviso. Man mano che si acquisisce familiarità con le operazioni che generano avvisi, è possibile che si desideri rispondere in modo diverso per determinate operazioni. Ad esempio, è possibile inviare notifiche a utenti diversi per determinate operazioni. 

Per creare una regola di avviso per un'operazione specifica, utilizzare una query che includa le colonne **Category** e **Operation** . 

Nell'esempio seguente viene creato un avviso quando la velocità del volume di inserimento ha raggiunto il 80% del limite.

- Destinazione: selezionare l'area di lavoro Log Analytics
- Criteri:
  - Nome segnale: Ricerca log personalizzata
  - Query di ricerca: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - In base a: Numero di risultati
  - Condizione: Maggiore di
  - Soglia: 0
  - Periodo: 5 (minuti)
  - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)


Nell'esempio seguente viene creato un avviso quando la raccolta dati ha raggiunto il limite giornaliero. 

- Destinazione: selezionare l'area di lavoro Log Analytics
- Criteri:
  - Nome segnale: Ricerca log personalizzata
  - Query di ricerca: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - In base a: Numero di risultati
  - Condizione: Maggiore di
  - Soglia: 0
  - Periodo: 5 (minuti)
  - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [avvisi del log](alerts-log.md).
- [Raccogliere i dati di controllo della query](../log-query/query-audit.md) per l'area di lavoro.
