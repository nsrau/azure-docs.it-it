---
title: La soluzione Gestione avvisi in Operations Management Suite (OMS) | Documentazione Microsoft
description: La soluzione Alert Management in Log Analytics consente di analizzare tutti gli avvisi nell&quot;ambiente.  Oltre a consolidare gli avvisi generati in OMS, importa gli avvisi da gruppi di gestione di System Center Operations Manager (SCOM) collegati in Log Analytics.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 885de1e94e3ce520621dc8dc7a4a495501f6a429
ms.openlocfilehash: 35b4c30de20c46312bd7e4524a4264450184138a
ms.lasthandoff: 02/18/2017


---
# <a name="alert-management-solution-in-operations-management-suite-oms"></a>La soluzione Gestione avvisi in Operations Management Suite (OMS)

![Icona di Alert Management](media/log-analytics-solution-alert-management/icon.png)

La soluzione Alert Management in Log Analytics consente di analizzare tutti gli avvisi nel repository di Log Analytics.  Questi avvisi possono provenire da diverse origini, inclusi quelli [creati da Log Analytics](log-analytics-alerts.md) o [importati da Nagios o Zabbix](log-analytics-linux-agents.md#linux-alerts).  La soluzione importa anche gli avvisi da qualsiasi [gruppo di gestione di System Center Operations Manager (SCOM) connesso](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Prerequisiti
La soluzione funziona con i record nel repository di Log Analytics con un tipo di **avviso**, pertanto è necessario eseguire qualsiasi configurazione necessaria per raccogliere questi record.

- Per gli avvisi di Log Analytics, [creare regole di avviso](log-analytics-alerts.md) per creare i record degli avvisi direttamente nel repository.
- Per gli avvisi di Nagios e Zabbix, [configurare tali server](log-analytics-linux-agents.md#linux-alerts) per inviare avvisi a Log Analytics.
- Per gli avvisi SCOM,[connettere il gruppo di gestione Operations Manager all'area di lavoro Log Analytics](log-analytics-om-agents.md).  Gli avvisi creati in SCOM verranno importati in Log Analytics.  

## <a name="configuration"></a>Configurazione
Aggiungere la soluzione Alert Management all'area di lavoro di OMS usando la procedura descritta in [Aggiungere soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

## <a name="management-packs"></a>Management Pack
Se il gruppo di gestione SCOM è connesso all'area di lavoro di OMS, i Management Pack seguenti verranno installati in SCOM quando si aggiunge questa soluzione.  Per questi Management Pack non è richiesta alcuna configurazione o manutenzione.  

* Alert Management di Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

## <a name="data-collection"></a>Raccolta dei dati
### <a name="agents"></a>Agents
La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supporto | Descrizione |
|:--- |:--- |:--- |
| [Agenti di Windows](log-analytics-windows-agents.md) | No |Gli agenti di Windows diretti non generano avvisi.  Gli avvisi di Log Analytics possono essere creati da eventi e dati sulle prestazioni raccolti dagli agenti di Windows. |
| [Agenti Linux](log-analytics-linux-agents.md) | No |Gli agenti di Linux diretti non generano avvisi.  Gli avvisi di Log Analytics possono essere creati da eventi e dati sulle prestazioni raccolti dagli agenti di Linux.  Gli avvisi di Nagios e Zabbix vengono raccolti dai rispettivi server che richiedono l'agente Linux. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) |Sì |Gli avvisi generati per gli agenti SCOM vengono distribuiti al gruppo di gestione e quindi inoltrati a Log Analytics.<br><br>Non è necessaria una connessione diretta dagli agenti SCOM a Log Analytics. I dati degli avvisi vengono inoltrati dal gruppo di gestione al repository di Log Analytics. |


### <a name="collection-frequency"></a>Frequenza della raccolta
- I record degli avvisi sono disponibili per la soluzione appena vengono archiviati nel repository.
- I dati degli avvisi vengono inviati dal gruppo di gestione SCOM a Log Analytics ogni tre minuti.  

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Alert Management all'area di lavoro di OMS, il riquadro **Alert Management** verrà aggiunto al dashboard OMS.  Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di avvisi attivi generati nelle ultime 24 ore.  Non è possibile modificare questo intervallo di tempo.

![Riquadro di Alert Management](media/log-analytics-solution-alert-management/tile.png)

Fare clic su sul riquadro **Gestione avvisi** per aprire il relativo** **dashboard.  Il dashboard include le colonne nella tabella seguente.  Ogni colonna elenca i primi dieci avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati.  È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su **Visualizza tutto** nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna | Descrizione |
|:--- |:--- |
| Critical Alerts |Tutti gli avvisi con un livello di gravità Critico raggruppati per nome dell'avviso.  Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Warning Alerts |Tutti gli avvisi con una gravità Avviso per nome dell'avviso.  Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Avvisi SCOM attivi |Tutti gli avvisi raccolti da SCOM con uno stato diverso da *Chiuso* raggruppati per origine che ha generato l'avviso. |
| All Active Alerts |Tutti gli avvisi con qualsiasi gravità raggruppati per nome dell'avviso. Include solo gli avvisi SCOM con qualsiasi stato diverso da *Chiuso*. |

Se si scorre verso destra, il dashboard elencherà diverse query comuni che è possibile selezionare per eseguire una [ricerca log](log-analytics-log-searches.md) per dati di avviso.

![Dashboard di Alert Management](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Record di Log Analytics
La soluzione Alert Management consente di analizzare qualsiasi record con un tipo di **Avviso**.  Gli avvisi creati da Log Analytics o raccolti da Nagios o Zabbix non vengono raccolti direttamente dalla soluzione.

La soluzione non importa avvisi da SCOM e crea un record corrispondente per ciascuno di essi con un tipo di **Avviso** e un SourceSystem di **OpsManager**.  Questi record includono le proprietà elencate nella tabella seguente.  

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*Avviso* |
| SourceSystem |*OpsManager* |
| AlertContext |Dettagli dell'elemento di dati che ha causato la generazione dell'avviso nel formato XML. |
| AlertDescription |Descrizione dettagliata dell'avviso. |
| AlertId |GUID dell'avviso. |
| AlertName |Nome dell'avviso. |
| AlertPriority |Livello di priorità dell'avviso. |
| AlertSeverity |Livello di gravità dell'avviso. |
| AlertState |Ultimo stato di risoluzione dell'avviso. |
| LastModifiedBy |Nome dell'utente che ha effettuato l'ultima modifica dell'avviso. |
| ManagementGroupName |Nome del gruppo di gestione in cui è stato generato l'avviso. |
| RepeatCount |Numero di volte in cui è stato generato lo stesso avviso per lo stesso oggetto monitorato dalla risoluzione. |
| ResolvedBy |Nome dell'utente che ha risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |
| SourceDisplayName |Nome visualizzato dell'oggetto di monitoraggio che ha generato l'avviso. |
| SourceFullName |Nome completo dell'oggetto di monitoraggio che ha generato l'avviso. |
| TicketId |ID ticket per l'avviso se l'ambiente SCOM è integrato con un processo per l'assegnazione di ticket per gli avvisi.  Vuoto se non è assegnato alcun ID ticket. |
| TimeGenerated |Data e ora in cui è stato creato l'avviso. |
| TimeLastModified |Data e ora in cui è stato modificato l'avviso. |
| TimeRaised |Data e ora in cui è stato generato l'avviso. |
| TimeResolved |Data e ora in cui è stato risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |

## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente fornisce ricerche di log di esempio per i record degli avvisi raccolti da questa soluzione.  

| Query | Descrizione |
|:--- |:--- |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR |Avvisi critici generati durante le ultime 24 ore |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR |Avvertenze generate durante le ultime 24 ore |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName |Origini con avvisi critici generati durante le ultime 24 ore |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed |Avvisi critici generati durante le ultime 24 ore e che sono ancora attivi |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed |Avvisi critici generati durante le ultime 24 ore e che sono chiusi |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity |Avvisi generati durante l'ultimo giorno raggruppati in base alla relativa gravità |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc |Avvisi generati durante l'ultimo giorno raggruppati in base al relativo valore di conteggio |

## <a name="next-steps"></a>Passaggi successivi
* Leggere l'articolo [Avvisi in Log Analytics](log-analytics-alerts.md) per informazioni dettagliate sulla generazione di avvisi di Log Analytics.


