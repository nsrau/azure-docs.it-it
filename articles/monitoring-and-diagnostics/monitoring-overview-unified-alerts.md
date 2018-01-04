---
title: Esplorare la nuova esperienza di avvisi (anteprima) in Monitor di Azure | Documenti Microsoft
description: "Comprendere come i nuovi avvisi semplici e scalabili esperienza in Azure permette di creazione, visualizzazione e la gestione degli avvisi più semplici"
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
ms.date: 12/12/2017
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 5ded43548d9aea106c6e083476df4e735b8c00a6
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Esplorare la nuova esperienza di avvisi (anteprima) in Monitor di Azure

## <a name="overview"></a>Panoramica
 Avvisi esperienza in Azure è un nuovo aspetto e funzionalità aggiornate. Questa nuova esperienza è disponibile il **avvisi (anteprima)** scheda in Monitor di Azure. Di seguito sono indicati alcuni dei vantaggi dell'utilizzo la nuova esperienza di avvisi (anteprima):

 - **Separazione di avvisi generato e regole di avviso** : nella esperienza Alerts(Preview) Avviso regole (la definizione della condizione che attiva un avviso) e si differenzino solo gli avvisi generati (un'istanza di esecuzione della regola di avviso), pertanto l'operative e viste di configurazione sono separate. 
 - **Oggetto unificata esperienza per gli avvisi di metrica e di log di creazione** - nuovi avvisi (anteprima) authoring guide di esperienza utente lungo il processo di configurazione di una regola di avviso, che rende più semplice individuare le funzioni nel modo corretto per ricevere gli avvisi nel. 
 - **Visualizzazione generato gli avvisi di Log Analitica nel portale di Azure** -esperienza In avvisi (anteprima), è ora possibile anche vedere generato gli avvisi di Log Analitica nella sottoscrizione.  

Nelle sezioni seguenti vengono descritti, in modo più dettagliato, funzionamento la nuova esperienza. 

## <a name="taxonomy"></a>Tassonomia
L'esperienza Alerts(preview) Usa seguenti concetti per separare gli oggetti regola di avviso e di avviso generato durante unificare l'esperienza di creazione tra tipi diversi di avviso.

- **Risorsa di destinazione** -una destinazione può essere qualsiasi risorsa di Azure. Risorsa di destinazione che definisce l'ambito e segnali disponibili per gli avvisi. Destinazioni di esempio: una macchina virtuale, un account di archiviazione, un set di scalabilità della macchina virtuale, un Log Analitica area di lavoro o soluzione. 

- **Criteri** : i criteri sono una combinazione di segnale e applicata la logica su una risorsa di destinazione. Esempi: Percentuale CPU > 70%, tempo di risposta Server > 4 ms, il conteggio di risultati di un log di query > 100 e così via. 

- **Segnale** - segnala vengono generati dalla risorsa di destinazione e può essere di tipi diversi. Questa versione di anteprima supporta i tipi di segnali metrica e di Log.

- **Logica** -logica definita dall'utente per verificare se il segnale è all'interno di previsto o valori di intervallo.  
 
- **Azione** -una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un indirizzo o la registrazione in un URL del webhook). Le notifiche possono in genere attivare più azioni. I tipi di avviso supportati in questa versione di anteprima, gruppi di azioni di supporto.  
 
- **Regola di avviso** -la definizione di una condizione che attiverà l'avviso. In questa versione di anteprima, la regola di avviso acquisisce la destinazione e i criteri per gli avvisi. La regola di avviso può essere un abilitato o uno stato disabilitato. 
 
- **Generato avviso** -creato quando viene generata una regola di avviso abilitata. L'oggetto di avvisi attivato può essere generato o uno stato non risolto.

    > [!NOTE]
    > Questa è diversa dall'esperienza di avvisi corrente in cui l'avviso rappresenta la regola e avviso generato e pertanto può essere in uno stato di avviso, attivo o disabilitato.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Posizione unica da visualizzare e gestire gli avvisi
L'obiettivo dell'esperienza avvisi (anteprima) è il luogo solo per visualizzare e gestire tutti gli avvisi di Azure. Le sottosezioni seguenti descrivono le funzioni di ogni singola schermata della nuova esperienza.

