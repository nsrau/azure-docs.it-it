---
title: Esplorare la nuova esperienza Avvisi in Monitoraggio di Azure | Microsoft Docs
description: Comprendere come la nuova esperienza semplice e scalabile di Azure per gli avvisi consente di creare, visualizzare e gestire gli avvisi con maggiore facilità
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nuova esperienza Avvisi in Monitoraggio di Azure

## <a name="overview"></a>Panoramica

> [!NOTE]
> Questo articolo descrive i nuovi avvisi. Gli avvisi di Monitoraggio di Azure classici di tipo precedente sono descritti nell'articolo di [panoramica degli avvisi classici](monitoring-overview-alerts.md). 
>
>

È disponibile una nuova esperienza Avvisi. L'esperienza Avvisi precedente è ora disponibile nella scheda Avvisi (versione classica). La nuova esperienza Avvisi offre i vantaggi seguenti rispetto all'esperienza Avvisi (versione classica):

-   **Migliore sistema di notifica**: tutti gli avvisi più recenti usano [gruppi di azioni]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), ovvero gruppi di azioni e notifiche con nome che possono essere riusati in più avvisi.  Al contrario, gli avvisi delle metriche classici e gli avvisi di Log Analytics di tipo precedente non usano gruppi di azioni. 
- **Esperienza di creazione unificata**: tutte le funzionalità di creazione per metriche, log e log attività in Monitoraggio di Azure, Log Analytics e Application Insights si trovano in un'unica posizione. 
- **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: è ora possibile visualizzare anche gli avvisi di Log Analytics attivati nella sottoscrizione. In precedenza, era necessario un portale separato. 
- **Separazione tra avvisi attivati e regole di avviso**: viene fatta distinzione tra regole di avviso (definizioni delle condizioni che attivano gli avvisi) e avvisi attivati (istanze di attivazione delle regole di avviso) e sono pertanto disponibili visualizzazioni separate per le attività operative e la configurazione.
- **Flusso di lavoro migliore**: la nuova esperienza di creazione di avvisi guida l'utente nel processo di configurazione di una regola di avviso, rendendo più semplice l'identificazione delle condizioni per cui è opportuno ricevere un avviso.
 
In particolare, i nuovi avvisi delle metriche presentano i miglioramenti seguenti:
-   **Latenza migliorata**: gli avvisi delle metriche più recenti possono essere eseguiti con una frequenza di un minuto. Gli avvisi metrica meno recenti sono sempre eseguibili con una frequenza di 5 minuti. Gli avvisi del log hanno ancora un ritardo superiore a 1 minuto a causa del tempo necessario per inserire i log. 
-   **Supporto delle metriche multidimensionali**: è possibile inviare un avviso per le metriche multidimensionali, in modo da monitorare un segmento interessante della metrica.
-   **Maggiore controllo delle condizioni delle metriche**: è possibile definire regole di avviso più dettagliate. Gli avvisi più recenti supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
-   **Monitoraggio combinato di più metriche**: è possibile monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
-   **Metriche dai log** (anteprima pubblica limitata): alcuni dati di log diretti a Log Analytics possono essere ora estratti in metriche di Monitoraggio di Azure e quindi essere oggetto di avvisi come le altre metriche. 



Le sezioni seguenti descrivono in modo più dettagliato come funziona la nuova esperienza.

## <a name="alert-rules-terminology"></a>Terminologia relativa alle regole di avviso
La nuova esperienza Avvisi si basa sui concetti seguenti per separare gli oggetti regola di avviso e avviso attivato, offrendo al tempo stesso un'esperienza di creazione unificata per tipi di avvisi diversi.

- **Risorsa di destinazione**: come destinazione può essere configurata una qualsiasi risorsa di Azure. La risorsa di destinazione definisce l'ambito e i segnali disponibili per la generazione di avvisi. Esempi di destinazione: una macchina virtuale, un account di archiviazione, un set di scalabilità di macchine virtuali, un'area di lavoro di Log Analytics o una risorsa di Application Insights.

- **Criteri**: una combinazione di segnale e logica applicata a una risorsa di destinazione. Esempi: Percentuale d'uso della CPU > 70%, Tempo di risposta del server > 4 ms, Numero di risultati di una query su log > 100 e così via. 

- **Segnale**: il segnale emesso dalla risorsa di destinazione. I segnali possono essere di vari tipi. I tipi di segnale supportati sono **Metrica**, **Log attività**, **Application Insights** e **Log**.

- **Logica**: la logica definita dall'utente per verificare se il segnale è compreso nell'intervallo o nei valori previsti.  
 
- **Azione**: un'azione specifica eseguita quando viene attivato l'avviso. Ad esempio, l'invio di un messaggio a un indirizzo di posta elettronica o la chiamata di un URL webhook. Quando viene attivato un avviso, possono essere eseguite più azioni. Gli avvisi supportano i gruppi di azioni.  
 
- **Regola di avviso**: condizione che attiverà l'avviso. La regola di avviso acquisisce la destinazione e i criteri per la generazione di avvisi. Lo stato della regola di avviso può essere Abilitato o Disabilitato.
 
    > [!NOTE]
    > Questo oggetto si comporta quindi in modo diverso rispetto all'esperienza Avvisi (versione classica), dove l'avviso rappresenta sia la regola sia l'avviso attivato e quindi può avere uno stato attivo, disabilitato o di avviso.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Un'unica area per visualizzare e gestire gli avvisi
L'obiettivo dell'esperienza Avvisi è quello di offrire un'unica posizione dove visualizzare e gestire tutti gli avvisi di Azure. Le sottosezioni seguenti descrivono le funzioni di ogni schermata della nuova esperienza.

