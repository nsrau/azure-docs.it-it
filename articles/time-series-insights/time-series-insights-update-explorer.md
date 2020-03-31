---
title: Visualizzare i dati in Esplora anteprima - Azure Time Series Insights . Documenti Microsoft
description: Informazioni sulle funzionalità e le opzioni disponibili in Esplora anteprima di Azure Time Series Insights.Learn about features and options available in the Azure Time Series Insights Preview explorer.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861762"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Esplora anteprima di Azure Time Series InsightsAzure Time Series Insights Preview explorer

Questo articolo descrive le varie funzionalità e opzioni disponibili [nell'applicazione Web demo](https://insights.timeseries.azure.com/preview/demo)di Azure Time Series Insights Preview.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare Azure Time Series Insights Preview explorer, è necessario:To get started with the Azure Time Series Insights Preview explorer, you must:

* Disporre di un ambiente Time Series Insights. Per altre informazioni sul provisioning di un'istanza, leggere l'esercitazione Azure Time Series Insights Preview.Learn more about provisioning an instance by reading the [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) tutorial.
* [Fornire l'accesso](./time-series-insights-data-access.md) ai dati all'ambiente Time Series Insights creato per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine eventi all'ambiente Time Series Insights per eseguire il push dei dati nell'ambiente:Add an event source to the Time Series Insights environment to push data to the environment:
  * Informazioni su [come connettersi a un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * [Scopri come connetterti a un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Esplora l'Esplora anteprima di Time Series Insights

Azure Time Series Insights Preview explorer è costituito dai sette elementi seguenti:The Azure Time Series Insights Preview explorer consists of the following seven elements:

[![Panoramica di Time Series Insights Preview Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Pannello Ambiente:](#1-environment-panel)visualizza tutti gli ambienti Azure Time Series Insights.Environment panel : Displays all your Azure Time Series Insights environments.
1. [Barra di navigazione](#2-navigation-bar): Consente di passare tra le pagine **Analizza** e **Modello.**
1. [Albero della gerarchia e pannello di ricerca](#3-hierarchy-tree-and-search-panel): Consente di selezionare e cercare elementi di dati specifici da tracciare.
1. [Serie temporali:](#4-time-series-well)mostra tutti gli elementi di dati attualmente selezionati.
1. [Grafico:](#5-chart-panel)visualizza il grafico di lavoro corrente.
1. [Sequenza temporale](#6-time-editor-panel): consente di modificare l'intervallo di tempo di lavoro.
1. [Barra dell'app](#7-app-bar): Contiene le opzioni di gestione degli utenti (ad esempio il tenant corrente) e consente di modificare le impostazioni della lingua e della lingua.


## <a name="1-environment-panel"></a>1. Pannello Ambiente

Il panello degli ambienti visualizza tutti gli ambienti Time Series Insights a cui si ha accesso. L'elenco include gli ambienti con pagamento in base al costo (anteprima) e gli ambienti S1/S2 (disponibilità generale). È sufficiente selezionare l'ambiente Time Series Insights che si desidera utilizzare per essere immediatamente portato lì.

1. Selezionare la freccia a discesa accanto all'ambiente visualizzato.

   [![Pannello Ambiente](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Selezionare quindi l'ambiente desiderato.

## <a name="2-navigation-bar"></a>2. Barra di navigazione

  [![La barra di navigazione](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Utilizzare la barra di navigazione per scegliere tra due viste:

* **Analizza**: Usalo per creare grafici ed eseguire analisi avanzate sui dati delle serie temporali modellati o non modellati.
* **Modello:** usalo per eseguire il push di nuovi tipi, gerarchie e istanze di Time Series Insights nel modello Time Series Insights.

### <a name="model-authoring"></a>Creazione di modelli

Azure Time Series Insights Preview supporta le operazioni complete di creazione, lettura, aggiornamento ed eliminazione (CRUD) nel modello Time Series.The Azure Time Series Insights Preview supports full create, read, update, and delete (CRUD) operations on your Time Series Model.

[![Il pannello di ricerca del modello](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo di modello Time Series**: è possibile utilizzare i tipi Time Series Insights per definire variabili o formule per l'esecuzione di calcoli. Sono associati a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia del modello Time Series**: Le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza modello Time Series**: Le istanze sono le serie temporali stesse. Nella maggior parte dei casi, si tratta di **DeviceID** o **AssetID**, ovvero l'identificatore univoco dell'asset nell'ambiente.

Per ulteriori informazioni sul modello Time Series, leggere [Times Series Models](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Albero della gerarchia e pannello di ricerca

L'albero della gerarchia e il riquadro di ricerca consentono di cercare ed esplorare facilmente la gerarchia [del modello Time Series](./time-series-insights-update-tsm.md) per trovare le istanze specifiche delle serie temporali che si desidera visualizzare nel grafico. Quando si selezionano le istanze, non solo vengono aggiunte al grafico corrente, ma vengono anche aggiunte al riquadro dei dati. 

[![Albero gerarchico e pannello di ricerca](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Il riquadro dei risultati di ricerca consente inoltre di visualizzare i risultati in una visualizzazione gerarchia o in una visualizzazione elenco, semplificando l'individuazione delle istanze che si desidera visualizzare.
 
## <a name="4-time-series-well"></a>4. Serie temporale bene

Il pozzo visualizza i campi di istanza e altri metadati associati alle istanze di Time Series Insights selezionate. Selezionando le caselle di controllo sul lato destro, è possibile nascondere o visualizzare istanze specifiche dal grafico corrente. 

  [![Il pozzo Anteprima](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

È possibile rimuovere elementi di dati specifici dal riquadro dei dati corrente selezionando il controllo rosso **Elimina** (cestino) sul lato sinistro dell'elemento. Il pozzo consente inoltre di controllare la modalità di visualizzazione di ogni elemento nel grafico. È possibile scegliere di aggiungere ombreggiature min/max, punti dati, spostare l'elemento nel tempo e visualizzare l'istanza in modo steprato. 

Inoltre, il controllo delle esplorazioni consente di creare facilmente spostamenti temporali e grafici a dispersione.  

  [![Opzioni di layout Pozzo](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se viene visualizzato il messaggio seguente, l'istanza non dispone di dati durante l'intervallo di tempo selezionato. Per risolvere il problema, aumentare l'intervallo di tempo o verificare che l'istanza stia spingendo i dati.
>
> ![Nessuna notifica dati](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Pannello grafico

Il grafico consente di visualizzare le istanze di serie temporali come linee. È possibile comprimere il pannello dell'ambiente, il modello di dati e il pannello di controllo dell'intervallo di tempo facendo clic sui controlli Web per ingrandire il grafico. 

  [![Panoramica del grafico di anteprima](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo di grafico:** controlla quali elementi di dati sono disponibili per la visualizzazione.

1. **Dimensione intervallo**: Lo strumento di scorrimento Dimensione intervallo consente di ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Ciò fornisce un controllo più preciso del movimento tra grandi sezioni di tempo che mostrano tendenze uniformi fino a fette piccole come il millisecondo, consentendo di esaminare tagli granulari e ad alta risoluzione dei dati. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, ovvero risoluzione del bilanciamento, velocità della query e granularità.

1. **Ingrandimento e panoramica:** selezionare questo controllo per eseguire lo zoom e la panoramica del grafico.

1. **Controllo dell'asse Y**: Consente di scorrere le opzioni di visualizzazione dell'asse Y disponibili:

    * `Stacked`: ogni linea ha un singolo asse Y.
    * `Overlap`: consente di impilare più linee sullo stesso asse Y, con la modifica dei dati dell'asse Y in base alla linea selezionata.
    * `Shared`: tutti i dati dell'asse Y visualizzati insieme.

1. **Elemento Marker**: L'elemento dati attualmente selezionato e i dettagli associati.

È possibile eseguire ulteriormente il drill-down di una sezione di dati specifica facendo clic con il **pulsante sinistro** del mouse su un punto dati nel grafico corrente tenendo premuto il mouse e trascinando l'area selezionata nel punto finale desiderato. **Fare clic** con il pulsante destro del mouse sull'area blu selezionata e quindi selezionare **Ingrandimento** come illustrato di seguito. È anche possibile visualizzare e scaricare gli eventi di telemetria nell'intervallo di tempo selezionato.

  [![Anteprima zoom grafico](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Dopo aver **eseguito** l'azione di zoom, verrà visualizzato il set di dati selezionato. Selezionare il controllo formato per scorrere le tre rappresentazioni dell'asse y dei dati di Time Series Insights.

  [![Anteprima dell'asse y del grafico](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Di seguito viene fornito un esempio di grafico sovrapposto:Here, an example of an **overlapping chart** is provided:

  [![Opzione Grafico sovrapposto](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Il pulsante **Altre azioni** si espande per visualizzare le opzioni Scarica **come CSV**, Connetti a **Power BI**, Mostra dati grafico come **tabella**ed Esplora eventi **non elaborati.**

  [![Opzione Altre azioni](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Per altre informazioni sull'opzione **Connetti a Power BI,** vedere [Connettore Power BI nativo](concepts-power-bi.md)di Time Series Insights .

## <a name="6-time-editor-panel"></a>6. Pannello editor temporale

Quando si lavora con Time Series Insights, si selezionerà per la prima volta un intervallo di tempo. L'intervallo di tempo selezionato controllerà il set di dati disponibile per la manipolazione con i widget di aggiornamento Time Series Insights.

  [![Pannello di selezione di data e ora](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Una parte della sequenza temporale viene evidenziata in un colore ambrato o arancione per indicare l'intervallo di dati disponibili in un archivio caldo.

Nell'aggiornamento Time Series Insights è disponibile i controlli Web seguenti per la selezione dell'intervallo di tempo di lavoro. 

  [![Controllo dei pozzi di esplorazione](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. Controllo dispositivo di **scorrimento dell'intervallo**di date interno: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal controllo dispositivo di scorrimento dell'intervallo di date esterno.

1. **Aumentare e ridurre i pulsanti dell'intervallo**di date : Aumentare o ridurre l'intervallo di tempo selezionando uno dei pulsanti per l'intervallo desiderato.

1. **Controllo di compressione dell'intervallo**di tempo: questo controllo Web consente di nascondere tutti i controlli ad eccezione dello strumento di scorrimento dell'intervallo di date interno.

1. Controllo dispositivo di **scorrimento dell'intervallo**di date esterno: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

1. Controllo dispositivo di **scorrimento intervallo**temporale : utilizzarlo per passare rapidamente tra le selezioni dell'intervallo di tempo preimpostato, ad esempio gli ultimi **30 minuti,** le **ultime 12 ore**o un **intervallo personalizzato.** Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

   [![Da e verso il pannello di selezione](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Barra dell'app

Il pannello di navigazione Anteprima Time Series Insights viene visualizzato nella parte superiore dell'app Time Series Insights. Fornisce le seguenti funzionalità:

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  [![Icona di condivisione](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Seleziona la nuova icona **Condividi** per condividere un link URL con il tuo team.

  [![Condividere l'URL dell'istanza](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sezione tenant

  [![Selezione tenant](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Visualizza le informazioni correnti sull'account di accesso Time Series Insights.
* Usalo per passare tra i temi di Time Series Insights disponibili.
* Utilizzarlo per visualizzare [l'anteprima demo app web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Selezione tema

Per selezionare un nuovo tema, selezionare l'icona del profilo nell'angolo superiore destro. Selezionare **quindi Cambia tema**.

  [![Selezione tema](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> La selezione della lingua è disponibile anche selezionando l'icona del profilo.

Anteprima di Azure Time Series Insights supporta due temi:

* **Tema chiaro**: Il tema predefinito mostrato in tutto il documento.
* **Tema scuro**: Esegue il rendering dell'esploratore come illustrato di seguito:

  [![Tema scuro selezionato](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="preview-terms-panel"></a>Pannello dei termini di anteprima

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. È possibile utilizzare il prodotto disponibile in generale e Anteprima in combinazione. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2. 

Invece della gerarchia, viene visualizzato il riquadro dei termini di Time Series Insights. Il pannello dei termini consente di definire le query nell'ambiente. Utilizzarlo per filtrare anche i dati in base a un predicato.

  [![Posizione del riquadro di query](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Dove**: utilizzare la clausola where per filtrare rapidamente gli eventi utilizzando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operando supportati includono quanto segue:

| Operazione | Tipi supportati   | Note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | string | Sul lato destro sono consentiti solo valori letterali stringa costante. Stringa vuota e NULL non sono consentiti. |

Per ulteriori informazioni sulle operazioni di query e sui tipi di dati supportati, vedere [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Esempi di clausole where

  [![Esempi di clausola Where](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Misura**: Un elenco a discesa che visualizza tutte le colonne numeriche (**Doubles**) che è possibile utilizzare come elementi per il grafico corrente.

**Dividi per:** questo elenco a discesa visualizza tutte le colonne di categoria disponibili (Stringhe) nel modello in base alle quali è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini per visualizzare sullo stesso asse x. Immettere i parametri desiderati e quindi selezionare **Aggiungi** per aggiungere un termine nuovo.

  [![Queryed e visualizzazione filtrata uno](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

È possibile mostrare e nascondere gli elementi nel pannello grafico selezionando l'icona visibile, come illustrato nell'immagine seguente. Per rimuovere completamente le query, selezionare la **X**rossa .

  [![Annullare un'opzione sottoposta a query e filtrata](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sull'archiviazione e l'ingresso](./time-series-insights-update-storage-ingress.md) in Azure Time Series Insights Preview.Learn about storage and ingress in the Azure Time Series Insights Preview.

- Leggere il documento Time Series Insights Preview sulla [modellazione dei dati.](./time-series-insights-update-tsm.md)

- [Scopri come diagnosticare e risolvere i problemi dell'istanza](./time-series-insights-update-how-to-troubleshoot.md) di Time Series Insights.
