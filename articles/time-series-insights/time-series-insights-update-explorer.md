---
title: Visualizzazione dei dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive le funzionalità e le opzioni disponibili nell'app Web dello strumento di esplorazione di Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399868"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizzare i dati nella versione di anteprima dello strumento di esplorazione

Questo documento descrive l'interfaccia utente e le funzionalità di esperienza utente e di anteprima di Insights di Azure ora serie [app web demo](https://insights.timeseries.azure.com/preview/demo). In particolare, viene illustrato il layout dell'hosting di esempio, le opzioni di personalizzazione dell'interfaccia e navigazione attraverso la dimostrazione fornita.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare Esplora la versione di anteprima di Azure ora Series Insights, è necessario:

* Avere un ambiente Time Series Insights configurato. Per altre informazioni sul provisioning di un'istanza, provare il [versione di anteprima di Azure ora Series Insights](./time-series-insights-update-create-environment.md) esercitazione.
* [Fornire l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights creata per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine evento all'ambiente Time Series Insights ai dati di push per l'ambiente:
  * Scopri [come connettersi a un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Scopri [come connettersi a un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Informazioni su Esplora l'anteprima

Lo strumento di esplorazione di Anteprima di Azure Time Series Insights è costituito dagli elementi seguenti:

[![La vista di esplorazione](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Pannello ambiente</a>: Consente di visualizzare gli ambienti Azure Time Series Insights.
- <a href="#navigation-menu">Menu di navigazione</a>: Usarlo per spostarsi tra i **Analyze** e **modello** pagine.
- <a href="#hierarchy-tree">Albero gerarchico</a>: Usarlo per selezionare elementi specifici di modello e i dati da tracciare.
- <a href="#preview-well">Area delle serie temporali</a>: Visualizza gli elementi di dati attualmente selezionata nel formato di tabella con codifica a colori.
- <a href="#preview-chart">Pannello del grafico</a>: visualizza il grafico di lavoro corrente.
- <a href="#time-editor-panel">Sequenza temporale</a>: Usarlo per modificare l'intervallo di tempo di lavoro.
- <a href="#navigation-panel">Barra delle app</a>: Contiene le opzioni di gestione utente, ad esempio il tenant corrente. È possibile usarlo per modificare le impostazioni di tema e della lingua.

## <a name="environment-drop-down-list"></a>Riepilogo ambiente

Elenco a discesa ambiente consente di visualizzare tutti gli ambienti Time Series Insights che è possibile utilizzare. L'elenco include gli ambienti con pagamento a consumo, ad esempio la versione di anteprima di tempo Series Insights. L'elenco include anche gli ambienti S1 o S2, che sono disponibili a livello generale.

1. Selezionare la freccia giù accanto al proprio ambiente visualizzato.

   [![Il pannello di controllo](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Quindi, selezionare l'ambiente desiderato.

## <a name="navigation-menu"></a>Menu di navigazione

  [![Menu di navigazione](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Usare il menu di navigazione per selezionare tra due viste:

* **Analyze** (Analizza): Usarlo per grafico ed eseguire l'analitica avanzata sui dati delle serie temporali modellato o unmodeled.
* **Model** (Modella): Usarla per eseguire il push nuovi tipi, gerarchie e le istanze Series Insights un'anteprima del modello Time Series Insights.

## <a name="hierarchy-tree"></a>albero gerarchico

L'albero gerarchico consente di visualizzare gli elementi di dati selezionati che includono modelli specifici dispositivi e sensori dei dispositivi.

### <a name="model-search-panel"></a>Pannello ricerca con modello

È possibile utilizzare il pannello di ricerca del modello per cercare facilmente e passare la gerarchia del modello Time Series per trovare le istanze di serie di tempo specifico da visualizzare nel grafico. Dopo aver selezionato le istanze, vengono anche aggiunti per il grafico corrente e i dati.

  [![Il pannello di ricerca del modello](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Creazione di modelli

Supporta la versione di anteprima di Azure ora Series Insights completa crea, leggere, aggiornarla ed eliminazione (CRUD) in del modello Time Series.

* **Tipo di modello serie temporale**: È possibile utilizzare i tipi di Time Series Insights consente di definire le variabili o formule per l'esecuzione di calcoli. Perché sono associate a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia di modelli serie temporale**: le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza di modello serie temporale**: Le istanze sono le serie temporali stesse. Nella maggior parte dei casi, sono le **DeviceID** oppure **AssetID**, che è l'identificatore univoco dell'asset nell'ambiente.

Per altre informazioni sul Modello serie temporale, vedere [Modelli serie temporale](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Visualizzare in anteprima anche

La finestra Visualizza i campi di istanza e altri metadati associati alle istanze selezionate di Time Series Insights. Selezionando le caselle di controllo a destra, è possibile nascondere o visualizzare le istanze specifiche del grafico corrente. È anche possibile rimuovere gli elementi di dati specifici dai dati correnti anche selezionando il rosso **eliminare** (Cestino) controllo sul lato sinistro dell'elemento.

  [![L'anteprima ben](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Riconfigurare il layout delle **Analyze** grafico pagina, selezionare l'icona di puntini di sospensione nell'angolo superiore destro:

  [![Opzioni di layout di dati di telemetria](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se viene visualizzato il messaggio seguente, l'istanza non contiene tutti i dati durante l'intervallo di tempo selezionato. Per risolvere il problema, aumentare l'intervallo di tempo o verificare che l'istanza è il push dei dati.
>
> ![Nessuna notifica di dati](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Grafico di anteprima

Con il grafico, è possibile visualizzare le istanze di Time Series Insights sotto forma di linee. È possibile comprimere il pannello di ambiente, modello di data e ora del Pannello di controllo span selezionando i controlli web per ingrandire il grafico.

  [![Panoramica dell'anteprima del grafico](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **L'intervallo di date selezionato**: controlla quali elementi dati sono disponibili per la visualizzazione.

- **Scorrimento intervallo di date interna**: Usare due endpoint controlla trascinandoli nell'intervallo di tempo desiderato.

- **Controllo di compressione dell'intervallo di tempo**: comprime ed espande l'editor del pannello dell'intervallo di tempo.

- **Controllo di formattazione dell'asse Y**: Consente di scorrere le opzioni di visualizzazione dell'asse y disponibili:

    * `Default`: Ogni riga dispone di un asse y singoli.
    * `Stacked`: Usarlo per stack più righe sull'asse y stesso, con l'asse y vengono modificati dati in base alla riga selezionata.
    * `Shared`: Tutti i dati dell'asse y visualizzati insieme.

- **Elemento dati corrente**: l'elemento dati attualmente selezionato e i dettagli associati.

Eseguire il drill-ulteriormente in una sezione di dati specifico, fare clic di un punto dati nel grafico corrente e quindi trascinare nell'area selezionata per l'endpoint di propria scelta. L'area grigia selezionato e scegliere **Zoom**, come illustrato nell'immagine seguente:

  [![Zoom del grafico di anteprima](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Dopo aver eseguito il **Zoom** azione, viene visualizzato i dati selezionati set. Selezionare il controllo del formato dell'asse y per scorrere le rappresentazioni di asse y tre dei dati di Time Series Insights.

  [![Asse y del grafico di anteprima](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

In questo caso, è possibile vedere un esempio di assi Y condivisi:

  [![Anteprima condivisi gli assi Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Pannello editor di data e ora

Quando si usa Anteprima di Time Series Insights, prima di tutto si seleziona un intervallo di tempo. L'intervallo di tempo selezionato controlla il set di dati che è disponibile per la modifica con il widget Series Insights un'anteprima. I controlli web seguenti sono disponibili in anteprima in fase di Series Insights per selezionare l'intervallo di tempo di lavoro:

  [![Pannello di selezione di data e ora](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Scorrimento intervallo di date interna**: Usare due endpoint controlla trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interna è vincolato dal controllo dispositivo di scorrimento intervallo di date esterno.

1. **Pulsanti per aumentare o diminuire l'intervallo di date**: aumentare o diminuire il periodo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

1. **Controllo di compressione dell'intervallo di tempo**: Questo controllo web consente di nascondere tutti i controlli tranne lo strumento di dispositivo di scorrimento intervallo di date interna.

1. **Controllo dispositivo di scorrimento intervallo di date outer**: Usare i controlli di endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo di intervallo di date interna.

1. **Elenco a discesa intervallo di date tempi rapidi**: Usarlo per passare rapidamente tra le selezioni di span tempo preimpostato, ad esempio l'ultima **30 minuti**, il **ultime 12 ore**, o un **intervallo personalizzato**. Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

1. **Strumento dispositivo di scorrimento delle dimensioni dell'intervallo**: Usarlo per ingrandire o ridurre gli intervalli di stesso intervallo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. Visualizza le tendenze uniformi fino a intervalli con dimensioni di un millisecondo. È possibile utilizzarlo per vedere tagli ad alta risoluzione, granulari dei dati. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, che bilancia risoluzione, velocità della query e granularità.

1. **Controllo web per e-da-intervallo di date**: Questo controllo web, è possibile selezionare facilmente gli intervalli di data e ora desiderati. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo aver apportato le modifiche da applicare all'area di lavoro corrente, selezionare **salvare**.

   [![Al e dal Pannello di selezione](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Pannello di navigazione

Il pannello di navigazione di un'anteprima Insights serie viene visualizzata nella parte superiore dell'app Time Series Insights. Fornisce le funzionalità seguenti.

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  [![Icona di condivisione](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selezionare nuovo **condividere** icona per condividere un collegamento all'URL con il tuo team.

  [![Condividere l'URL dell'istanza](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sezione tenant

  [![Selezione di tenant](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Visualizza le informazioni di account di accesso corrente di Time Series Insights.
* Usato per passare tra temi disponibili Time Series Insights.
* Usarlo per visualizzare l'anteprima [app web demo](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Selezione tema

Per selezionare un nuovo tema, selezionare l'icona del profilo nell'angolo superiore destro. Quindi, selezionare **modificare il tema**.

  [![Selezione del tema](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Selezione della lingua è disponibile anche selezionando l'icona del profilo.

Anteprima di Azure Time Series Insights supporta due temi:

* **Tema chiaro**: tema predefinito visualizzato in questo documento.
* **Tema scuro**: esegue il rendering dello strumento di esplorazione, come mostrato di seguito:

  [![Selezionato con tema scuro](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="preview-terms-panel"></a>Pannello termini anteprima

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. Si potrebbe voler usare il prodotto generalmente disponibile e anteprima insieme. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2. 

Invece della gerarchia, vengono visualizzati il Panello termini di Time Series Insights, in cui le query vengono definite nell'ambiente in uso. Usarlo per filtrare i dati in base a un predicato.

  [![Pannello in cui eseguire una query](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Where**: Usare where clausola per filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operandi supportati includono:

| Operazione | Tipi supportati   | Note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | String | Sono consentiti solo valori letterali di stringa costante sul lato destro. Non sono consentiti valori NULL o stringhe vuote. |

Per altre informazioni sulle operazioni di query supportati e tipi di dati, vedere [ora serie espressione (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Gli esempi in cui le clausole

  [![In cui gli esempi di clausola](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Misura**: Un elenco di riepilogo a discesa che consente di visualizzare tutte le colonne numeriche (**doppi**) è possibile usare come elementi per il grafico corrente.

**Dividi per**: Questo elenco Visualizza tutte le disponibili categoriche colonne (stringhe) nel modello che è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini da visualizzare sull'asse x. Immettere i parametri desiderato e quindi selezionare **Add** per aggiungere un nuovo termine.

  [![Visualizzazione filtrata e sottoposti a query uno](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

È possibile visualizzare e nascondere gli elementi del pannello del grafico, selezionando l'icona visibile, come illustrato nell'immagine seguente. Per rimuovere completamente le query, selezionare il colore rosso **X**.

  [![Sottoposto a query e filtrato visualizza due](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Scopri [in ingresso e in archiviazione](./time-series-insights-update-storage-ingress.md) nell'anteprima di Insights di Azure ora serie.
- Continuare a leggere il documento di anteprima in fase Series Insights [modellazione dei dati](./time-series-insights-update-tsm.md).
- Scopri [come diagnosticare e risolvere i problemi](./time-series-insights-update-how-to-troubleshoot.md) l'istanza di Time Series Insights.