### <a name="alerts-overview-page"></a>Pagina Panoramica avvisi
La pagina di panoramica **Monitoraggio - Avvisi** mostra un riepilogo aggregato di tutti gli avvisi attivati e di tutte le regole di avviso configurate o abilitate, oltre a un elenco di tutti gli avvisi attivati. Se si cambiano le sottoscrizioni o i parametri di filtro, i dati aggregati e l'elenco degli avvisi attivati vengono aggiornati di conseguenza.

> [!NOTE]
> La visualizzazione degli avvisi attivati in Avvisi è limitata agli avvisi di log attività e metriche supportati. La panoramica di Monitoraggio di Azure mostra il numero di avvisi attivati, inclusi quelli della versione precedente di Avvisi di Azure.

 ![Panoramica avvisi](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gestione delle regole di avviso
**Monitoraggio - Avvisi > Regole** è una singola pagina da cui è possibile gestire tutte le regole di avviso nelle sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso (abilitate o disabilitate), che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. È possibile abilitare o disabilitare o anche modificare le regole di avviso da questa pagina.  

 ![Regole di avviso](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Un'unica esperienza per la creazione di avvisi in tutte le origini di monitoraggio
Nella nuova esperienza Avvisi è possibile creare avvisi in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale. Tutti gli avvisi attivati e i relativi dettagli sono disponibili in un'unica pagina.  
 
La procedura per creare un avviso prevede tre passaggi. L'utente sceglie prima di tutto una destinazione per l'avviso, quindi seleziona il segnale appropriato e infine specifica la logica da applicare al segnale come parte della regola di avviso. Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'esperienza di creazione comune filtra automaticamente l'elenco dei segnali disponibili in base alla risorsa di destinazione selezionata e fornisce indicazioni per definire la logica degli avvisi.

Per altre informazioni su come creare i tipi di avviso seguenti, vedere [questo articolo](monitor-alerts-unified-usage.md).
- Avvisi delle metriche
- Avvisi di log (Log Analytics)
- Avvisi di log (Log attività)
- Avvisi di log (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Avvisi supportati nella nuova esperienza
Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare questi servizi, [vedere questo articolo](./monitoring-overview.md). Di seguito è riportato un riepilogo dei tipi di avviso disponibili in Azure e gli avvisi attualmente supportati dalla nuova esperienza Avvisi. 


| **Tipo di segnale** | **Origine di monitoraggio** | **Descrizione** | 
|-------------|----------------|-------------|
| Metrica | Monitoraggio di Azure | Denominati anche [avvisi metriche near real time](monitoring-near-real-time-metric-alerts.md), supportano la valutazione delle condizioni delle metriche alla frequenza di un minuto e consentono l'uso di regole relative a più metriche e di regole relative a metriche multidimensionali. Un elenco dei tipi di risorse supportati è disponibile in [questa sezione](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Metrica | Monitoraggio di Azure | Nella nuova esperienza Avvisi non sono supportati gli [avvisi delle metriche classici](monitoring-overview-alerts.md) di tipo precedente. Questi avvisi sono disponibili nella sezione Avvisi (versione classica) del portale di Azure. Gli avvisi classici supportano alcuni tipi di metriche che non sono ancora stati trasferiti nei nuovi avvisi. Per un elenco completo, vedere [Metriche supportate](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Log  | Log Analytics | Questa categoria di avvisi consente di ricevere notifiche o eseguire ricerche automatizzate quando una query di ricerca log su dati relativi a metriche e/o eventi soddisfa determinati criteri. Gli avvisi di Log Analytics di tipo precedente sono ancora disponibili ma sono stati anche [copiati nella nuova esperienza](monitoring-alerts-extend.md). È disponibile anche un'[anteprima dei *log di Log Analytics come metriche*](monitoring-alerts-extend-tool.md). Questa anteprima consente di convertire alcuni tipi di log in metriche, che possono essere fatte oggetto di avvisi usando la nuova esperienza Avvisi. L'anteprima è utile se si hanno log non Azure che si intende gestire insieme alle metriche di Monitoraggio di Azure native. |
| Log attività | Log attività (generale) | Include i record di tutte le azioni di creazione, aggiornamento ed eliminazione eseguite sulla destinazione selezionata (risorsa/gruppo di risorse/sottoscrizione). |
| Log attività  | Integrità del servizio | Non supportati nell'esperienza Avvisi. Vedere [Creare gli avvisi del log attività per le notifiche del servizio](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Log  | Application Insights | Include i log con i dettagli sulle prestazioni dell'applicazione. Usando una query di analisi, è possibile definire le condizioni per le azioni da eseguire, in base ai dati dell'applicazione. |
| Metrica | Application Insights | Non supportati nell'esperienza Avvisi. Vedere [Avvisi delle metriche](../application-insights/app-insights-alerts.md) |
| Test di disponibilità Web | Application Insights | Non supportati nell'esperienza Avvisi.  Vedere [Avvisi di test Web](../application-insights/app-insights-monitor-web-app-availability.md). Disponibili in qualsiasi sito Web dotato delle funzionalità per l'invio di dati ad Application Insights. Ricevere una notifica quando la disponibilità o la velocità di risposta di un sito Web è inferiore alle aspettative. |




## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come usare la nuova esperienza Avvisi per creare, visualizzare e gestire gli avvisi](monitor-alerts-unified-usage.md)
- [Informazioni sugli avvisi del log nell'esperienza Avvisi](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi metriche nell'esperienza Avvisi](monitoring-near-real-time-metric-alerts.md)
- [Informazioni sugli avvisi del log attività nell'esperienza Avvisi](monitoring-activity-log-alerts-new-experience.md)
