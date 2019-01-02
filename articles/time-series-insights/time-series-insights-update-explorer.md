---
title: Strumento di esplorazione di Anteprima di Azure Time Series Insights - Visualizzare dati nello strumento di esplorazione di Anteprima di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive le funzionalità e le opzioni disponibili nell'app Web dello strumento di esplorazione di Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 5372a36291ee13966d497bdae83a6e214dce99b9
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272338"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizzare i dati nella versione di anteprima dello strumento di esplorazione

Questo articolo descrive le funzionalità e le opzioni disponibili nell'[app Web dello strumento di esplorazione](https://insights.timeseries.azure.com/preview/samples) di Anteprima di Azure Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare lo strumento di esplorazione di Anteprima di Azure Time Series Insights, è necessario:

* Avere un ambiente Time Series Insights configurato. Per altre informazioni, vedere [Esercitazione: Anteprima di Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* Fornire l'accesso ai dati all'ambiente Time Series Insights creato per l'account. È possibile fornire l'accesso a se stessi e ad altri utenti.
* Aggiungere un'origine evento all'ambiente Time Series Insights per eseguire il push dei dati nell'ambiente.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Informazioni sullo strumento di esplorazione di Anteprima di Azure Time Series Insights

  ![explorer-one][1]

Lo strumento di esplorazione di Anteprima di Azure Time Series Insights è costituito dagli elementi seguenti:

* **Barra di navigazione**: consente di passare dalle pagine di analisi a quelle dei modelli.
* **Albero gerarchico**: consente di selezionare elementi dati specifici con cui creare un grafico.
* **Area delle serie temporali**: visualizza gli elementi dati attualmente selezionati.
* **Pannello del grafico**: visualizza il grafico di lavoro corrente.
* **Sequenza temporale**: consente di modificare l'intervallo di tempo di lavoro.
* **Barra delle app**: contiene le opzioni di gestione degli utenti, ad esempio il tenant corrente, e consente di modificare le impostazioni relative a tema e lingua.

## <a name="time-series-insights-preview-environment-panel"></a>Pannello degli ambienti Anteprima di Time Series Insights

Il panello degli ambienti visualizza tutti gli ambienti Time Series Insights a cui si ha accesso. L'elenco include l'anteprima degli ambienti con pagamento in base al consumo e gli ambienti S1/S2 (disponibili a livello generale). È sufficiente fare clic sull'ambiente Time Series Insights che si vuole usare.

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Menu di spostamento di Anteprima di Time Series Insights

  ![explorer-three][3]

Con il menu di spostamento, è possibile passare da un'app Time Series Insights all'altra:

* **Analyze** (Analizza): consente di creare grafici e di eseguire un'analisi avanzata dei dati delle serie temporali modellati o non modellati.

* **Model** (Modella): consente di eseguire il push di nuovi tipi, gerarchie e istanze di Anteprima di Time Series Insights nel modello di Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>Creazione di modelli di Anteprima di Time Series Insights

Con questa app, è possibile eseguire operazioni di creazione, lettura, aggiornamento ed eliminazione sul modello serie temporale.  

* **Tipo di modello serie temporale**: i tipi di Time Series Insights consentono di definire variabili o formule per eseguire calcoli. Sono associati a una determinata istanza di Time Series Insights. Un tipo può avere una o più variabili.
* **Gerarchia di modelli serie temporale**: le gerarchie sono organizzazioni sistematiche dei dati. Le gerarchie rappresentano le relazioni tra entità diverse nei dati di Time Series Insights.
* **Istanza di modello serie temporale**: le istanze sono le serie temporali stesse. Nella maggior parte dei casi, sono l'elemento DeviceID o AssetID, ovvero l'identificatore univoco dell'asset nell'ambiente.

Per altre informazioni sul Modello serie temporale, vedere [Modelli serie temporale](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Pannello di ricerca di modelli di Anteprima di Time Series Insights

Il pannello di ricerca dei modelli consente di eseguire facilmente la ricerca e l'esplorazione nella gerarchia di Modello serie temporale per trovare le specifiche istanze di serie temporale da visualizzare nel grafico. Quando le istanze sono selezionate, vengono aggiunte sia al grafico corrente che all'area dati.

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>Area di Anteprima di Time Series Insights

L'area visualizza i campi delle istanze e altri metadati associati alle istanze delle serie temporali selezionate. Le caselle di controllo sulla destra consentono di nascondere o visualizzare istanze specifiche nel grafico corrente. È anche possibile rimuovere elementi dati specifici dall'area dati corrente facendo clic sul controllo rosso x a destra dell'elemento.

  ![explorer-five][5]

È anche possibile disancorare il pannello della telemetria per avere una migliore visualizzazione verticale degli elementi nell'area dati.

  ![explorer-six][6]

> [!NOTE]
> Se viene visualizzato il messaggio seguente, non sono disponibili dati dell'istanza per l'intervallo di tempo selezionato. Per risolvere il problema, è possibile estendere l'intervallo di tempo o verificare che l'istanza stia eseguendo il push dei dati.
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>Grafico di Anteprima di Time Series Insights

Con il grafico, è possibile visualizzare le istanze delle serie temporali come linee. È possibile comprimere il pannello dell'ambiente, il modello di dati e il pannello di controllo dell'intervallo di tempo facendo clic sui controlli Web per ingrandire il grafico.

  ![explorer-eight][8]

1. **Intervallo di date selezionato**: controlla quali elementi dati sono disponibili per la visualizzazione.

1. **Strumento dispositivo di scorrimento dell'intervallo di date interno**: usare i due controlli endpoint trascinandoli sull'intervallo di tempo desiderato.

1. **Controllo di compressione dell'intervallo di tempo**: comprime ed espande l'editor del pannello dell'intervallo di tempo.

1. **Controllo di formattazione dell'asse Y**: scorre le opzioni di visualizzazione dell'asse Y disponibili:

    * `Default`: ogni linea ha una singola asse Y.
    * `Stacked`: consente di distribuire con spaziatura più linee sullo stesso asse Y, dati del quale cambiano a seconda della linea selezionata.
    * `Shared`: i dati dell'asse Y vengono visualizzati tutti insieme.

1. **Elemento dati corrente**: l'elemento dati attualmente selezionato e i dettagli associati.

È possibile esaminare più in dettaglio una sezione dati specifica facendo clic su un punto dati nel grafico corrente e quindi trascinando l'area selezionata sull'endpoint scelto. Fare clic con il pulsante destro del mouse sull'area grigia selezionata e scegliere Zoom, come illustrato nell'immagine seguente:

  ![explorer-nine][9]

Dopo aver eseguito l'azione di zoom, verrà visualizzato il set di dati selezionato. Fare clic sul controllo di formattazione dell'asse Y per scorrere le tre rappresentazioni dell'asse Y dei dati di Time Series Insights.

  ![explorer-ten][10]

Ecco un esempio di assi Y condivisi:

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Pannello dell'editor di intervallo di Anteprima di Time Series Insights

Quando si usa Anteprima di Time Series Insights, prima di tutto si seleziona un intervallo di tempo. L'intervallo di tempo selezionato controlla il set di dati disponibile per la modifica con i widget di Anteprima di Time Series Insights. In Anteprima di Time Series Insights sono disponibili i controlli Web seguenti per selezionare l'intervallo di tempo di lavoro.

  ![explorer-twelve][12]

1. **Strumento dispositivo di scorrimento dell'intervallo di date interno**: usare i due controlli endpoint trascinandoli sull'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal dispositivo di scorrimento dell'intervallo di date esterno.

1. **Pulsanti per aumentare o diminuire l'intervallo di date**: aumentare o diminuire il periodo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

1. **Controllo di compressione dell'intervallo di tempo**: questo controllo Web consente di nascondere tutti i controlli tranne lo strumento dispositivo di scorrimento dell'intervallo di date interno.

1. **Dispositivo di scorrimento dell'intervallo di date esterno**: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

1. **Elenco a discesa degli intervalli di date Tempi rapidi**: consente di passare rapidamente da una selezione di intervallo di tempo preimpostato a un'altra, ad esempio gli ultimi 30 minuti, le ultime 12 ore o un intervallo personalizzato. Modificando questo valore, vengono modificati anche gli intervalli disponibili illustrati per lo strumento dispositivo di scorrimento delle dimensioni dell'intervallo.

1. **Strumento dispositivo di scorrimento delle dimensioni dell'intervallo**: consente di ingrandire e ridurre gli intervalli nell'arco dello stesso periodo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. Visualizza tendenze uniformi in sezioni temporali riferite a millisecondi, consentendo di mostrare sezioni di dati con granularità e risoluzione superiori. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, che bilancia risoluzione, velocità della query e granularità.

1. **Controllo Web A e Da per l'intervallo di date**: con questo controllo Web è possibile fare clic e selezionare facilmente gli intervalli di tempo e di date desiderati. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo aver apportato le modifiche, per applicare l'area di lavoro corrente, selezionare **Salva**.

  ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Pannello di navigazione di Anteprima di Time Series Insights

Il pannello di navigazione di Anteprima di Time Series Insights fornisce le funzionalità seguenti:

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>Controllo collegamento della condivisione sessione corrente

  ![explorer-fifteen][15]

Selezionare il controllo Web del collegamento (evidenziato) per generare un URL per salvare o condividere la sessione di lavoro di Azure Time Series Insights corrente, che include:

* Intervallo di tempo attualmente selezionato
* Dimensioni dell'intervallo attualmente selezionato
* Area data attualmente selezionata

### <a name="tenant-section"></a>Sezione tenant

  ![explorer-sixteen][16]

* Visualizza le informazioni sull'account di accesso di Time Series Insights corrente.
* Consente di passare da un tema di Time Series Insights disponibile a un altro.

### <a name="theme-selection"></a>Selezione tema

Anteprima di Azure Time Series Insights supporta due temi:

* **Tema chiaro**: tema predefinito visualizzato in questo documento.
* **Tema scuro**:  esegue il rendering dello strumento di esplorazione, come mostrato di seguito:

  ![explorer-seventeen][17]

È anche possibile scegliere tra le lingue supportate.

## <a name="s1s2-environment-controls"></a>Controlli dell'ambiente S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Pannello delle condizioni di Anteprima di Time Series Insights

Questa sezione si applica solo agli ambienti S1/S2 esistenti che provano a usare lo strumento di esplorazione nell'interfaccia utente aggiornata. Potrebbe essere necessario usare il prodotto disponibile a livello generale e l'anteprima in combinazione. Allo strumento di esplorazione aggiornato sono state aggiunte alcune funzionalità dell'interfaccia utente esistente, ma in Time Series Insights Explorer esistente è possibile ottenere l'esperienza dell'interfaccia utente completa per l'ambiente S1/S2.  

Invece della gerarchia, verrà visualizzato il pannello delle condizioni di Time Series Insights, dove si definiscono le query nell'ambiente e che consente di filtrare i dati in base a un predicato.

  ![explorer-eighteen][18]

Il pannello dell'editor delle condizioni di Anteprima di Time Series Insights accetta i parametri seguenti:

**Where**: la clausola where consente di filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando un operando, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operandi supportati includono:

| Operazione | Tipi supportati   | Note |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL. |
| `HAS` | string | Sono consentiti solo valori letterali di tipo stringa costante sul lato destro. Non sono consentiti valori NULL o stringhe vuote. |

Per altre informazioni sulle operazioni di query e sui tipi di dati supportati, vedere [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Esempi di clausole Where

  ![explorer-nineteen][19]

**Misura**: in questo elenco a discesa sono visualizzate tutte le colonne numeriche (valori di tipo **Double**) che è possibile usare come elementi per il grafico corrente.

**Dividi per**: in questo elenco a discesa sono visualizzate tutte le colonne delle categorie (valori di tipo String) del modello in base a cui è possibile raggruppare i dati. È possibile aggiungere fino a cinque termini da visualizzare sull'asse X. Immettere i parametri desiderati e quindi selezionare **Aggiungi** per aggiungere una nuova condizione.

  ![explorer-twenty][20]

È possibile visualizzare e nascondere gli elementi nel pannello del grafico selezionando l'icona visibile, come illustrato nell'immagine seguente. È possibile rimuovere completamente le query facendo clic sulla **x** rossa.

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:
* [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)
* [Modellazione di dati](./time-series-insights-update-tsm.md)
* [Diagnosi e risoluzione dei problemi](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