### <a name="alerts-preview-overview-page"></a>Pagina Panoramica avvisi (anteprima)
**Monitoraggio, avvisi (anteprima)** pagina Panoramica Mostra riepilogo aggregata di tutti gli avvisi attivati e totale configurato abilitato le regole di avviso. Viene inoltre un elenco di tutti gli avvisi attivati. Modifica le sottoscrizioni o i parametri di filtro Aggiorna le aggregazioni e gli avvisi generati elenco.

> [!NOTE]
> Avvisi attivati visualizzati negli avvisi (anteprima) sono limitati agli avvisi di log e metrica supportati; Monitoraggio di Azure Mostra numero di avvisi attivati inclusi quelli meno recenti di avvisi di Azure

 ![Cenni preliminari su avvisi anteprima](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gestione delle regole di avviso
**Monitoraggio, avvisi (anteprima) > regole** è una singola pagina per gestire tutte le regole di avviso tra le sottoscrizioni di Azure. Elenca tutte le regole di avviso (abilitato o disabilitato) e possono essere ordinati in base alle risorse di destinazione, i gruppi di risorse, il nome della regola o stato. Anche le regole di avviso possono essere attivata e disattivata o modificate da questa pagina.  

 ![regole di anteprima di avvisi](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Esperienza di creazione in tutte le origini di monitoraggio di un avviso
Esperienza in avvisi (anteprima), gli avvisi possono essere creati in modo coerente indipendentemente dal servizio di monitoraggio o tipo di segnale. Tutti gli avvisi generati e relativi dettagli sono disponibili in una singola pagina.  
 
La creazione di un avviso è un'attività in tre passaggi in cui l'utente sceglie innanzitutto una destinazione per l'avviso, quindi selezionando il segnale di destra e quindi specificare la logica da applicare al segnale come parte della regola di avviso. Questo processo di creazione semplificato non richiede all'utente di conoscere il monitoraggio di origine o segnali supportati prima di selezionare una risorsa di Azure. L'esperienza di creazione comune Filtra l'elenco di segnali disponibili in base a una risorsa di destinazione selezionata automaticamente e consente la creazione della logica di avviso

Per altre informazioni su come creare i seguenti tipi di avviso [qui](monitor-alerts-unified-usage.md). 
- Metrici avvisi (denominati in tempo quasi reale metrica avvisi esperienza corrente)
- Avvisi del registro (Log Analitica)
 

## <a name="alert-types-supported-in-this-preview"></a>Tipi di avviso supportati in questa versione di anteprima


| **Tipo di segnale** | **Origine di monitoraggio** | **Descrizione** | 
|-------------|----------------|-------------|
| Metrica | Monitoraggio di Azure | Chiamato [ **gli avvisi in tempo quasi reale metrica** ](monitoring-near-real-time-metric-alerts.md) esperienza corrente, questi avvisi metrica supporta la valutazione delle condizioni di metrica con una frequenza 1 minuto e consentire per le regole più metriche. È disponibile un elenco di tipi di risorse supportati [qui](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Altre metriche avvisi in base a quanto definito [qui](monitoring-overview-alerts.md#alerts-in-different-azure-services) non sono supportate nell'esperienza avvisi (anteprima).|
| Log  | Log Analytics | Ricevere notifiche o eseguire azioni automatizzate quando una query di ricerca Log sui dati di metrica e/o evento soddisfa determinati criteri.|
| Log  | Log attività | Non supportato nell'esperienza di avvisi (anteprima). |
| Log  | Application Insights | Non supportato nell'esperienza di avvisi (anteprima). |
| Metrica | Application Insights | Non supportato nell'esperienza di avvisi (anteprima). |
| Test di disponibilità | Application Insights | Non supportato nell'esperienza di avvisi (anteprima). |


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come utilizzare la nuova esperienza di avvisi (anteprima) per creare, visualizzare e gestire gli avvisi](monitor-alerts-unified-usage.md)
- [Informazioni sugli avvisi di log nell'esperienza di avvisi (anteprima)](monitor-alerts-unified-log.md)
- [Informazioni sulle metrici avvisi nell'esperienza di avvisi (anteprima)](monitoring-near-real-time-metric-alerts.md)

