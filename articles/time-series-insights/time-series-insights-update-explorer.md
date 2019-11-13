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
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: f09de6fee4c9de39d98ec66714686ab1e805c4cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014874"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizzare i dati nella versione di anteprima dello strumento di esplorazione

Questo documento descrive le funzionalità dell'interfaccia utente e dell'esperienza utente e l'interfaccia dell' [app Web demo](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview. In particolare, viene illustrato il layout dell'esempio ospitato, le opzioni di personalizzazione dell'interfaccia e la navigazione attraverso la demo fornita.

## <a name="prerequisites"></a>prerequisiti

Per iniziare a usare Esplora Azure Time Series Insights Preview, è necessario:

* Avere un ambiente Time Series Insights configurato. Per ulteriori informazioni sul provisioning di un'istanza, provare l'esercitazione [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Fornire l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights creato per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine evento all'ambiente Time Series Insights per eseguire il push dei dati nell'ambiente:
  * Informazioni [su come connettersi a un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Informazioni [su come connettersi a un hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet.

## <a name="learn-about-the-preview-explorer"></a>Informazioni su Esplora anteprime

Lo strumento di esplorazione di Anteprima di Azure Time Series Insights è costituito dagli elementi seguenti:

[![la visualizzazione di esplorazione](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Pannello ambiente</a>: Visualizza gli ambienti di Azure Time Series Insights.
- <a href="#navigation-menu">Menu di navigazione</a>: utilizzarlo per spostarsi tra le pagine **analizza** e **modello** .
- <a href="#hierarchy-tree">Albero gerarchico</a>: consente di selezionare elementi di dati e modelli specifici per la creazione di grafici.
- <a href="#preview-well">Time Series well</a>: Visualizza gli elementi dati attualmente selezionati in formato tabella con codifica a colori.
- <a href="#preview-chart">Pannello grafico</a>: Visualizza il grafico di lavoro corrente.
- <a href="#time-editor-panel">Sequenza temporale</a>: usarla per modificare l'intervallo di tempo di lavoro.
- <a href="#navigation-panel">Barra dell'app</a>: contiene le opzioni di gestione degli utenti, ad esempio il tenant corrente. È possibile usarlo per modificare le impostazioni del tema e della lingua.

## <a name="environment-drop-down-list"></a>Elenco a discesa ambiente

Nell'elenco a discesa ambiente vengono visualizzati tutti gli ambienti Time Series Insights a cui si ha accesso. L'elenco include gli ambienti con pagamento in base al consumo, ad esempio l'anteprima Time Series Insights. L'elenco include anche gli ambienti S1/S2, disponibili a livello generale.

1. Selezionare la freccia a discesa accanto all'ambiente visualizzato.

   [![il pannello di controllo](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Quindi selezionare l'ambiente desiderato.

## <a name="navigation-menu"></a>Menu di navigazione

  [![il menu di navigazione](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Usare il menu di navigazione per scegliere tra due visualizzazioni:

* **Analizza**: è possibile usare questa funzionalità per progettare ed eseguire analisi avanzate sui dati di serie temporali modellati o non modellati.
* **Modello**: usarlo per eseguire il push di nuovi tipi di Time Series Insights di anteprima, gerarchie e istanze nel modello di Time Series Insights.

## <a name="hierarchy-tree"></a>Albero gerarchia

Nell'albero gerarchia vengono visualizzati gli elementi dati selezionati che includono modelli, dispositivi specifici e sensori nei dispositivi.

### <a name="model-search-panel"></a>Pannello di ricerca modelli

È possibile utilizzare il pannello di ricerca del modello per eseguire facilmente ricerche e spostarsi nella gerarchia dei modelli Time Series per individuare le istanze di serie temporali specifiche che si desidera visualizzare nel grafico. Dopo aver selezionato le istanze, queste vengono aggiunte sia al grafico corrente sia all'area dati.

  [![il pannello di ricerca del modello](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Creazione di modelli

Il Azure Time Series Insights anteprima supporta operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) complete sul modello Time Series.

* **Tipo di modello Time Series**: è possibile usare i tipi di Time Series Insights per definire le variabili o le formule per l'esecuzione di calcoli. Esse sono associate a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia del modello Time Series**: le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza del modello Time Series**: le istanze sono le serie temporali. Nella maggior parte dei casi sono **DeviceID** o **AssetID**, che è l'identificatore univoco dell'asset nell'ambiente.

Per altre informazioni sul Modello serie temporale, vedere [Modelli serie temporale](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Anteprima ottima

Il pozzetto Visualizza i campi dell'istanza e altri metadati associati alle istanze di Time Series Insights selezionate. Selezionando le caselle di controllo sul lato destro, è possibile nascondere o visualizzare istanze specifiche del grafico corrente. È anche possibile rimuovere elementi di dati specifici dall'area dati correnti selezionando il controllo rosso **Delete** (Cestino) sul lato sinistro dell'elemento.

  [![l'anteprima](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Per riconfigurare il layout della pagina **analizza** grafico, selezionare l'icona con i puntini di sospensione nell'angolo in alto a destra:

  [opzioni di layout ![telemetria](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se viene visualizzato il messaggio seguente, l'istanza non contiene dati durante l'intervallo di tempo selezionato. Per risolvere il problema, aumentare l'intervallo di tempo o verificare che l'istanza stia eseguendo il push dei dati.
>
> ![Nessuna notifica sui dati](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Grafico di anteprima

Con il grafico è possibile visualizzare le istanze di Time Series Insights come righe. È possibile comprimere il pannello ambiente, il modello di dati e il pannello di controllo intervallo di tempo selezionando i controlli Web per rendere il grafico più grande.

  [Panoramica del grafico di anteprima ![](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Intervallo di date selezionato**: controlla gli elementi dati disponibili per la visualizzazione.

- **Strumento di scorrimento intervallo di date interno**: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato.

- **Intervallo di tempo compresso controllo**: comprime ed espande l'editor del pannello dell'intervallo di tempo.

- **Controllo del formato dell'asse y**: scorre le opzioni di visualizzazione asse y disponibili:

    * `Default`: ogni riga ha un singolo asse y.
    * `Stacked`: utilizzarlo per eseguire lo stack di più righe sullo stesso asse y, con la modifica dei dati dell'asse y in base alla riga selezionata.
    * `Shared`: tutti i dati dell'asse y visualizzati insieme.

- **Elemento dati corrente**: l'elemento dati attualmente selezionato e i relativi dettagli associati.

Per eseguire il drill-down di una sezione di dati specifica, fare clic su un punto dati nel grafico corrente, quindi trascinare l'area selezionata nell'endpoint desiderato. Fare clic con il pulsante destro del mouse sull'area grigia selezionata e selezionare **Zoom**, come illustrato nella figura seguente:

  [zoom del grafico ![anteprima](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Dopo aver eseguito l'azione di **Zoom** , viene visualizzato il set di dati selezionato. Consente di selezionare il controllo del formato dell'asse y per scorrere le tre rappresentazioni dell'asse y dei dati Time Series Insights.

  [![l'asse y del grafico di anteprima](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Qui è possibile vedere un esempio di assi Y condivisi:

  [![anteprima assi Y condivisi](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Pannello Editor temporale

Quando si usa Anteprima di Time Series Insights, prima di tutto si seleziona un intervallo di tempo. L'intervallo di tempo selezionato controlla il set di dati disponibile per la manipolazione con i widget Time Series Insights Preview. I controlli Web seguenti sono disponibili in Time Series Insights anteprima per la selezione dell'intervallo di tempo di lavoro:

  [![Pannello di selezione di data e ora](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Strumento di scorrimento intervallo di date interno**: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal controllo dispositivo di scorrimento dell'intervallo di date esterno.

1. **Aumentare e ridurre i pulsanti di intervallo di date**: aumentare o ridurre l'intervallo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

1. **Controllo di compressione intervallo di tempo**: questo controllo Web consente di nascondere tutti i controlli ad eccezione dello strumento dispositivo di scorrimento intervallo di date interno.

1. **Controllo dispositivo di scorrimento intervallo di date esterno**: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

1. **Elenco a discesa tempi rapidi**per l'intervallo di tempo: usarlo per passare rapidamente da una selezione a un intervallo di tempo preimpostato, ad esempio gli ultimi **30 minuti**, le **ultime 12 ore**o un **intervallo personalizzato**. Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

1. **Strumento di scorrimento Dimensioni intervallo**: usarlo per ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. Consente di visualizzare le tendenze smussate fino a sezioni fino a un millisecondo. È possibile usarlo per visualizzare i tagli granulari e ad alta risoluzione dei dati. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, che bilancia risoluzione, velocità della query e granularità.

1. **Intervallo di date da e verso il controllo Web**: con questo controllo Web è possibile selezionare facilmente gli intervalli di data e ora desiderati. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo avere apportato le modifiche da applicare all'area di lavoro corrente, selezionare **Salva**.

   [![da e verso il pannello di selezione](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Pannello di navigazione

Il pannello di navigazione Time Series Insights anteprima viene visualizzato nella parte superiore dell'app Time Series Insights. Fornisce le funzionalità seguenti:

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  [icona di condivisione ![](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selezionare l'icona nuova **condivisione** per condividere un collegamento URL con il team.

  [![condividere l'URL dell'istanza](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sezione tenant

  [selezione del tenant ![](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

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

  [![tema scuro selezionato](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="preview-terms-panel"></a>Pannello termini anteprima

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. Potrebbe essere necessario usare il prodotto e l'anteprima disponibili a livello generale in combinazione. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2. 

Al posto della gerarchia, viene visualizzato il pannello Time Series Insights termini, in cui si definiscono le query nell'ambiente in uso. Usarlo per filtrare i dati in base a un predicato.

  [![dove pannello query](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Where**: utilizzare la clausola WHERE per filtrare rapidamente gli eventi utilizzando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operando supportati sono i seguenti:

| Operazione | Tipi supportati   | note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | String | Sul lato destro sono consentiti solo valori letterali stringa costanti. Non sono consentite stringhe vuote e NULL. |

Per ulteriori informazioni sulle operazioni di query e sui tipi di dati supportati, vedere l'articolo relativo all' [espressione Time Series (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Esempi di clausole WHERE

  [esempi di clausole WHERE ![](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Measure**: elenco a discesa che consente di visualizzare tutte le colonne numeriche (**Double**) che è possibile utilizzare come elementi per il grafico corrente.

**Split by**: questo elenco a discesa consente di visualizzare tutte le colonne categoriche (stringhe) disponibili nel modello in cui è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini per visualizzare sullo stesso asse x. Immettere i parametri desiderati e quindi selezionare **Aggiungi** per aggiungere un nuovo termine.

  [![sottoposto a query e filtrato una vista](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

È possibile mostrare e nascondere gli elementi nel pannello del grafico selezionando l'icona visibile, come illustrato nella figura seguente. Per rimuovere completamente le query, selezionare la **X**rossa.

  [![sottoposto a query e filtrato-visualizzazione due](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [archiviazione e](./time-series-insights-update-storage-ingress.md) il traffico in ingresso nella Azure Time Series Insights Preview.
- Leggere il documento Time Series Insights anteprima sulla [modellazione dei dati](./time-series-insights-update-tsm.md).
- Informazioni [su come diagnosticare e risolvere i problemi relativi all'istanza di](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights.
