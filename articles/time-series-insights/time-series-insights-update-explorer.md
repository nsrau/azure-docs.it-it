---
title: Visualizzare i dati in Esplora anteprime-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle funzionalità e le opzioni disponibili in Esplora Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133429"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Esplora Azure Time Series Insights Preview

Questo articolo descrive le varie funzionalità e opzioni disponibili all'interno dell' [applicazione Web demo](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview.

## <a name="prerequisites"></a>prerequisiti

Per iniziare a usare Esplora Azure Time Series Insights Preview, è necessario:

* Eseguire il provisioning di un ambiente Time Series Insights. Per altre informazioni sul provisioning di un'istanza, vedere l'esercitazione [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Fornire l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights creato per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine evento all'ambiente Time Series Insights per eseguire il push dei dati nell'ambiente:
  * Informazioni [su come connettersi a un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Informazioni [su come connettersi a un hub](./time-series-insights-how-to-add-an-event-source-iothub.md) Internet

## <a name="explore-the-time-series-insights-preview-explorer"></a>Esplora Time Series Insights Preview Explorer

Azure Time Series Insights Preview Explorer è costituito dai sette elementi seguenti:

[Panoramica di ![Time Series Insights Preview Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Pannello ambiente](#1-environment-panel): Visualizza tutti gli ambienti di Azure Time Series Insights.
1. [Barra di spostamento](#2-navigation-bar): consente di spostarsi tra le pagine **analizza** e **modello** .
1. [Albero gerarchia e pannello di ricerca](#3-hierarchy-tree-and-search-panel): consente di selezionare e cercare elementi dati specifici da tracciare.
1. [Time Series well](#4-time-series-well): Mostra tutti gli elementi dati attualmente selezionati.
1. [Pannello grafico](#5-chart-panel): Visualizza il grafico di lavoro corrente.
1. [Sequenza temporale](#6-time-editor-panel): consente di modificare l'intervallo di tempo di lavoro.
1. [Barra dell'app](#7-app-bar): contiene le opzioni di gestione degli utenti, ad esempio il tenant corrente, e consente di modificarle e le impostazioni della lingua.


## <a name="1-environment-panel"></a>1. pannello ambiente

Il panello degli ambienti visualizza tutti gli ambienti Time Series Insights a cui si ha accesso. L'elenco include gli ambienti con pagamento in base al consumo (anteprima) e gli ambienti S1/S2 (disponibilità generale). È sufficiente fare clic sull'ambiente Time Series Insights che si vuole usare per immediatamente.

1. Selezionare la freccia a discesa accanto all'ambiente visualizzato.

   [pannello ambiente ![](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Quindi selezionare l'ambiente desiderato.

## <a name="2-navigation-bar"></a>2. barra di spostamento

  [![barra di spostamento](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

Utilizzare la barra di navigazione per scegliere tra due visualizzazioni:

* **Analizza**: è possibile usare questa funzionalità per progettare ed eseguire analisi avanzate sui dati di serie temporali modellati o non modellati.
* **Modello**: usarlo per eseguire il push di nuovi tipi di Time Series Insights di anteprima, gerarchie e istanze nel modello di Time Series Insights.

### <a name="model-authoring"></a>Creazione di modelli

Il Azure Time Series Insights anteprima supporta operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) complete sul modello Time Series.

[![il pannello di ricerca del modello](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo di modello Time Series**: è possibile usare i tipi di Time Series Insights per definire le variabili o le formule per l'esecuzione di calcoli. Esse sono associate a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia del modello Time Series**: le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza del modello Time Series**: le istanze sono le serie temporali. Nella maggior parte dei casi sono **DeviceID** o **AssetID**, che è l'identificatore univoco dell'asset nell'ambiente.

Per altre informazioni sul Modello serie temporale, vedere [Modelli serie temporale](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. albero gerarchico e pannello di ricerca

L'albero gerarchico e il pannello di ricerca consentono di eseguire facilmente ricerche e spostamenti nella gerarchia dei [modelli Time Series](./time-series-insights-update-tsm.md) per individuare le istanze di serie temporali specifiche che si desidera visualizzare nel grafico. Quando si selezionano le istanze, queste non vengono aggiunte solo al grafico corrente, ma vengono aggiunte anche all'area dati. 

[albero ![gerarchia e pannello di ricerca](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

Il riquadro Risultati ricerca consente inoltre di visualizzare i risultati in una visualizzazione gerarchica o in una visualizzazione elenco, semplificando la ricerca delle istanze che si desidera visualizzare.
 
## <a name="4-time-series-well"></a>4. serie temporali

Il pozzetto Visualizza i campi dell'istanza e altri metadati associati alle istanze di Time Series Insights selezionate. Selezionando le caselle di controllo sul lato destro, è possibile nascondere o visualizzare istanze specifiche del grafico corrente. 

  [![l'anteprima](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

È possibile rimuovere elementi di dati specifici dall'area dati correnti selezionando il controllo di **eliminazione** rossa (possibile cestino) sul lato sinistro dell'elemento. Il bene consente inoltre di controllare la modalità di visualizzazione di ogni elemento nel grafico. È possibile scegliere di aggiungere le ombreggiature min/max, i punti dati, spostare l'elemento nel tempo e visualizzare l'istanza in modo graduale. 

Il controllo esplorazioni consente inoltre di creare facilmente turni temporali e grafici a dispersione.  

  [opzioni di layout ![bene](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se viene visualizzato il messaggio seguente, l'istanza non contiene dati durante l'intervallo di tempo selezionato. Per risolvere il problema, aumentare l'intervallo di tempo o verificare che l'istanza stia eseguendo il push dei dati.
>
> ![Nessuna notifica sui dati](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5. pannello grafico

Il grafico consente di visualizzare le istanze di serie temporali come righe. È possibile comprimere il pannello dell'ambiente, il modello di dati e il pannello di controllo dell'intervallo di tempo facendo clic sui controlli Web per ingrandire il grafico. 

  [Panoramica del grafico di anteprima ![](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **Tipo di grafico**: controlla gli elementi dati disponibili per la visualizzazione.

1. **Dimensioni intervallo**: lo strumento Slider dimensioni intervallo consente di ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Questo strumento garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità, consentendo di visualizzare tendenze uniformi in sezioni temporali riferite a millisecondi e pertanto di mostrare sezioni di dati con granularità e risoluzione superiori. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, ovvero risoluzione del bilanciamento, velocità della query e granularità.

1. **Zoom e panoramica**: fare clic su questo controllo per applicare lo zoom e il panning al grafico.

1. **Controllo asse y**: scorre le opzioni di visualizzazione asse y disponibili:

    * `Stacked`: ogni riga ha un singolo asse Y.
    * `Overlap`: utilizzarlo per eseguire lo stack di più righe sullo stesso asse Y, con la modifica dei dati dell'asse Y in base alla riga selezionata.
    * `Shared`: tutti i dati dell'asse Y visualizzati insieme.

1. **Elemento marker**: l'elemento dati attualmente selezionato e i relativi dettagli associati.

È possibile analizzare ulteriormente una sezione di dati specifica facendo **clic** con il pulsante destro del mouse su un punto dati nel grafico corrente tenendo premuto il mouse e trascinando l'area selezionata sull'endpoint desiderato. **Fare clic con il pulsante destro del mouse** sull'area grigia selezionata e scegliere **Zoom** , come illustrato di seguito. È anche possibile visualizzare e scaricare gli eventi di telemetria nell'intervallo di tempo selezionato.

  [zoom del grafico ![anteprima](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Dopo aver eseguito l'azione di **Zoom** , viene visualizzato il set di dati selezionato. Selezionare il controllo del formato per scorrere le tre rappresentazioni dell'asse y dei dati del Time Series Insights.

  [![l'asse y del grafico di anteprima](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

Qui è possibile vedere un esempio di **grafico sovrapposto**:

  [![opzione grafico sovrapposto](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

Il pulsante **altre azioni** si espande per visualizzare le opzioni **Scarica come CSV** ed **Esporta in Power bi** .

  [opzione ![altre azioni](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Vedere informazioni sul [connettore Time Series Insights Power bi nativo](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. pannello Editor temporale

Quando si lavora con Time Series Insights si seleziona per prima cosa un intervallo di tempo. L'intervallo di tempo selezionato controllerà il set di dati disponibile per la manipolazione con i widget di aggiornamento Time Series Insights.

  [![Pannello di selezione di data e ora](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> Una parte della sequenza temporale viene evidenziata in un colore ambrato o arancione per indicare l'intervallo di dati disponibile in warm Store.

I controlli Web seguenti sono disponibili nell'aggiornamento Time Series Insights per la selezione dell'intervallo di tempo di lavoro. 

  [controllo ben esplorato ![](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Controllo dispositivo di scorrimento intervallo di date interno**: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal controllo dispositivo di scorrimento dell'intervallo di date esterno.

1. **Aumentare e ridurre i pulsanti di intervallo di date**: aumentare o ridurre l'intervallo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

1. **Controllo di compressione intervallo di tempo**: questo controllo Web consente di nascondere tutti i controlli ad eccezione dello strumento dispositivo di scorrimento intervallo di date interno.

1. **Controllo dispositivo di scorrimento intervallo di date esterno**: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

1. **Controllo dispositivo di scorrimento intervallo di tempo**: consente di passare rapidamente da una selezione a un intervallo di tempo preimpostato, ad esempio gli ultimi **30 minuti**, le **ultime 12 ore**o un **intervallo personalizzato**. Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

   [![da e verso il pannello di selezione](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. barra dell'app

Il pannello di navigazione Time Series Insights anteprima viene visualizzato nella parte superiore dell'app Time Series Insights. Fornisce le funzionalità seguenti:

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  [icona di condivisione ![](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

Selezionare l'icona nuova **condivisione** per condividere un collegamento URL con il team.

  [![condividere l'URL dell'istanza](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sezione tenant

  [selezione del tenant ![](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* Visualizza le informazioni correnti sull'account di accesso Time Series Insights.
* Usarlo per passare tra i temi Time Series Insights disponibili.
* Usarlo per visualizzare l'anteprima dell' [app Web demo](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Selezione tema

Per selezionare un nuovo tema, selezionare l'icona del profilo nell'angolo in alto a destra. Selezionare quindi **Cambia tema**.

  [Selezione tema ![](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> La selezione della lingua è disponibile anche selezionando l'icona del profilo.

Anteprima di Azure Time Series Insights supporta due temi:

* **Tema chiaro**: il tema predefinito illustrato in questo documento.
* **Tema scuro**: esegue il rendering di Esplora risorse come illustrato di seguito:

  [![tema scuro selezionato](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="preview-terms-panel"></a>Pannello termini anteprima

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. Potrebbe essere necessario usare il prodotto e l'anteprima disponibili a livello generale in combinazione. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2. 

Al posto della gerarchia, viene visualizzato il pannello Time Series Insights termini, in cui si definiscono le query nell'ambiente in uso. Usarlo per filtrare i dati in base a un predicato.

  [![dove pannello query](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Where**: utilizzare la clausola WHERE per filtrare rapidamente gli eventi utilizzando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operando supportati sono i seguenti:

| Operazione | Tipi supportati   | note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | String | Sul lato destro sono consentiti solo valori letterali stringa costanti. Non sono consentite stringhe vuote e NULL. |

Per ulteriori informazioni sulle operazioni di query e sui tipi di dati supportati, vedere l'articolo relativo all' [espressione Time Series (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Esempi di clausole WHERE

  [esempi di clausole WHERE ![](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**Measure**: elenco a discesa che consente di visualizzare tutte le colonne numeriche (**Double**) che è possibile utilizzare come elementi per il grafico corrente.

**Split by**: questo elenco a discesa consente di visualizzare tutte le colonne categoriche (stringhe) disponibili nel modello in cui è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini per visualizzare sullo stesso asse x. Immettere i parametri desiderati e quindi selezionare **Aggiungi** per aggiungere un nuovo termine.

  [![sottoposto a query e filtrato una vista](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

È possibile mostrare e nascondere gli elementi nel pannello del grafico selezionando l'icona visibile, come illustrato nella figura seguente. Per rimuovere completamente le query, selezionare la **X**rossa.

  [![annullare un'opzione sottoposta a query e filtrata](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [archiviazione e](./time-series-insights-update-storage-ingress.md) il traffico in ingresso nella Azure Time Series Insights Preview.

- Leggere il documento Time Series Insights anteprima sulla [modellazione dei dati](./time-series-insights-update-tsm.md).

- Informazioni [su come diagnosticare e risolvere i problemi relativi all'istanza di](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights.
