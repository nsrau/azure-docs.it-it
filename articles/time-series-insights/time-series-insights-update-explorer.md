---
title: Visualizzazione dei dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive le funzionalità e le opzioni disponibili nell'app Web dello strumento di esplorazione di Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442092"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizzare i dati nella versione di anteprima dello strumento di esplorazione

Questo documento descrive la funzionalità dell'interfaccia utente ed esperienza utente e l'interfaccia dell'anteprima di Insights di Azure ora serie [app web demo](https://insights.timeseries.azure.com/preview/demo). In particolare, viene illustrato il layout dell'hosting di esempio, le opzioni di personalizzazione dell'interfaccia e navigazione attraverso la dimostrazione fornita.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare Esplora la versione di anteprima di Azure ora Series Insights, è necessario:

* Avere un ambiente Time Series Insights configurato. Per altre informazioni sul provisioning di un'istanza di prova nostri [versione di anteprima di Azure ora Series Insights](./time-series-insights-update-create-environment.md) esercitazione.
* [Fornire l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights creata per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine evento all'ambiente Time Series Insights ai dati di push per l'ambiente:
  * Informazioni su [come connettersi a un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Informazioni su [come connettersi a un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>Informazioni su Esplora l'anteprima

Lo strumento di esplorazione di Anteprima di Azure Time Series Insights è costituito dagli elementi seguenti:

[![La vista di esplorazione](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Pannello di ambiente**</a>: Consente di visualizzare gli ambienti Azure Time Series Insights.
1. <a href="#navigation-menu">**Menu di navigazione**</a>: Consente di alternare le **Analyze** e **modello** pagine.
1. <a href="#hierarchy-tree">**Struttura ad albero gerarchica**</a>: Consente di selezionare il modello specifico e gli elementi di dati da tracciare.
1. <a href="#preview-well">**Time series bene**</a>: Visualizza gli elementi di dati attualmente selezionata nel formato di tabella con codifica a colori.
1. <a href="#preview-chart">**Pannello del grafico**</a>:  visualizza il grafico di lavoro corrente.
1. <a href="#time-editor-panel">**Timeline**</a>:  consente di modificare l'intervallo di tempo di lavoro.
1. <a href="#navigation-panel">**App bar**</a>:  contiene le opzioni di gestione degli utenti, ad esempio il tenant corrente, e consente di modificare le impostazioni relative a tema e lingua.

## <a name="environment-dropdown"></a>Elenco a discesa Environment

L'elenco a discesa environment consente di visualizzare tutti gli ambienti Time Series Insights che è possibile utilizzare. L'elenco include gli ambienti con pagamento a consumo (anteprima) e ambienti S1 o S2 (disponibilità generale o GA). 

1. È sufficiente fare clic sulla freccia accanto al proprio ambiente visualizzato:

   [![Il pannello di controllo](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Selezionare quindi l'ambiente desiderato.

## <a name="navigation-menu"></a>Menu di navigazione

  [![Menu di navigazione](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Menu di navigazione consente di selezionare tra due viste:

* **Analyze** (Analizza): consente di creare grafici e di eseguire un'analisi avanzata dei dati delle serie temporali modellati o non modellati.
* **Model** (Modella): consente di eseguire il push di nuovi tipi, gerarchie e istanze di Anteprima di Time Series Insights nel modello di Time Series Insights.

## <a name="hierarchy-tree"></a>albero gerarchico

La gerarchia ad albero Visualizza gli elementi di dati selezionati tra cui modelli specifici dispositivi e sensori dei dispositivi.

### <a name="model-search-panel"></a>Pannello ricerca con modello

Il pannello di ricerca dei modelli consente di eseguire facilmente la ricerca e l'esplorazione nella gerarchia di Modello serie temporale per trovare le specifiche istanze di serie temporale da visualizzare nel grafico. Quando le istanze sono selezionate, vengono aggiunte sia al grafico corrente che all'area dati.

  [![Il pannello di ricerca del modello](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Creazione di modelli

L'anteprima di Azure ora Series Insights supporta le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) complete in del modello Time Series.  

* **Tipo di modello serie temporale**: i tipi di Time Series Insights consentono di definire variabili o formule per eseguire calcoli. Sono associati a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia di modelli serie temporale**: le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza di modello serie temporale**: Le istanze sono le serie temporali stesse. Nella maggior parte dei casi, sono le **DeviceID** oppure **AssetID**, che è l'identificatore univoco dell'asset nell'ambiente.

Per altre informazioni sul Modello serie temporale, vedere [Modelli serie temporale](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Visualizzare in anteprima anche

La finestra Visualizza i campi di istanza e altri metadati associati alle istanze selezionate di Time Series Insights. Le caselle di controllo sul lato destro consentono di nascondere o visualizzare le istanze specifiche del grafico corrente. È inoltre possibile rimuovere gli elementi di dati specifici dai tuoi dati correnti facendo il red **eliminare** (Cestino) controllo sul lato sinistro dell'elemento.

  [![L'anteprima ben](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

È anche possibile riconfigurare il layout delle **Analyze** pagina selezionando l'icona di puntini di sospensione in alto a destra del grafico:

  [![Opzioni di layout di dati di telemetria](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se viene visualizzato il messaggio seguente, non sono disponibili dati dell'istanza per l'intervallo di tempo selezionato. Per risolvere il problema, è possibile estendere l'intervallo di tempo o verificare che l'istanza stia eseguendo il push dei dati.
>
> ![Nessuna notifica di dati](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Grafico di anteprima

Con il grafico, è possibile visualizzare le istanze di Time Series Insights sotto forma di linee. È possibile comprimere il pannello dell'ambiente, il modello di dati e il pannello di controllo dell'intervallo di tempo facendo clic sui controlli Web per ingrandire il grafico.

  [![Panoramica dell'anteprima del grafico](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **Intervallo di date selezionato**: controlla quali elementi dati sono disponibili per la visualizzazione.

1. **Strumento dispositivo di scorrimento dell'intervallo di date interno**: usare i due controlli endpoint trascinandoli sull'intervallo di tempo desiderato.

1. **Controllo di compressione dell'intervallo di tempo**: comprime ed espande l'editor del pannello dell'intervallo di tempo.

1. **Controllo di formattazione dell'asse Y**: scorre le opzioni di visualizzazione dell'asse Y disponibili:

    * `Default`: ogni linea ha una singola asse Y.
    * `Stacked`: consente di distribuire con spaziatura più linee sullo stesso asse Y, dati del quale cambiano a seconda della linea selezionata.
    * `Shared`: i dati dell'asse Y vengono visualizzati tutti insieme.

1. **Elemento dati corrente**: l'elemento dati attualmente selezionato e i dettagli associati.

È possibile esaminare più in dettaglio una sezione dati specifica facendo clic su un punto dati nel grafico corrente e quindi trascinando l'area selezionata sull'endpoint scelto. Visualizzare l'area disabilitato, selezionato, quindi scegliere **Zoom** come illustrato nell'immagine seguente:

  [![Zoom del grafico di anteprima](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Dopo aver eseguito il **Zoom** azione, verrà visualizzato il set di dati selezionato. Fare clic sul controllo di formattazione dell'asse Y per scorrere le tre rappresentazioni dell'asse Y dei dati di Time Series Insights.

  [![Asse y del grafico di anteprima](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Ecco un esempio di assi Y condivisi:

  [![Anteprima condivise dell'asse y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Pannello editor di data e ora

Quando si usa Anteprima di Time Series Insights, prima di tutto si seleziona un intervallo di tempo. L'intervallo di tempo selezionato controlla il set di dati disponibile per la modifica con i widget di Anteprima di Time Series Insights. In Anteprima di Time Series Insights sono disponibili i controlli Web seguenti per selezionare l'intervallo di tempo di lavoro.

  [![Pannello di selezione data e ora](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Strumento dispositivo di scorrimento dell'intervallo di date interno**: usare i due controlli endpoint trascinandoli sull'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal dispositivo di scorrimento dell'intervallo di date esterno.

1. **Pulsanti per aumentare o diminuire l'intervallo di date**: aumentare o diminuire il periodo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

1. **Controllo di compressione dell'intervallo di tempo**: questo controllo Web consente di nascondere tutti i controlli tranne lo strumento dispositivo di scorrimento dell'intervallo di date interno.

1. **Dispositivo di scorrimento dell'intervallo di date esterno**: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

1. **Tempi rapidi-intervallo di date discesa**: Consente passare rapidamente tra le selezioni di span tempo preimpostato, ad esempio l'ultima **30 minuti**, il **ultime 12 ore**, o un **intervallo personalizzato**. Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

1. **Strumento dispositivo di scorrimento delle dimensioni dell'intervallo**: consente di ingrandire e ridurre gli intervalli nell'arco dello stesso periodo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. Visualizza tendenze uniformi in sezioni temporali riferite a millisecondi, consentendo di mostrare sezioni di dati con granularità e risoluzione superiori. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, che bilancia risoluzione, velocità della query e granularità.

1. **Controllo Web A e Da per l'intervallo di date**: Questo controllo web, è facilmente possibile fare clic e selezionare la data desiderata e gli intervalli di tempo. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo aver apportato le modifiche, per applicare l'area di lavoro corrente, selezionare **Salva**.

   [![Al e dal Pannello di selezione](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Pannello di navigazione

Il pannello di navigazione di un'anteprima Insights serie viene visualizzata nella parte superiore dell'app Time Series Insights. Fornisce le funzionalità seguenti.

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  [![Icona di condivisione](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selezionare nuovo **condividere** icona per condividere un collegamento all'URL con il tuo team.

  [![Condividere l'URL dell'istanza](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sezione tenant

  [![Selezione di tenant](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Visualizza le informazioni sull'account di accesso di Time Series Insights corrente.
* Consente di passare da un tema di Time Series Insights disponibile a un altro.
* Consente di visualizzare l'anteprima [app web demo](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Selezione tema

Per selezionare un nuovo tema, fare clic sull'icona del profilo nell'angolo superiore destro. Quindi, selezionare **modificare il tema**.

  [![Selezione del tema](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Selezione della lingua è disponibile anche facendo clic sull'icona del profilo.

Anteprima di Azure Time Series Insights supporta due temi:

* **Tema chiaro**: tema predefinito visualizzato in questo documento.
* **Tema scuro**:  esegue il rendering dello strumento di esplorazione, come mostrato di seguito:

  [![Selezionato con tema scuro](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="preview-terms-panel"></a>Pannello termini anteprima

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. Potrebbe essere necessario usare il prodotto disponibile a livello generale e l'anteprima in combinazione. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2.  

Invece della gerarchia, verrà visualizzato il pannello delle condizioni di Time Series Insights, dove si definiscono le query nell'ambiente e che consente di filtrare i dati in base a un predicato.

  [![Pannello in cui eseguire una query](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Where**: la clausola where consente di filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operandi supportati includono:

| Operazione | Tipi supportati   | Note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | String | Sono consentiti solo valori letterali di tipo stringa costante sul lato destro. Non sono consentiti valori NULL o stringhe vuote. |

Per altre informazioni sulle operazioni di query e sui tipi di dati supportati, vedere [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Esempi di clausole Where

  [![In cui gli esempi di clausola](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Misura**: Un elenco a discesa che consente di visualizzare tutte le colonne numeriche (**doppi**) è possibile usare come elementi per il grafico corrente.

**Dividi per**: in questo elenco a discesa sono visualizzate tutte le colonne delle categorie (valori di tipo String) del modello in base a cui è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini da visualizzare sull'asse X. Immettere i parametri desiderati e quindi selezionare **Aggiungi** per aggiungere una nuova condizione.

  [![Visualizzazione filtrata e sottoposti a query uno](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

È possibile visualizzare e nascondere gli elementi nel pannello del grafico selezionando l'icona visibile, come illustrato nell'immagine seguente. È possibile rimuovere completamente le query, fare clic su rossa **X**.

  [![Sottoposto a query e filtrato visualizza due](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Scopri [in ingresso e in archiviazione](./time-series-insights-update-storage-ingress.md) nell'anteprima di Insights di Azure ora serie.

- Continuare a leggere il documento di anteprima in fase Series Insights [modellazione dei dati](./time-series-insights-update-tsm.md).

- Scopri [come diagnosticare e risolvere i problemi](./time-series-insights-update-how-to-troubleshoot.md) l'istanza di Time Series Insights.
