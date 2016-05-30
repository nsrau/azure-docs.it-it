<properties 
   pageTitle="Soluzione Alert Management in Log Analytics | Microsoft Azure"
   description="La soluzione Alert Management in Log Analytics consente di analizzare tutti gli avvisi nell'ambiente. Oltre a consolidare gli avvisi generati in OMS, importa gli avvisi da gruppi di gestione di System Center Operations Manager (SCOM) collegati in Log Analytics."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2016"
   ms.author="bwren" />

# Soluzione Alert Management in Log Analytics

La soluzione Alert Management consente di analizzare tutti gli avvisi nell'ambiente. Oltre a consolidare gli avvisi generati in OMS, importa gli avvisi da gruppi di gestione di System Center Operations Manager (SCOM) collegati in Log Analytics. Negli ambienti con più gruppi di gestione, la soluzione Alert Management fornirà una visualizzazione consolidata degli avvisi di tutti i gruppi di gestione.

![Icona di Alert Management](media/log-analytics-solution-alert-management/icon.png)

## Prerequisiti

- Per importare gli avvisi da SCOM, questa soluzione richiede una connessione tra l'area di lavoro di OMS e un gruppo di gestione SCOM usando il processo descritto in [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).  


## Configurazione

Aggiungere la soluzione Alert Management all'area di lavoro di OMS usando la procedura descritta in [Aggiungere soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.

## Management Pack

Se il gruppo di gestione SCOM è connesso all'area di lavoro di OMS, i Management Pack seguenti verranno installati in SCOM quando si aggiunge questa soluzione. Per questi Management Pack non è richiesta alcuna configurazione o manutenzione.

- Alert Management di Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

## Raccolta dei dati

### Agents

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supporto | Descrizione |
|:--|:--|:--|
| [Agenti di Windows](log-analytics-windows-agents.md) | No | Gli agenti di Windows diretti non generano avvisi SCOM. |
| [Agenti Linux](log-analytics-limux-agents.md) | No | Gli agenti di Linux diretti non generano avvisi SCOM. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | Sì | Gli avvisi generati per gli agenti SCOM vengono distribuiti al gruppo di gestione e quindi inoltrati a Log Analytics.<br><br>Non è necessaria una connessione diretta dall'agente SCOM a Log Analytics. I dati degli avvisi vengono inoltrati dal gruppo di gestione al repository OMS. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No | Gli avvisi SCOM non vengono archiviati negli account di archiviazione di Azure. |

### Frequenza della raccolta

Gli avvisi generati all'interno di OMS sono immediatamente disponibili per la soluzione. I dati degli avvisi vengono inviati dal gruppo di gestione SCOM a Log Analytics ogni tre minuti.

## Uso della soluzione

Quando si aggiunge la soluzione Alert Management all'area di lavoro di OMS, il riquadro **Alert Management** verrà aggiunto al dashboard OMS. Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di avvisi attivi generati nelle ultime 24 ore. Non è possibile modificare questo intervallo di tempo.

![Riquadro di Alert Management](media/log-analytics-solution-alert-management/tile.png)

Fare clic su sul riquadro **Alert Management** per aprire il relativo dashboard. Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su **Visualizza tutto** nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna| Descrizione |
|:--|:--|
| Critical Alerts | Tutti gli avvisi con un livello di gravità Critico raggruppati per nome dell'avviso. Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Warning Alerts | Tutti gli avvisi con una gravità Avviso per nome dell'avviso. Fare clic sul nome di un avviso per eseguire una ricerca che restituisce tutti i record per tale avviso. |
| Avvisi SCOM attivi | Tutti gli avvisi SCOM con uno stato diverso da *Chiuso* raggruppati per origine che ha generato l'avviso. |
| All Active Alerts | Tutti gli avvisi con qualsiasi gravità raggruppati per nome dell'avviso. Include solo gli avvisi SCOM con qualsiasi stato diverso da *Chiuso*.|

Se si scorre verso destra, il dashboard elencherà diverse query comuni che è possibile selezionare per eseguire una [ricerca log](log-analytics-log-searches.md) per dati di avviso.

