---
title: Esplorare la nuova esperienza Avvisi (anteprima) in Monitoraggio di Azure | Microsoft Docs
description: "Comprendere come la nuova esperienza semplice e scalabile di Azure per gli avvisi consente di creare, visualizzare e gestire gli avvisi con maggiore facilità"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 316dcd53509897a6efc387749ca6f9ec268cb7ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Esplorare la nuova esperienza Avvisi (anteprima) in Monitoraggio di Azure

## <a name="overview"></a>Panoramica
 L'esperienza Avvisi di Azure è stata migliorata con una nuova grafica e funzionalità aggiornate. La nuova esperienza è accessibile dalla scheda **Avvisi (anteprima)** in Monitoraggio di Azure. Tra i vantaggi offerti dalla nuova esperienza Avvisi (anteprima) sono inclusi:

 - **Separazione tra avvisi attivati e regole di avviso**: l'esperienza Avvisi (anteprima) fa distinzione tra regole di avviso (le definizioni delle condizioni che attivano gli avvisi) e avvisi attivati (le istanze di attivazione delle regole di avviso) e sono pertanto disponibili visualizzazioni separate per le attività operative e la configurazione.
 - **Esperienza unificata per la creazione di avvisi di log e metriche**: la nuova esperienza Avvisi (anteprima) offre una procedura guidata per la configurazione di una regola di avviso. In questo modo è possibile definire più facilmente le condizioni per cui è opportuno ricevere un avviso.
 - **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: nell'esperienza Avvisi (anteprima) è ora possibile visualizzare anche gli avvisi di Log Analytics attivati in una sottoscrizione.  
 - **Esperienza unificata per la creazione di avvisi del log attività**: è ora possibile creare avvisi del log attività direttamente da **Monitoraggio** > **Avvisi (anteprima)**. In precedenza era possibile creare questi avvisi solo tramite **Monitoraggio** > **Log attività**.

Le sezioni seguenti descrivono in modo più dettagliato come funziona la nuova esperienza.

## <a name="taxonomy"></a>Tassonomia
L'esperienza Avvisi (anteprima) si basa sui concetti seguenti per separare gli oggetti di regola di avviso e avviso attivato offrendo al tempo stesso un'esperienza unificata per tipi di avviso diversi.

- **Risorsa di destinazione**: come destinazione può essere configurata una qualsiasi risorsa di Azure. La risorsa di destinazione definisce l'ambito e i segnali disponibili per la generazione di avvisi. Esempi di destinazione: una macchina virtuale, un account di archiviazione, un set di scalabilità di macchine virtuali, una soluzione o un'area di lavoro di Log Analytics.

- **Criteri**: una combinazione di segnale e logica applicata a una risorsa di destinazione. Esempi: Percentuale d'uso della CPU > 70%, Tempo di risposta del server > 4 ms, Numero di risultati di una query su log > 100 e così via. 

- **Segnale**: il segnale emesso dalla risorsa di destinazione. I segnali possono essere di vari tipi. Come tipi di segnale, questa versione di anteprima supporta **Metrica**, **Log attività**, **Application Insights** e **Log**.

- **Logica**: la logica definita dall'utente per verificare se il segnale è compreso nell'intervallo o nei valori previsti.  
 
- **Azione**: una chiamata specifica inviata al destinatario di una notifica, ad esempio l'invio di un indirizzo tramite posta elettronica o la pubblicazione in un URL webhook. Le notifiche possono in genere attivare più azioni. I tipi di avviso supportati in questa versione di anteprima consentono di attivare gruppi di azioni.  
 
- **Regola di avviso**: la definizione di una condizione che attiverà l'avviso. In questa versione di anteprima la regola di avviso acquisisce la destinazione e i criteri per la generazione di avvisi. Lo stato della regola di avviso può essere Abilitato o Disabilitato.
 
- **Avviso attivato**: l'avviso che viene creato al momento dell'attivazione di una regola di avviso abilitata. Lo stato di un oggetto di questo tipo può essere attivato o non risolto.

    > [!NOTE]
    > Questo oggetto si comporta quindi in modo diverso rispetto all'esperienza degli avvisi corrente, dove rappresenta sia la regola sia l'avviso attivato e pertanto può avere uno stato attivo, disabilitato o di avviso.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Un'unica area per visualizzare e gestire gli avvisi
L'obiettivo dell'esperienza Avvisi (anteprima) è quello di offrire un'unica area per visualizzare e gestire tutti gli avvisi di Azure. Le sottosezioni seguenti descrivono le funzioni di ogni schermata della nuova esperienza.

