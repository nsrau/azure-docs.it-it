---
title: Cartelle di lavoro di monitoraggio di Azure con parametri personalizzati
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: mbullwin
ms.openlocfilehash: 33da3cd8a72bb4d93011c348db65c5b4d9e687ed
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461464"
---
# <a name="interactive-workbooks"></a>Cartelle di lavoro interattive

Le cartelle di lavoro consentono agli autori di creare report interattivi ed esperienze per i consumer. L'interattività è supportata in diversi modi.

## <a name="parameter-changes"></a>Modifiche ai parametri

Quando un utente di una cartella di lavoro aggiorna un parametro, qualsiasi controllo che utilizza il parametro viene aggiornato e ridisegnato automaticamente per riflettere il nuovo stato. Questo è il modo in cui la maggior parte dei report portale di Azure supporta l'interattività. Le cartelle di lavoro forniscono questa operazione in modo semplice e con il minimo sforzo dell'utente.

Altre informazioni sui [parametri nelle cartelle di lavoro](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Griglia, riquadro, selezioni di grafici

Le cartelle di lavoro consentono agli autori di creare scenari in cui fare clic su una riga in una griglia per aggiornare i grafici successivi in base al contenuto della riga.

Ad esempio, un utente può avere una griglia che mostra un elenco di richieste e alcune statistiche come i conteggi degli errori. Potrebbero configurarlo in modo che facendo clic su una riga corrispondente a una richiesta venga visualizzato un grafico dettagliato sotto l'aggiornamento per filtrare solo la richiesta.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Impostazione dell'interattività sulla riga della griglia fare clic

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Usare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come _log_, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query`per visualizzare i risultati
6. Selezionare l'icona _delle impostazioni avanzate_ nel piè di pagina della query (l'icona ha l'aspetto di un ingranaggio). Verrà visualizzato il riquadro Impostazioni avanzate.
7. Controllare l'impostazione: `When an item is selected, export a parameter` .
8. Sotto l'impostazione selezionata selezionare *Aggiungi parametro* e compilarlo con le informazioni riportate di seguito.
    1. Campo da esportare:`Request`
    2. Nome parametro:`SelectedRequest`
    3. Valore predefinito: `All requests`
9. Selezionare Salva

    ![Screenshot che illustra l'editor avanzato con le impostazioni per l'esportazione dei campi come parametri.](./media/workbooks-interactive/export-parameters-add.png)

10. Selezionare `Done Editing`.
11. Aggiungere un altro controllo query usando i passaggi 2 e 3.
12. Utilizzare l'editor di query per immettere il KQL per l'analisi.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query`per visualizzare i risultati.
14. Modificare la _visualizzazione_ in `Area chart` .
15. Scegliere una riga da selezionare nella prima griglia. Si noti che il grafico ad area seguente Filtra la richiesta selezionata.

Il report risultante ha un aspetto simile al seguente in modalità di modifica:

![Screenshot delle prime due query in modalità di modifica.](./media/workbooks-interactive//interactivity-grid-create.png)

Nell'immagine seguente viene illustrato un report interattivo più elaborato in modalità di lettura in base agli stessi principi. Il report USA i clic della griglia per esportare i parametri, che a sua volta viene usato in due grafici e in un blocco di testo.

![Screenshot di un report utilizzando i clic della griglia in modalità lettura.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Esportazione del contenuto di un'intera riga

A volte è preferibile esportare l'intero contenuto della riga selezionata anziché solo una colonna specifica. In questi casi, lasciare la proprietà annullata `Field to export` nel passaggio 7,1 precedente. Le cartelle di lavoro esporteranno l'intero contenuto della riga come JSON nel parametro.

Nel controllo KQL di riferimento usare la `todynamic` funzione per analizzare il codice JSON e accedere alle singole colonne.

## <a name="grid-cell-clicks"></a>Clic sulla cella della griglia

Le cartelle di lavoro consentono agli autori di aggiungere interattività tramite un tipo speciale di renderer della colonna Grid denominato `link renderer` . Un renderer di collegamenti converte una cella della griglia in un collegamento ipertestuale in base al contenuto della cella. Le cartelle di lavoro supportano molti tipi di renderer di collegamenti, inclusi quelli che consentono l'apertura di pannelli di panoramica delle risorse, visualizzatori del contenitore delle proprietà, ricerca di App Insights, utilizzo, traccia delle transazioni e così via.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Impostazione dell'interattività tramite i clic della cella della griglia

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Usare il collegamento _Aggiungi query_ per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come _log_, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query`per visualizzare i risultati
6. Selezionare _Impostazioni colonna_ per aprire il riquadro Impostazioni.
7. Nella sezione _Columns_ impostare:
    1. _Esempio_ -renderer della colonna: `Link` , visualizzazione da aprire: `Cell Details` , etichetta collegamento:`Sample`
    2. Renderer della colonna _count_ : `Bar` , tavolozza dei colori: `Blue` , valore minimo:`0`
    3. Renderer della colonna _Request_ :`Automatic`
    4. Selezionare _Salva e Chiudi_ per applicare le modifiche

    ![Screenshot della scheda dell'impostazione della colonna.](./media/workbooks-interactive/column-settings.png)

8. Fare clic su uno dei `Sample` collegamenti nella griglia. Verrà aperto un riquadro con i dettagli di una richiesta campionata.

    ![Screenshot del riquadro dei dettagli della richiesta campionata.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Azioni renderer di collegamento

| Azione di collegamento | Azione su clic |
|:------------- |:-------------|
| `Generic Details` | Mostra i valori di riga in una scheda di contesto della griglia delle proprietà |
| `Cell Details` | Mostra il valore della cella in una scheda di contesto della griglia delle proprietà. utile quando la cella contiene un tipo dinamico con informazioni, ad esempio JSON con proprietà della richiesta come la posizione, l'istanza del ruolo e così via. |
| `Cell Details` | Mostra il valore della cella in una scheda di contesto della griglia delle proprietà. utile quando la cella contiene un tipo dinamico con informazioni, ad esempio JSON con proprietà della richiesta come la posizione, l'istanza del ruolo e così via. |
| `Custom Event Details` | Apre i dettagli della ricerca di Application Insights con l'ID evento personalizzato ( `itemId` ) nella cella |
| `* Details` | Simile ai dettagli dell'evento personalizzato, ad eccezione delle dipendenze, delle eccezioni, delle visualizzazioni di pagina, delle richieste e delle tracce. |
| `Custom Event User Flows` | Apre il Application Insights Flussi utente esperienza trasformata tramite pivot per il nome dell'evento personalizzato nella cella |
| `* User Flows` | Simile a Flussi utente eventi personalizzati ad eccezione delle eccezioni, delle visualizzazioni pagina e delle richieste |
| `User Timeline` | Apre la sequenza temporale dell'utente con l'ID utente (user_Id) nella cella |
| `Session Timeline` | Apre l'esperienza di ricerca Application Insights per il valore nella cella, ad esempio ricercare il testo "ABC", dove ABC è il valore nella cella. |
| `Resource overview` | Aprire la panoramica della risorsa nel portale in base al valore di ID risorsa nella cella |

## <a name="conditional-visibility"></a>Visibilità condizionale

La cartella di lavoro consente agli utenti di far apparire o scomparire determinati controlli in base ai valori dei parametri. In questo modo gli autori possono avere un aspetto diverso in base all'input dell'utente o allo stato di telemetria. Un esempio mostra agli utenti solo un riepilogo quando le cose sono valide, ma visualizzano i dettagli completi quando si verifica un errore.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Impostazione dell'interattività tramite visibilità condizionale

1. Per configurare due controlli interattivi, attenersi alla procedura descritta nella sezione [impostazione dell'interattività nella riga della griglia](#setting-up-interactivity-on-grid-row-click) .
2. Aggiungere un nuovo parametro nella parte superiore:
    1. Nome: `ShowDetails`
    2. Tipo di parametro:`Drop down`
    3. Obbligatorio:`checked`
    4. Ottenere i dati da:`JSON`
    5. Input JSON:`["Yes", "No"]`
    6. Salva per eseguire il commit delle modifiche.

    ![Dopo aver selezionato il pulsante Aggiungi parametro, viene visualizzato il riquadro Modifica parametro.](./media/workbooks-interactive/edit-parameter.png)

3. Impostare il valore del parametro su`Yes`

    ![Sopra il pulsante modifica completato è l'elenco a discesa che consente di impostare il valore del parametro](./media/workbooks-interactive/set-parameter.png)

4. Nel controllo query con il grafico ad area selezionare l'icona _Impostazioni avanzate_ (icona a forma di ingranaggio)
5. Controllare l'impostazione`Make this item conditionally visible`
    1. Questo elemento è visibile se il `ShowDetails` valore `equals` del parametro`Yes`
6. Selezionare _modifica completato_ per confermare le modifiche.
7. Selezionare _modifica eseguita_ sulla barra degli strumenti della cartella di lavoro per attivare la modalità di lettura.
8. Impostare il valore del parametro `ShowDetails` su `No` . Si noti che il grafico seguente scompare.

L'immagine seguente mostra il caso visibile in cui `ShowDetails` è`Yes`

![Screenshot che mostra la visibilità condizionale in cui il grafico è visibile](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

L'immagine seguente mostra il caso nascosto in cui `ShowDetails` è`No`

![Screenshot che mostra la visibilità condizionale in cui il grafico è nascosto](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interattività con selezioni multiple in griglie e grafici

I passaggi della query e della metrica possono anche esportare uno o più parametri quando viene selezionata una riga o più righe.

![Screenshot che mostra le impostazioni dei parametri di esportazione con più parametri. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. Nel passaggio della query che visualizza la griglia passare alle impostazioni avanzate.
2. Selezionare la `When items are selected, export parameters` casella di controllo. Verranno visualizzati altri controlli.
3. Selezionare la `allow selection of multiple values` casella di controllo.
    1. La visualizzazione visualizzata consentirà l'utilizzo di più selezioni e i valori del parametro esportato saranno matrici di valori, ad esempio quando si usano i parametri a discesa con selezione più.
    2. Se l'opzione è deselezionata, la visualizzazione della visualizzazione rispetterà solo l'ultimo elemento selezionato. Esportazione di un singolo valore alla volta.
4. Per ogni parametro che si vuole esportare, usare il pulsante *Aggiungi parametro* . Verrà visualizzata una finestra popup contenente le impostazioni per il parametro da esportare.

Quando è abilitata la selezione singola, l'autore può specificare il campo dei dati originali da esportare. I campi includono il nome del parametro, il tipo di parametro e il valore predefinito da utilizzare se non è selezionato alcun elemento (facoltativo).

Quando è abilitata l'opzione multiselezione, l'autore specifica il campo dei dati originali da esportare. I campi includono il nome del parametro, il tipo di parametro, le virgolette con e il delimitatore. Quando si trasformano i valori delle frecce in testo quando vengono sostituiti in una query, vengono utilizzati i valori di virgoletta con e delimitatore. In MultiSelect se non è selezionato alcun valore, il valore predefinito è una matrice vuota.

> [!NOTE]
> Per la selezione a più livelli, verranno esportati solo valori univoci. i valori della matrice di output, ad esempio "1, 1, 2, 1", verranno visualizzati come valori di output.

È possibile lasciare vuota l'impostazione "campo da esportare" nelle impostazioni di esportazione. In tal caso, tutti i campi disponibili nei dati verranno esportati come oggetto JSON file di coppie chiave: valore. Per le griglie e i titoli, questi saranno tutti i campi nella griglia. Per i grafici, i campi disponibili saranno x, y, serie ed etichetta (a seconda del tipo di grafico).

Mentre il comportamento predefinito prevede l'esportazione di un parametro come testo, se si sa che il campo è una sottoscrizione o un ID di risorsa, usarlo come tipo di parametro Export. Questo consentirà al parametro di essere usato a valle in posizioni che richiedono tali tipi di parametri.

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