![Dashboard di Alert Management](media/log-analytics-solution-alert-management/dashboard.png)

## Intervallo di tempo e ambito

Per impostazione predefinita, l'ambito degli avvisi analizzati nella soluzione Alert Management riguarda tutti i gruppi di gestione connessi generati negli ultimi sette giorni.

![Ambito di Alert Management](media/log-analytics-solution-alert-management/scope.png)

- Per modificare i gruppi di gestione inclusi nell'analisi, fare clic su **Ambito** nella parte superiore del dashboard. È possibile selezionare **Globale** per tutti i gruppi di gestione connessi o **Per gruppo di gestione** per selezionare un singolo gruppo di gestione.

- Per modificare l'intervallo di tempo degli avvisi, selezionare **Dati in base a** nella parte superiore del dashboard. È possibile selezionare gli avvisi generati negli ultimi sette giorni, nell'ultimo giorno o nelle ultime sei ore. In alternativa, è possibile selezionare **Personalizzato** e specificare un intervallo di date personalizzato.

## Record di Log Analytics

La soluzione Alert Management consente di analizzare qualsiasi record con un tipo di **Avviso**. Consente anche di importare avvisi da SCOM e di creare un record corrispondente per ciascuno di essi con un tipo di **Avviso** e un SourceSystem di **OpsManager**. Questi record includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Tipo | *Avviso* |
| SourceSystem | *OpsManager* |
| AlertContext | Dettagli dell'elemento di dati che ha causato la generazione dell'avviso nel formato XML. |
| AlertDescription | Descrizione dettagliata dell'avviso. |
| AlertId | GUID dell'avviso. |
| AlertName | Nome dell'avviso. |
| AlertPriority | Livello di priorità dell'avviso. |
| AlertSeverity | Livello di gravità dell'avviso. |
| AlertState | Ultimo stato di risoluzione dell'avviso. |
| LastModifiedBy | Nome dell'utente che ha effettuato l'ultima modifica dell'avviso. |
| ManagementGroupName | Nome del gruppo di gestione in cui è stato generato l'avviso. |
| RepeatCount | Numero di volte in cui è stato generato lo stesso avviso per lo stesso oggetto monitorato dalla risoluzione. |
| ResolvedBy | Nome dell'utente che ha risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |
| SourceDisplayName | Nome visualizzato dell'oggetto di monitoraggio che ha generato l'avviso. |
| SourceFullName | Nome completo dell'oggetto di monitoraggio che ha generato l'avviso. |
| TicketId | ID ticket per l'avviso se l'ambiente SCOM è integrato con un processo per l'assegnazione di ticket per gli avvisi. Vuoto se non è assegnato alcun ID ticket. |
| TimeGenerated | Data e ora in cui è stato creato l'avviso. |
| TimeLastModified | Data e ora in cui è stato modificato l'avviso. |
| TimeRaised | Data e ora in cui è stato generato l'avviso. |
| TimeResolved | Data e ora in cui è stato risolto l'avviso. Vuoto se l'avviso non è ancora stato risolto. |

## Ricerche di log di esempio

La tabella seguente fornisce ricerche di log di esempio per i record degli avvisi raccolti da questa soluzione.

| Query | Descrizione |
|:--|:--|
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR | Avvisi critici generati durante le ultime 24 ore |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR | Avvertenze generate durante le ultime 24 ore |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR | measure count() as Count by SourceDisplayName | Origini con avvisi critici generati durante le ultime 24 ore |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed | Avvisi critici generati durante le ultime 24 ore e che sono ancora attivi |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed | Avvisi critici generati durante le ultime 24 ore e che sono chiusi |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY | measure count() as Count by AlertSeverity | Avvisi generati durante l'ultimo giorno raggruppati in base alla relativa gravità |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY | sort RepeatCount desc | Avvisi generati durante l'ultimo giorno raggruppati in base al relativo valore di conteggio |

## Passaggi successivi

- Leggere l'articolo [Avvisi in Log Analytics](log-analytics-alerts.md) per informazioni dettagliate sulla generazione di avvisi di Log Analytics. 

<!---HONumber=AcomDC_0518_2016-->