### <a name="alerts-preview-overview-page"></a>Pagina di panoramica Avvisi (anteprima)
La pagina di panoramica **Monitoraggio - Avvisi (anteprima)** mostra un riepilogo aggregato di tutti gli avvisi attivati e di tutte le regole di avviso configurate o abilitate, oltre a un elenco di tutti gli avvisi attivati. Se si cambiano le sottoscrizioni o i parametri di filtro, i dati aggregati e l'elenco degli avvisi attivati vengono aggiornati di conseguenza.

> [!NOTE]
> La visualizzazione degli avvisi attivati in Avvisi (anteprima) è limitata agli avvisi di log e metriche supportati. Monitoraggio di Azure mostra il numero di avvisi attivati, inclusi quelli della versione precedente di Avvisi di Azure.

 ![alerts-preview-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gestione delle regole di avviso
**Monitoraggio - Avvisi (anteprima)>Regole** è una singola pagina da cui è possibile gestire tutte le regole di avviso nelle varie sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso (abilitate o disabilitate), che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. È possibile abilitare o disabilitare o anche modificare le regole di avviso da questa pagina.  

 ![alerts-preview-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Un'unica esperienza per la creazione di avvisi in tutte le origini di monitoraggio
Nell'esperienza Avvisi (anteprima) è possibile creare avvisi in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale. Tutti gli avvisi attivati e i relativi dettagli sono disponibili in un'unica pagina.  
 
La procedura per creare un avviso prevede tre passaggi. L'utente sceglie prima di tutto una destinazione per l'avviso, quindi seleziona il segnale appropriato e infine specifica la logica da applicare al segnale come parte della regola di avviso. Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'esperienza di creazione comune filtra automaticamente l'elenco dei segnali disponibili in base alla risorsa di destinazione selezionata e fornisce indicazioni per definire la logica degli avvisi.

Per altre informazioni su come creare i tipi di avviso seguenti, vedere [questo articolo](monitor-alerts-unified-usage.md).
- Avvisi di metriche (denominati Avvisi metriche near real time nell'esperienza corrente)
- Avvisi di log (Log Analytics)
- Avvisi di log (Log attività)
- Avvisi di log (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Tipi di avviso supportati in questa versione di anteprima


| **Tipo di segnale** | **Origine di monitoraggio** | **Descrizione** | 
|-------------|----------------|-------------|
| Metrica | Monitoraggio di Azure | Denominati [**Avvisi metriche near real time**](monitoring-near-real-time-metric-alerts.md) nell'esperienza corrente, questi avvisi supportano la valutazione delle condizioni delle metriche alla frequenza di un minuto e consentono l'uso di regole relative a più metriche. Un elenco dei tipi di risorse supportati è disponibile in [questa sezione](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Altri avvisi di metriche definiti in [questa sezione](monitoring-overview-alerts.md#alerts-in-different-azure-services) non sono supportati nell'esperienza Avvisi (anteprima).|
| Log  | Log Analytics | Questa categoria di avvisi consente di ricevere notifiche o eseguire ricerche automatizzate quando una query di ricerca log su dati relativi a metriche e/o eventi soddisfa determinati criteri.|
| Log  | Log attività | Questa categoria include i record di tutte le azioni di creazione, aggiornamento ed eliminazione eseguite sulla destinazione selezionata (risorsa/gruppo di risorse/sottoscrizione). |
| Log  | Log di integrità dei servizi | Questa categoria non è supportata nell'esperienza Avvisi (anteprima).   |
| Log  | Application Insights | Questa categoria include i log con i dettagli sulle prestazioni dell'applicazione. Usando una query di analisi, è possibile definire le condizioni per le azioni da eseguire, in base ai dati dell'applicazione. |
| Metrica | Application Insights | Questa categoria non è supportata nell'esperienza Avvisi (anteprima). |
| Test di disponibilità | Application Insights | Questa categoria non è supportata nell'esperienza Avvisi (anteprima). |


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come usare la nuova esperienza Avvisi (anteprima) per creare, visualizzare e gestire gli avvisi](monitor-alerts-unified-usage.md)
- [Informazioni sugli avvisi di log nell'esperienza Avvisi (anteprima)](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi di metriche nell'esperienza Avvisi (anteprima)](monitoring-near-real-time-metric-alerts.md)
- [Informazioni sugli avvisi del log attività nell'esperienza Avvisi (anteprima)](monitoring-activity-log-alerts-new-experience.md)
