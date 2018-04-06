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
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nuova esperienza Avvisi in Monitoraggio di Azure

## <a name="overview"></a>Panoramica
È disponibile una nuova esperienza Avvisi. L'esperienza Avvisi precedente è ora disponibile nella scheda Avvisi (versione classica). La nuova esperienza Avvisi offre i vantaggi seguenti rispetto all'esperienza Avvisi (versione classica):

 - **Separazione tra avvisi attivati e regole di avviso**: viene fatta distinzione tra regole di avviso (definizioni delle condizioni che attivano gli avvisi) e avvisi attivati (istanze di attivazione delle regole di avviso) e sono pertanto disponibili visualizzazioni separate per le attività operative e la configurazione.
 - **Esperienza di creazione unificata**: tutte le funzionalità di creazione per metriche, log e log attività in Monitoraggio di Azure, Log Analytics e Application Insights si trovano in un'unica posizione. 
 - **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: è ora possibile visualizzare anche gli avvisi di Log Analytics attivati nella sottoscrizione. In precedenza, era necessario un portale separato. 
 - **Flusso di lavoro migliore**: la nuova esperienza di creazione di avvisi guida l'utente nel processo di configurazione di una regola di avviso, rendendo più semplice l'identificazione delle condizioni per cui è opportuno ricevere un avviso.
 

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

 ![Panoramica avvisi](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gestione delle regole di avviso
**Monitoraggio - Avvisi > Regole** è una singola pagina da cui è possibile gestire tutte le regole di avviso nelle sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso (abilitate o disabilitate), che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. È possibile abilitare o disabilitare o anche modificare le regole di avviso da questa pagina.  

 ![Regole di avviso](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Un'unica esperienza per la creazione di avvisi in tutte le origini di monitoraggio
Nella nuova esperienza Avvisi è possibile creare avvisi in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale. Tutti gli avvisi attivati e i relativi dettagli sono disponibili in un'unica pagina.  
 
La procedura per creare un avviso prevede tre passaggi. L'utente sceglie prima di tutto una destinazione per l'avviso, quindi seleziona il segnale appropriato e infine specifica la logica da applicare al segnale come parte della regola di avviso. Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'esperienza di creazione comune filtra automaticamente l'elenco dei segnali disponibili in base alla risorsa di destinazione selezionata e fornisce indicazioni per definire la logica degli avvisi.

Per altre informazioni su come creare i tipi di avviso seguenti, vedere [questo articolo](monitor-alerts-unified-usage.md).
- Avvisi delle metriche
- Avvisi di log (Log Analytics)
- Avvisi di log (Log attività)
- Avvisi di log (Application Insights)

 

## <a name="alert-types-supported"></a>Tipi di avvisi supportati


| **Tipo di segnale** | **Origine di monitoraggio** | **Descrizione** | 
|-------------|----------------|-------------|
| Metrica | Monitoraggio di Azure | Questi avvisi metriche, detti anche [**avvisi metriche near real time**](monitoring-near-real-time-metric-alerts.md) supportano la valutazione delle condizioni delle metriche con frequenza fino a un minuto e consentono l'uso di regole relative a più metriche. Un elenco dei tipi di risorse supportati è disponibile in [questa sezione](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Gli avvisi metriche precedenti, illustrati [qui](monitoring-overview-alerts.md#alerts-in-different-azure-services), non sono supportati nella nuova esperienza Avvisi. Questi avvisi sono disponibili in Avvisi (versione classica)|
| Log  | Log Analytics | Questa categoria di avvisi consente di ricevere notifiche o eseguire ricerche automatizzate quando una query di ricerca log su dati relativi a metriche e/o eventi soddisfa determinati criteri.|
| Log attività | Log attività | Questa categoria include i record di tutte le azioni di creazione, aggiornamento ed eliminazione eseguite sulla destinazione selezionata (risorsa/gruppo di risorse/sottoscrizione). |
| Log  | Log di integrità dei servizi | Non supportati nell'esperienza Avvisi.   |
| Log  | Application Insights | Questa categoria include i log con i dettagli sulle prestazioni dell'applicazione. Usando una query di analisi, è possibile definire le condizioni per le azioni da eseguire, in base ai dati dell'applicazione. |
| Metrica | Application Insights | Non supportati nell'esperienza Avvisi. Questi avvisi sono disponibili in Avvisi (versione classica) |
| Test di disponibilità | Application Insights | Non supportati nell'esperienza Avvisi. Questi avvisi sono disponibili in Avvisi (versione classica) |


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come usare la nuova esperienza Avvisi per creare, visualizzare e gestire gli avvisi](monitor-alerts-unified-usage.md)
- [Informazioni sugli avvisi del log nell'esperienza Avvisi](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi metriche nell'esperienza Avvisi](monitoring-near-real-time-metric-alerts.md)
- [Informazioni sugli avvisi del log attività nell'esperienza Avvisi](monitoring-activity-log-alerts-new-experience.md)
